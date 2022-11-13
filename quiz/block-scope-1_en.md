## TL;DR

`let` and `const` in `for` creates new scope for each iteration, which means the `i` in `console.log()` points to different values.

```js
for (let i = 0; i < 5; i++) {
  setTimeout(() => console.log(i), 0)
}
// 0
// 1
// 2
// 3
// 4
```

This behavior doesn't apply to `var`, meaning the `i` points to same gloabl scope. Because of `setTimeout()`, `console.log()` is called after `for` loop is done, so `i` is always 5.

```js
for (var i = 0; i < 5; i++) {
  setTimeout(() => console.log(i), 0)
}
// 5
// 5
// 5
// 5
// 5
```

Below is detailed explanation. It is pretty complex, hang on!

## An simple example of closure

Let's look at code below.

```js
const a = 1

function func() {
  const a = 2
  return () => console.log(a)
}

console.log(a) // 1
func()() // 2
```

The answer is obvious, `func()` returns a closure in which `a` is bound to 2.
Same identifier `a` somehow refers to different values based on where `a` is, have you wondered how it is done?

The answers are in ECMAScript specs.

## Evaluation - to get the value of identifier

From [ECMAScript spec](https://tc39.es/ecma262/#sec-identifiers-runtime-semantics-evaluation),
the value of an identifier is determined by `ResolveBinding(StringValue of Identifier).`

## ResolveBinding ( _name_ [ , _env_ ] )

> 1. If _env_ is not present or if _env_ is **undefined**, then
>    1. Set _env_ to the running execution context's **LexicalEnvironment**.
> 2. Assert: _env_ is an Environment Record.
> 3. If the source text matched by the syntactic production that is being evaluated is contained in strict mode code, let _strict_ be true; else let _strict_ be false.
> 4. Return ? GetIdentifierReference(env, name, strict).

Two things are important here.

1. The **LexicalEnvironment** on **running execution context** is where to resolve the binding.
2. it returns not the value directly, but some reference.

## GetIdentifierReference ( _env_, _name_, _strict_ )

From [ECMAScript spec](https://tc39.es/ecma262/#sec-getidentifierreference), below is how to resolve the bindings recursively.

1. If _env_ is the value **null**, then
   1. Return the Reference Record { [[Base]]: unresolvable, [[ReferencedName]]: name, [[Strict]]: strict, [[ThisValue]]: empty }.
2. Let _exists_ be ? env.HasBinding(name).
3. If _exists_ is true, then
   1. Return the Reference Record { [[Base]]: env, [[ReferencedName]]: name, [[Strict]]: strict, [[ThisValue]]: empty }.
4. Else,
   1. Let _outer_ be _env_.[[OuterEnv]].
   2. Return ? GetIdentifierReference(_outer_, name, strict).

So we can see clearly that

1. if identifier is not on the **LexicalEnvironment**, then it goes to its `[[OuterEnv]]` recursly, just like the prototype chain we are familiar with.
2. it returns a Reference Record.

## Environment Record

From [ECMAScript spec](https://tc39.es/ecma262/#sec-environment-records)

> Environment Record is a specification type used to define the association of Identifiers to specific variables and functions, based upon the lexical nesting structure of ECMAScript code. Usually an Environment Record is associated with some specific syntactic structure of ECMAScript code such as a FunctionDeclaration, a BlockStatement, or a Catch clause of a TryStatement. Each time such code is evaluated, a new Environment Record is created to record the identifier bindings that are created by that code.

Usually we talk about _scope_ in JavaScript, so Environment Record is just the internal implementation.

> Every Environment Record has an [[OuterEnv]] field, which is either null or a reference to an outer Environment Record. This is used to model the logical nesting of Environment Record values.

So we can see now why we are able to access variable outside of function.

> Environment Record is abstract class, it has 3 sub classess
>
> 1. A _Declarative Environment Record_ is used to define the effect of ECMAScript language syntactic elements such as _FunctionDeclarations_, _VariableDeclarations_, and Catch clauses that directly associate identifier bindings with ECMAScript language values.
> 2. An _Object Environment Record_ is used to define the effect of ECMAScript elements such as WithStatement that associate identifier bindings with the properties of some object.
> 3. A _Global Environment Record_ is used for Script global declarations. It does not have an outer environment; its [[OuterEnv]] is null.

So what call globe scope is the _Global Environment Record_, and scope is _Declarative Environment Record_. The `with` is not used commonly and indeed it has strange behavior which needs different kind of scope.

## What happens to the assignment ?

```js
const a = 1
```

What happens to above statement? According to [ECMAScript spec](https://tc39.es/ecma262/#sec-let-and-const-declarations-runtime-semantics-evaluation), it calles `InitializeBinding` on Environment Record.

> 1. Assert: IsUnresolvableReference(V) is false.
> 2. Let base be V.[[Base]].
> 3. Assert: base is an Environment Record.
> 4. Return ? base.InitializeBinding(V.[[ReferencedName]], W).

## How Environment Record is created in function declaration ?

```js
// global Environment Record
function func() {
  // inner Environment Record
  // [[OuterEnv]] is global Environment Record
}
```

Let's see how inner Environment Record is created. It is defined in [ECMAScript spec](https://tc39.es/ecma262/#sec-runtime-semantics-instantiateordinaryfunctionobject)

> FunctionDeclaration : function BindingIdentifier ( FormalParameters ) { FunctionBody }

> 1. Let _name_ be StringValue of BindingIdentifier.
> 2. Let _sourceText_ be the source text matched by FunctionDeclaration.
> 3. Let _F_ be OrdinaryFunctionCreate(%Function.prototype%, sourceText, FormalParameters, FunctionBody, non-lexical-this, _env_, privateEnv).
> 4. Perform SetFunctionName(_F_, name).
> 5. Perform MakeConstructor(_F_).
> 6. Return _F_.

This is the steps to create a new Function object, notice that `OrdinaryFunctionCreate()` takes `env` as parameter, it will be set as internal field `[[Environment]]` of `F`. So each function has its own `[[Environment]]` when created.

The inner scope won't be created until the function is executed, which is the internal method `[[Call]]` of function object as defined in [ECMAScript spec](https://tc39.es/ecma262/#sec-ecmascript-function-objects-call-thisargument-argumentslist)

## [[Call]] ( thisArgument, argumentsList )

> 1. Let _callerContext_ be the running execution context.
> 2. Let _calleeContext_ be PrepareForOrdinaryCall(F, undefined).
> 3. Assert: _calleeContext_ is now the running execution context.
> 4. If F.[[IsClassConstructor]] is true, then
>    1. Let error be a newly created TypeError object.
>    2. NOTE: error is created in _calleeContext_ with F's associated Realm Record.
>    3. Remove _calleeContext_ from the execution context stack and restore _callerContext_ as the running execution context.
>    4. Return ThrowCompletion(error).
> 5. Perform OrdinaryCallBindThis(F, _calleeContext_, thisArgument).
> 6. Let result be Completion(OrdinaryCallEvaluateBody(F, argumentsList)).
> 7. Remove _calleeContext_ from the execution context stack and restore _callerContext_ as the running execution context.
> 8. If result.[[Type]] is return, return result.[[Value]].
> 9. ReturnIfAbrupt(result).
> 10. Return undefined.

In step 2 a new calleeContext is created by PrepareForOrdinaryCall()

## PrepareForOrdinaryCall ( F, newTarget )

From [ECMAScript Spec](https://tc39.es/ecma262/#sec-prepareforordinarycall)

> 1. Let _callerContext_ be the running execution context.
> 2. Let _calleeContext_ be a new ECMAScript code execution context.
> 3. Set the Function of _calleeContext_ to F.
> 4. Let calleeRealm be F.[[Realm]].
> 5. Set the Realm of _calleeContext_ to calleeRealm.
> 6. Set the ScriptOrModule of _calleeContext_ to F.[[ScriptOrModule]].
> 7. Let localEnv be NewFunctionEnvironment(F, newTarget).
> 8. Set the LexicalEnvironment of _calleeContext_ to localEnv.
> 9. Set the VariableEnvironment of _calleeContext_ to localEnv.
> 10. Set the PrivateEnvironment of _calleeContext_ to F.[[PrivateEnvironment]].
> 11. If _callerContext_ is not already suspended, suspend _callerContext_.
> 12. Push _calleeContext_ onto the execution context stack; _calleeContext_ is now the running execution context.
> 13. NOTE: Any exception objects produced after this point are associated with calleeRealm.
> 14. Return _calleeContext_.

In this part we can see that

1. a new Code Execution context is created and pushed on top of the stack, working as running execution context.
2. a new Function Environment Record (localEnv) is created by `NewFunctionEnvironment`. The Environment Record works as both `LexicalEnvironment` ( let/const) and `VariableEnvironment` (var) of the newly created context.

The context is popped in the logic of `[[Call]]` when call is done.

## NewFunctionEnvironment(F, newTarget)

> 1. Let env be a new Function Environment Record containing no bindings.
> 2. Set env.[[FunctionObject]] to F.
> 3. If F.[[ThisMode]] is lexical, set env.[[ThisBindingStatus]] to lexical.
> 4. Else, set env.[[ThisBindingStatus]] to uninitialized.
> 5. Set env.[[NewTarget]] to newTarget.
> 6. Set env.[[OuterEnv]] to F.[[Environment]].
> 7. Return env.

In the creation of new Function Environment Record, we can see the `env.[[OuterEnv]]` is set to function's `[[Environment]], which makes closure possible.

## Difference between `let` and `var` in for loop.

According to [ECMAScript spec](https://tc39.es/ecma262/#sec-runtime-semantics-forloopevaluation), `let/const` is handled differently comparing to `var`.

For let and const, it works as below.

> ForStatement : for ( LexicalDeclaration Expressionopt ; Expressionopt ) Statement

> 1. Let _oldEnv_ be the running execution context's LexicalEnvironment.
> 2. Let _loopEnv_ be NewDeclarativeEnvironment(_oldEnv_).
> 3. Let isConst be IsConstantDeclaration of LexicalDeclaration.
> 4. Let boundNames be the BoundNames of LexicalDeclaration.
> 5. For each element dn of boundNames, do
>    1. If isConst is true, then
>       1. Perform ! _loopEnv_.CreateImmutableBinding(dn, true).
>    2. Else,
>       1. Perform ! _loopEnv_.CreateMutableBinding(dn, false).
> 6. Set the running execution context's LexicalEnvironment to _loopEnv_.
> 7. Let forDcl be Completion(Evaluation of LexicalDeclaration).
> 8. If forDcl is an abrupt completion, then
>    1. Set the running execution context's LexicalEnvironment to _oldEnv_.
>    2. Return ? forDcl.
> 9. If isConst is false, let _perIterationLets_ be boundNames; otherwise let _perIterationLets_ be a new empty List.
> 10. If the first Expression is present, let test be the first Expression; otherwise, let test be empty.
> 11. If the second Expression is present, let increment be the second Expression; otherwise, let increment be empty.
> 12. Let bodyResult be Completion(ForBodyEvaluation(test, increment, Statement, _perIterationLets_, labelSet)).
> 13. Set the running execution context's LexicalEnvironment to _oldEnv_
> 14. Return ? bodyResult.

In step 2, a new Environment Record (**loopEnv**) is created and it is set to LexicalEnvironment of current running
execution context to bind the lexical declarations

While for var it is much simpler, there is no new environment created.

> ForStatement : for ( var VariableDeclarationList ; Expressionopt ; Expressionopt ) Statement
>
> 1. Perform ? Evaluation of VariableDeclarationList.
> 2. If the first Expression is present, let _test_ be the first Expression; otherwise, let test be empty.
> 3. If the second Expression is present, let _increment_ be the second Expression; otherwise, let _increment_ be empty.
> 4. Return ? ForBodyEvaluation(_test_, _increment_, Statement, « », labelSet).

Also notice that even though `ForBodyEvaluation()` is called in both let/const and var, the _perIterationLets_ is empty for var.

`ForBodyEvaluation()` is the true part that runs each iteration. [ECMAScript Spec](https://tc39.es/ecma262/#sec-forbodyevaluation).

> 1. Let V be undefined.
> 2. Perform ? CreatePerIterationEnvironment(perIterationBindings).
> 3. Repeat,
>    1. If _test_ is not empty, then
>       1. Let _testRef_ be ? Evaluation of _test_.
>       2. Let _testValue_ be ? GetValue(_testRef_).
>       3. If ToBoolean(_testValue_) is false, return V.
>    2. Let _result_ be Completion(Evaluation of stmt).
>    3. If LoopContinues(_result_, _labelSet_) is false, return ? UpdateEmpty(_result_, V).
>    4. If _result_.[[Value]] is not empty, set V to _result_.[[Value]].
>    5. Perform ? CreatePerIterationEnvironment(perIterationBindings).
>    6. If _increment_ is not empty, then
>       1. Let _incRef_ be ? Evaluation of _increment_.
>       2. Perform ? GetValue(_incRef_).

In step 2, we can see that a new Environment Record is created on each iteration in `CreatePerIterationEnvironment()`, and the bindings `i` are newly creating by copying the value.

> 1. If perIterationBindings has any elements, then
>    1. Let lastIterationEnv be the running execution context's LexicalEnvironment.
>    2. Let outer be lastIterationEnv.[[OuterEnv]].
>    3. Assert: outer is not null.
>    4. Let thisIterationEnv be NewDeclarativeEnvironment(outer).
>    5. For each element bn of perIterationBindings, do
>       1. Perform ! thisIterationEnv.CreateMutableBinding(bn, false).
>       2. Let lastValue be ? lastIterationEnv.GetBindingValue(bn, true).
>       3. Perform ! thisIterationEnv.InitializeBinding(bn, lastValue).
>       4. Set the running execution context's LexicalEnvironment to thisIterationEnv.
> 2. Return unused.

So though this is called also for var, but the parameter of perIterationBindings is empty for var, so there is no Environment record created.

## Summary in details

```js
// Global Environment Record {}
//  ↳  Declarative Environment Record { i } - loop env
for (let i = 0; i < 5; i++) {
  //      ↳ Declaritive Environment Record { i } - iteration env, different on each iteration
  setTimeout(() => {
    //           ↳ Function Environment Record
    console.log(i)
  }, 0)
}
```

Above is the environment records involed. Since iteration env is different each time. `i` in `console.log()` has different values on each call.

```js
// Global Environment Record {}
for (var i = 0; i < 5; i++) {
  setTimeout(() => {
    // ↳ Function Environment Record
    console.log(i)
  }, 0)
}
```

For var, there are no new Environment Record created, `i` points to the same Global Environment Record. Because of `setTimeout()`, `console.log()` are called with the same value of `i` which is 5.
