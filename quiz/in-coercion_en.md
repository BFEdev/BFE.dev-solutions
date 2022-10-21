## TL;DR

`in` operator checks if a property name is present in the target object(including its prototype chain),
if the property name is not [Symbol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol) nor string, it is coerced to string.

`['foo']` is coerced to `'foo'`, so the result of both are `true`.

```
const obj = {
  foo: 'bar'
}

console.log('foo' in obj) // true
console.log(['foo'] in obj) // true
```

Below is longer version explaining the true logic behind this.

## `in` operator in ECMAScript spec

According to [the spec](https://tc39.es/ecma262/multipage/ecmascript-language-expressions.html#sec-relational-operators-runtime-semantics-evaluation), below is how `in` works.

> 1.  Let _lref_ be ? **Evaluation** of RelationalExpression.
> 2.  Let _lval_ be ? **GetValue**(_lref_).
> 3.  Let _rref_ be ? **Evaluation** of ShiftExpression.
> 4.  Let _rval_ be ? **GetValue**(_rref_).
> 5.  If _rval_ is not an Object, throw a TypeError exception.
> 6.  Return ? **HasProperty**(_rval_, ? **ToPropertyKey**(lval)).

We have 2 steps to run the check

1. ToPropertyKey - convert the propert name to a valid property key
2. HasProperty - check if the property exists

## ToPropertyKey

This is straightforward from [ECMAScript spec](https://tc39.es/ecma262/multipage/abstract-operations.html#sec-topropertykey).

> 1. Let _key_ be ? **ToPrimitive**(argument, string).
> 2. If _key_ is a Symbol, then
>    1. Return _key_.
> 3. Return ! **ToString**(key).

The property name is converted to [primitives](https://developer.mozilla.org/en-US/docs/Glossary/Primitive). If it is Symbol, use it, otherwise convert it to string.

## ToPrimitive() in ECMAScript Spec

This defines how we get primitive values from any data types, according to the [ECMAScript spec](https://tc39.es/ecma262/multipage/abstract-operations.html#sec-toprimitive):

> 1. If _input_ is an Object, then
> 1. Let _exoticToPrim_ be ? GetMethod(input, @@toPrimitive).
> 1. If _exoticToPrim_ is not undefined, then
>    1. If _preferredType_ is not present, let hint be "default".
>    2. Else if _preferredType_ is string, let hint be "string".
>    3. Else,
>       1. Assert: _preferredType_ is number.
>       2. Let hint be "number".
>    4. Let _result_ be ? Call(_exoticToPrim_, _input_, « _hint_ »).
>    5. If _result_ is not an Object, return _result_.
>    6. Throw a TypeError exception.
> 1. If _preferredType_ is not present, let _preferredType_ be number.
> 1. Return ? OrdinaryToPrimitive(_input_, _preferredType_).
> 1. Return _input_.

Here it says, if [Symbol.toPrimitive](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/toPrimitive) is defined in the object, then the method will be used in ToPrimitive(). This is pretty obvious, below is an example.

```js
const obj =
  {
    [Symbol.toPrimitive](hint) {
      if (hint === 'number') {
        return 100
      }
      return 'obj'
    }
  } + obj // 100
```

If [Symbol.toPrimitive](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/toPrimitive) is not defined, the default _OrdinaryToPrimitive()_ is used.

## OrdinaryToPrimitive() in ECMAScript spec

This is the default process before Symbol.toPrimitive is a thing. According to the [ECMAScript spec](https://tc39.es/ecma262/multipage/abstract-operations.html#sec-ordinarytoprimitive):

> 1. If hint is string, then
> 1. Let _methodNames_ be « "toString", "valueOf" ».
> 1. Else,
> 1. Let _methodNames_ be « "valueOf", "toString" ».
> 1. For each element _name_ of _methodNames_, do
> 1. Let _method_ be ? Get(O, name).
> 1. If IsCallable(_method_) is true, then
> 1. Let _result_ be ? Call(_method_, _O_).
> 1. If _result_ is not an Object, return _result_.
> 1. Throw a **TypeError** exception.

What it says is basically

1. if needs a string, first `toString()` then `valueOf()` is tried and return the first primitive value;
2. if needs a number, first `valueOf()` then `toString()` is tried and return the first primitive value;

## ToString

[The ECMAScript spec](https://tc39.es/ecma262/multipage/abstract-operations.html#sec-tostring) is simply a mapping table.

> | Argument Type | Result                                                                                                           |
> | ------------- | ---------------------------------------------------------------------------------------------------------------- |
> | Undefined     | Return `"undefined"`.                                                                                            |
> | Null          | Return `"null"`.                                                                                                 |
> | Boolean       | If argument is true, return `"true"`. If argument is false, return `"false"`.                                    |
> | Number        | Return Number::toString(argument, 10).                                                                           |
> | String        | Return argument.                                                                                                 |
> | Symbol        | Throw a TypeError exception.                                                                                     |
> | BigInt        | Return BigInt::toString(argument, 10).                                                                           |
> | Object        | Apply the following steps: 1. Let primValue be ? ToPrimitive(argument, string). 2. Return ? ToString(primValue). |

## HasProperty

[HasProperty](https://tc39.es/ecma262/multipage/abstract-operations.html#sec-hasproperty) is fairly simple

> The abstract operation HasProperty takes arguments O (an Object) and P (a property key) and returns either a normal completion containing a Boolean or a throw completion. It is used to determine whether an object has a property with the specified property key. The property may be either own or inherited. It performs the following steps when called: `Return ? O.[[HasProperty]](P).`

So it is native implementation and it checks the prototype chain.

## Step-by-step analysis.

```js
const obj = {
  foo: 'bar'
}

console.log('foo' in obj) // true
```

This is super easy, `'foo'` is string and in the object, so `true`.

```js
console.log(['foo'] in obj) // true
```

Internally following steps are how `['foo']` is converted to string

1. there is no [Symbol.toPrimitive]() defined in Array, continue.
2. since we expect string, `toString()` is tried before `valueOf()`, different from [Object.prototype.toString()](https://tc39.es/ecma262/multipage/fundamental-objects.html#sec-object.prototype.tostring), Array has its own [Array.prototype.toString()](https://tc39.es/ecma262/multipage/indexed-collections.html#sec-array.prototype.tostring)
   1. we can see from the [spec](https://tc39.es/ecma262/multipage/indexed-collections.html#sec-array.prototype.tostring) that `Array.prototype.join()` is used here.
   2. in [Array.prototype.join()](https://tc39.es/ecma262/multipage/indexed-collections.html#sec-array.prototype.join), it tries to convert all elements to string, which recursively calls [Array.prototype.toString()](https://tc39.es/ecma262/multipage/indexed-collections.html#sec-array.prototype.tostring).

Thus `['foo']` is converted to `'foo'` and `'foo' in object` is `true`.
