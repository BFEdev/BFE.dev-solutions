## TL;DR

Arrow function doesn't have its own `this`, so `this` inside of arrow functions is pointed to the `this` at outer scope, this is done at function creation time.

```js
const obj = {
  dev: 'bfe',
  a: function () {
    return this.dev
  }
}
console.log(obj.a())
// `'bfe'`, `this` points to `obj`

const obj = {
  dev: 'bfe',
  b() {
    return this.dev
  }
}
console.log(obj.b())
// `'bfe'`, shorthand of above

const obj = {
  dev: 'bfe',
  c: () => {
    return this.dev
  }
}

console.log(obj.c())
// undefined, arrow function doesn't have its own `this`
// `this` is pointed to outer scope which is global so undefined is logged

const obj = {
  dev: 'bfe',
  d: function () {
    return (() => {
      return this.dev
    })()
  }
}
console.log(obj.d())
// `'bfe'`
// arrow function doesn't have its own `this`, and `this` here points to its outer scope, which is the function. And this is determined at function creation time.
// when obj.d() is called, `this` points to obj, so arrow function inside has `this` points to `obj`.

const obj = {
  dev: 'bfe',
  b() {
    return this.dev
  },
  e: function () {
    return this.b()
  }
}
console.log(obj.e())
// `'bfe'`
// when `obje.e()` is called, its `this` is obj
// so for b, its `this` is also obj

const obj = {
  dev: 'bfe',
  b() {
    return this.dev
  },
  f: function () {
    return this.b
  }
}
console.log(obj.f()())
// undefined
// think about obj.f()() as someExpression(). obj.f() returns function b(), but there is no dot in someExpression, so `this` is global and undefined is logged

const obj = {
  dev: 'bfe',
  c: () => {
    return this.dev
  },
  g: function () {
    return this.c()
  }
}
console.log(obj.g())
// undefined
// in g(), `this` is obj
// but for c(), it is arrow function, it doesn't have its own `this`
// so it goes up to global and  undefined is logged

const obj = {
  dev: 'bfe',
  c: () => {
    return this.dev
  },
  h: function () {
    return this.c
  }
}
console.log(obj.h()())
// undefined
// obj.h() returns the arrow function, now the call expression is someExpression(), in which there is no dot
// also c() is arrow function, `this` points to global, so undefined is logged

const obj = {
  dev: 'bfe',
  i: function () {
    return () => {
      return this.dev
    }
  }
}

console.log(obj.i()())
// 'bfe'
// obj.i() returns the anonymous arrow function
// arrow function itself doesn't have `this`,
// but its outer scope, the anonymous function has `this` pointed to obj.
// so the result is 'bfe'
```

Below is detailed explanation, hang on

## CallMemberExpression

Let's first look at how below piece of code is evaluated.

```
obj.a()
```

This expression has two parts. `'obj.a'` and `'()'`.

`'obj.a'` is [MemberExpression](https://tc39.es/ecma262/#prod-MemberExpression) and `'()'` is [Arguments](https://tc39.es/ecma262/#prod-Arguments), as a whole it is [CallMemberExpression](https://tc39.es/ecma262/#prod-CallMemberExpression).

And here is the internal logic from [ECMAScript Spec](https://tc39.es/ecma262/#sec-function-calls-runtime-semantics-evaluation).

> 1. Let _expr_ be the CallMemberExpression that is covered by CoverCallExpressionAndAsyncArrowHead.
> 2. Let _memberExpr_ be the MemberExpression of _expr_.
> 3. Let _arguments_ be the Arguments of _expr_.
> 4. Let _ref_ be ? Evaluation of _memberExpr_.
> 5. Let _func_ be ? GetValue(_ref_).
> 6. If _ref_ is a Reference Record, IsPropertyReference(_ref_) is **false**, and _ref_.[[ReferencedName]] is "eval", then
>    1. If SameValue(_func_, %eval%) is true, then 1
>       1. Let _argList_ be ? ArgumentListEvaluation of arguments.
>       2. If _argList_ has no elements, return undefined.
>       3. Let _evalArg_ be the first element of argList.
>       4. If the source text matched by this CallExpression is strict mode code, let strictCaller be true. Otherwise let strictCaller be false.
>       5. Return ? **PerformEval**(evalArg, strictCaller, true).
> 7. Let _thisCall_ be this CallExpression.
> 8. Let _tailCall_ be IsInTailPosition(thisCall).
> 9. Return ? _EvaluateCall_(func, ref, arguments, tailCall).

We can see that what id does is

1. extract each parts, the MemberExpression and Arguments
2. Evaluate and get value of MemberExpression
3. get arguments from Arguments
4. forward it to EvaluateCall

## How Property accessor is evaluated?

Let's first take a look at how `obj.a` is evaluated.

From [ECMAScript spec](https://tc39.es/ecma262/#sec-property-accessors-runtime-semantics-evaluation), property accessor `MemberExpression . IdentifierName` is evaluated as blow.

> 1. Let baseReference be ? Evaluation of MemberExpression.
> 2. Let baseValue be ? GetValue(baseReference).
> 3. If the source text matched by this MemberExpression is strict mode code, let strict be true; else let strict be false.
> 4. Return EvaluatePropertyAccessWithIdentifierKey(baseValue, IdentifierName, strict).

It forwads to [EvaluatePropertyAccessWithIdentifierKey()](https://tc39.es/ecma262/#sec-evaluate-property-access-with-expression-key).

> 1. Let propertyNameReference be ? Evaluation of expression.
> 2. Let propertyNameValue be ? GetValue(propertyNameReference).
> 3. Let propertyKey be ? ToPropertyKey(propertyNameValue).
> 4. Return the Reference Record { [[Base]]: baseValue, [[ReferencedName]]: propertyKey, [[Strict]]: strict, [[ThisValue]]: empty }.

So `obj.a` in this question acutually returns a **Reference Record**, which is something like

```
{
  [[Base]]: obj,
  [[ReferencedName]]: 'a',
  [[Strict]]: false,
  [[ThisValue]]: empty
}
```

Now let's look at EvaluateCall

## EvaluateCall

According to the [ECMAScript spec](https://tc39.es/ecma262/#sec-evaluatecall).

> 1. If _ref_ is a Reference Record, then
>    1. If IsPropertyReference(ref) is true, then
>       1. Let _thisValue_ be **GetThisValue**(ref).
>    1. Else,
>       1. Let _refEnv_ be ref.[[Base]].
>       2. Assert: _refEnv_ is an Environment Record.
>       3. Let _thisValue_ be _refEnv_.WithBaseObject().
> 1. Else,
>    1. Let thisValue be undefined.
> 1. Let _argList_ be ? ArgumentListEvaluation of arguments.
> 1. If _func_ is not an Object, throw a TypeError exception.
> 1. If IsCallable(func) is false, throw a TypeError exception.
> 1. If _tailPosition_ is true, perform PrepareForTailCall().
> 1. Return ? Call(_func_, _thisValue_, argList).

We can clearly see in the first if branch, that the `[[Base]]` of Reference Record is used as `thisValue` thourhg [GetThisValue](https://tc39.es/ecma262/#sec-getthisvalue), which is `obj` in our case.

## How `this` is set for function call?

Now let's jump into the `()` - function call part.

From [here](https://tc39.es/ecma262/#sec-call), we see `F.[[Call]](V, argumentsList)` is used in above `Call(_func_, _thisValue_, argList)`, in which `V` means `this`.

And inside of [[[Call]] ( thisArgument, argumentsList )](https://tc39.es/ecma262/#sec-ecmascript-function-objects-call-thisargument-argumentslist), there is a step which **bind this to local scope**.

> OrdinaryCallBindThis ( F, calleeContext, thisArgument )
>
> 1. Let _thisMode_ be F.[[ThisMode]].
> 2. If _thisMode_ is lexical, return unused.
> 3. Let _calleeRealm_ be F.[[Realm]].
> 4. Let _localEnv_ be the LexicalEnvironment of calleeContext.
> 5. If _thisMode_ is strict, let _thisValue_ be thisArgument.
> 6. Else,
>    1. If thisArgument is undefined or null, then
>       1. Let globalEnv be _calleeRealm_.[[GlobalEnv]].
>       2. Assert: globalEnv is a Global Environment Record.
>       3. Let _thisValue_ be globalEnv.[[GlobalThisValue]].
>    2. Else,
>       1. Let _thisValue_ be ! ToObject(thisArgument).
>       2. NOTE: ToObject produces wrapper objects using _calleeRealm_.
> 7. Assert: _localEnv_ is a Function Environment Record.
> 8. Assert: The next step never returns an abrupt completion because _localEnv_.[[ThisBindingStatus]] is not initialized.
> 9. Perform ! _localEnv_.BindThisValue(_thisValue_).
> 10. Return unused.

In our case `thisArgument` is `obj` so local scope's `[[ThisValue]]` is set to `obj`.

Now it's time to look at function body

```js
return this.dev
```

## How `this` is evaluated ?

From [ECMAScript spec](https://tc39.es/ecma262/#sec-this-keyword), it is evaluated by ResolveThisBinding.

> 1. Let envRec be GetThisEnvironment().
> 2. Return ? envRec.GetThisBinding().

GetThisEnvironment() is very important, because we can see the recursive checks along the parent scopes.

> GetThisEnvironment ()
>
> 1. Let env be the running execution context's LexicalEnvironment.
> 2. Repeat,
>    1. Let exists be env.HasThisBinding().
>    2. If exists is true, return env.
>    3. Let outer be env.[[OuterEnv]].
>    4. Assert: outer is not null.
>    5. Set env to outer.

In step 2, we can see there is a loop that keeps checking the existence of `this` in `[[OuterEnv]]` which is what we commonly call outer scope .

So here is the full picture of the first case.

```js
const obj = {
  dev: 'bfe',
  a: function () {
    return this.dev
  }
}
console.log(obj.a())

// `obj.a` returns a reference record of {[[base]]: obj, [[ReferencedName]]: 'a'}
// () calls the function, it evaluates above reference record, gets `obj` as `this`, and set it as `[[ThisValue]]` of local scope
// `this` in function body tries recursively search for `[[ThisValue]]` along the lexical environment chain and instantly gets `obj`.
// `'bfe'` is logged.
```

## Method definition

In second case it is called [method definition](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Method_definitions), semantically different from property with a function.

```js
const obj = {
  dev: 'bfe',
  b() {
    return this.dev
  }
}
```

We can find the core part in [DefineMethodProperty](https://tc39.es/ecma262/#sec-definemethodproperty)

> 1. Assert: homeObject is an ordinary, extensible object with no non-configurable properties.
> 2. If key is a Private Name, then
>    1. Return PrivateElement { [[Key]]: key, [[Kind]]: method, [[Value]]: closure }.
> 3. Else,
>    1. Let desc be the PropertyDescriptor { [[Value]]: closure, [[Writable]]: true, [[Enumerable]]: enumerable, [[Configurable]]: true }.
>    2. Perform ! DefinePropertyOrThrow(homeObject, key, desc).
>    3. Return unused.

And in [DefinePropertyOrThrow](https://tc39.es/ecma262/#sec-definepropertyorthrow), internal method `[[DefineOwnProperty]]` on object is triggered.

Methods allows us to add `get` or `set` keyword in the front it has more capability that setting a function as normal property.

But in this question, it is the same as normal function property, so `'bfe'` is logged following above analysis.

```js
const obj = {
  e: function () {
    return this.b()
  }
}
console.log(obj.e())
```

For above case, `this.b` is the same as `obj.b()` so `'bfe'` is logged.

## Function object internally has `[[ThisMode]]` to indicate how to handle `this`.

According to [ECMAScript spec](https://tc39.es/ecma262/#table-internal-slots-of-ecmascript-function-objects), It has 3 values `lexical`, `strict` or `global`.

> `lexical` means that `this` refers to the `this` value of a lexically enclosing function.
> `strict` means that the `this` value is used exactly as provided by an invocation of the function.
> `global` means that a this value of `undefined` or `null` is interpreted as a reference to the `global` object, and any other this value is first passed to `ToObject`.

Guess you have noticed it, for arrow functions, this field should be `lexical` meaning `this` inside of arrow functions refers to `this` from outer scope.

## Arrow functions are created with `[[ThisMode]]` set to lexical and `this` arg won't be set

From [ECMAScript spec](https://tc39.es/ecma262/#sec-runtime-semantics-instantiatearrowfunctionexpression), we can clearly see that `lexical-this` is passed to OrdinaryFunctionCreate().

1. If name is not present, set name to "".
2. Let _env_ be the LexicalEnvironment of the running execution context.
3. Let _privateEnv_ be the running execution context's PrivateEnvironment.
4. Let _sourceText_ be the source text matched by ArrowFunction.
5. Let _closure_ be OrdinaryFunctionCreate(%Function.prototype%, _sourceText_, ArrowParameters, ConciseBody, **lexical-this**, env, privateEnv).
6. Perform SetFunctionName(_closure_, name).
7. Return _closure_.

And inside of [OrdinaryFunctionCreate](https://tc39.es/ecma262/#sec-ordinaryfunctioncreate), `[[ThisMode]]` is set to lexical at step 9.

When `obj.b()` is called, `this` arg is tried to set but is rejected by flag - `[[ThisMode]]` in [OrdinaryCallBindThis](https://tc39.es/ecma262/#sec-ordinarycallbindthis)

> OrdinaryCallBindThis ( F, calleeContext, thisArgument )
>
> 1. Let _thisMode_ be F.[[ThisMode]].
> 2. If _thisMode_ is lexical, return unused.
>    (omitted)

## `this` in arrow function goes to outer scope (lexical environment)

Going back to what we covered in [ResolveThisBinding](https://tc39.es/ecma262/#sec-this-keyword), since its own scope doesn't have `this` get set, its outer scopes are checked to find `this`.

Now the the cases with arrow functions are clear to us.

```js
const obj = {
  dev: 'bfe',
  c: () => {
    return this.dev
  }
  d: function() {
    return (() => {
      return this.dev
    })()
  },
  g: function() {
    return this.c()
  },
  h: function() {
    return this.c
  },
  i: function() {
    return () => {
      return this.dev
    }
  }
}

console.log(obj.c())
// undefined, since c itself has no `this` and it goes up to global scope which results in undefined

console.log(obj.d())

// 'bfe'
// the outer function has `this` set to `obj`. Though arrow function doesn't have `this`, it tries to get `this` though outer scope.

console.log(obj.g())
// undefined
// same as `obj.c()`

console.log(obj.h()())
// undefined
// same as above

console.log(obj.i()())
// 'bfe'
// same as above analysis
```

## `this` breaks if expression is not connected as property access.

Last case of `f` is a bit tricky

```js
const obj = {
  dev: 'bfe',
  b() {
    return this.dev
  },
  f: function () {
    return this.b
  }
}
console.log(obj.f()())
```

How we handle expression like `obj.f()()`? In [Call Expression](https://tc39.es/ecma262/#prod-CallExpression), it defines such case recursively.

```
CallExpression[Yield, Await] :
    CallExpression[?Yield, ?Await] Arguments[?Yield, ?Await]
```

In the [evaluation process](https://tc39.es/ecma262/#sec-function-calls-runtime-semantics-evaluation), we can see that the first CallExpression is first evaluated.

> 1. Let _ref_ be ? Evaluation of CallExpression.
> 2. Let _func_ be ? GetValue(ref).
> 3. Let thisCall be this CallExpression.
> 4. Let tailCall be IsInTailPosition(thisCall).
> 5. Return ? _EvaluateCall_(func, ref, Arguments, tailCall).

Notice that in step 2, `GeValue()` is used to get the function out, so `ref` is no longer a reference record which we cover at the beginning of this long explanation

And in [EvaluateCall](https://tc39.es/ecma262/#sec-evaluatecall), because `ref` is not reference record, `thisValue` is set to undefined. Again global scope is used as `this` in the body, so `undefined` is logged.
