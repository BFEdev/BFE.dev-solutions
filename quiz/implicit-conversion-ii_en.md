## TL;DR

For a binary operator, such as [addition operator `+`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Addition#description) and [subtraction operator `-`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Subtraction), the result is decided by following steps

1. get primitive values of operands (number is preferred by default)
2. if operator is addition `+` and one of the primitive values is string
   1. covert both of them to string and do string concatenation
3. otherwise convert both of them to numeric values and do numeric calculation

To get primitive value of an object.

1. if [Symbol.toPrimitive](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/toPrimitive) is defined, use it
2. if string is preferred, first `toString()` then `valueOf()` is tried and return the first primitive value;
3. if number is preferred, first `valueOf()` then `toString()` is tried and return the first primitive value;

(below is the detailed explanation, you can scroll down to the end to see the problem analysis)

## Implicit Coercion

For [addition operator `+`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Addition#description), it is used for both numeric addition or string concatenation.

```js
1 + 2 // 3
'1' + '2' // '12'
```

> There is also [unary plus `+`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Unary_plus), these are different

For subtraction operator `-` it is only used for numeric subtraction

```js
1 - 2 // -1
```

What happens if some unexpected data types are used as operands? Well the operands are converted to [primitive values](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#primitive_values) implicitly.

Besides above 2 operators, implicit coercion takes places in many other situations. How it works exactly is written in the [ECMAScript Spec](https://tc39.es/ecma262/multipage/#sec-intro).

## ApplyStringOrNumericBinaryOperator() in ECMAScript Spec

According to the [spec of addition operator +](https://tc39.es/ecma262/multipage/ecmascript-language-expressions.html#sec-addition-operator-plus), the core is [ApplyStringOrNumericBinaryOperator](https://tc39.es/ecma262/multipage/ecmascript-language-expressions.html#sec-applystringornumericbinaryoperator), it actually also applies to other binary operator as well, including subtraction operator `-`.

Let's take a look at the spec.

> 1. If _opText_ is **+**, then
> 1. Let _lprim_ be ? ToPrimitive(lval).
> 1. Let _rprim_ be ? ToPrimitive(rval).
> 1. If _lprim_ is a String or _rprim_ is a String, then
>    1. Let _lstr_ be ? ToString(lprim).
>    2. Let _rstr_ be ? ToString(rprim).
>    3. Return the string-concatenation of _lstr_ and _rstr_.
> 1. Set _lval_ to lprim.
> 1. Set _rval_ to rprim.
> 1. NOTE: At this point, it must be a numeric operation.
> 1. Let _lnum_ be ? ToNumeric(lval).
> 1. Let _rnum_ be ? ToNumeric(rval).
> 1. If Type(lnum) is different from Type(rnum), throw a TypeError exception.
> 1. If _lnum_ is a BigInt, then
> 1. If _opText_ is \**, return ? BigInt::exponentiate(*lnum*, *rnum\*).
> 1. If _opText_ is /, return ? BigInt::divide(_lnum_, _rnum_).
> 1. If _opText_ is %, return ? BigInt::remainder(_lnum_, _rnum_).
> 1. If _opText_ is >>>, return ? BigInt::unsignedRightShift(_lnum_, _rnum_).
> 1. Let _operation_ be the abstract operation associated with _opText_ and Type(_lnum_) in the following table:

> | _opText_ | Type(_lnum_) | operation                  |
> | -------- | ------------ | -------------------------- |
> | \*\*     | Number       | Number::exponentiate       |
> | \*       | Number       | Number::multiply           |
> | \*       | BigInt       | BigInt::multiply           |
> | /        | Number       | Number::divide             |
> | %        | Number       | Number::remainder          |
> | +        | Number       | Number::add                |
> | +        | BigInt       | BigInt::add                |
> | -        | Number       | Number::subtract           |
> | -        | BigInt       | BigInt::subtract           |
> | <<       | Number       | Number::leftShift          |
> | <<       | BigInt       | BigInt::leftShift          |
> | >>       | Number       | Number::signedRightShift   |
> | >>       | BigInt       | BigInt::signedRightShift   |
> | >>>      | Number       | Number::unsignedRightShift |
> | &        | Number       | Number::bitwiseAND         |
> | &        | BigInt       | BigInt::bitwiseAND         |
> | ^        | Number       | Number::bitwiseXOR         |
> | ^        | BigInt       | BigInt::bitwiseXOR         |
> | \|       | Number       | Number::bitwiseOR          |
> | \|       | BigInt       | BigInt::bitwiseOR          |

> 8. Return operation(**lnum**, **rnum**).

> No hint is provided in the calls to ToPrimitive in steps 1.a and 1.b. All standard objects except Dates handle the absence of a hint as if number were given; Dates handle the absence of a hint as if string were given. Exotic objects may handle the absence of a hint in some other manner.

Looks intimidating but don't be, basically it could be summarized to following rules.

1. get primitive values of operands (number is preferred by default)
2. if operator is addition `+` and one of the primitive values is string
   1. covert both of them to string and do string concatenation
3. otherwise convert both of them to numeric values and do numeric calculation

Also the note is important - "no hint is provided to the ToPrimitive() call", let's see what it means.

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
  } 

+ obj // 100
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

The order is important, Now it's time to take a look at our problem.

## Problem Analysis

We can see the key is **the primitive values(numeric values preferred) of the operands**. In the problem, we have Array and object literals to consider.

For Array

1. there is no [Symbol.toPrimitive]() defined, continue.
2. Array inherits [Object.prototype.valueOf()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/valueOf#description) is tried, but it just returns the array itself, not a primitive value, continue
3. Different from [Object.prototype.toString()](https://tc39.es/ecma262/multipage/fundamental-objects.html#sec-object.prototype.tostring), Array has its own [Array.prototype.toString()](https://tc39.es/ecma262/multipage/indexed-collections.html#sec-array.prototype.tostring)
   1. we can see from the [spec](https://tc39.es/ecma262/multipage/indexed-collections.html#sec-array.prototype.tostring) that `Array.prototype.join()` is used here.
   2. in [Array.prototype.join()](https://tc39.es/ecma262/multipage/indexed-collections.html#sec-array.prototype.join), it tries to convert all elements to string, which recursively calls [Array.prototype.toString()](https://tc39.es/ecma262/multipage/indexed-collections.html#sec-array.prototype.tostring).

For Object literal

1. there is no [Symbol.toPrimitive]() defined, continue.
2. [Object.prototype.valueOf()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/valueOf#description) is tried, but it just returns the object itself, not a primitive value, continue
3. [Object.prototype.toString()](https://tc39.es/ecma262/multipage/fundamental-objects.html#sec-object.prototype.tostring) is tried
   1. from the [spec](https://tc39.es/ecma262/multipage/fundamental-objects.html#sec-object.prototype.tostring), we can see string `'[object Object]'` is returned.

So the problem becomes clear to us now.

> Keep in mind that string concatenation only applies when operator is addition `+` and one of the operands is string

| expression      | primitive val                           | numeric calc or string concat | primitive val again                     | result                             |
| --------------- | --------------------------------------- | ----------------------------- | --------------------------------------- | ---------------------------------- |
| `[] + []`       | `'' + ''`                               | string concat                 | `'' + ''`                               | `''`                               |
| `[] + 1`        | `'' + 1`                                | string concat                 | `'' + 1`                                | `'1'`                              |
| `[[]] + 1`      | `'' + 1`                                | string concat                 | `'' + 1`                                | `'1'`                              |
| `[[1]] + 1`     | `'1' + 1`                               | string concat                 | `'1' + '1'`                             | `'11'`                             |
| `[[[[2]]]] + 1` | `'2' + 1`                               | string concat                 | `'2' + '1'`                             | `'21'`                             |
| `[] - 1`        | `'' - 1 `                               | numeric                       | `0 - 1`                                 | `-1`                               |
| `[[]] - 1`      | `'' - 1`                                | numeric                       | `0 - 1`                                 | `-1`                               |
| `[[1]] - 1`     | `'1' - 1`                               | numeric                       | `1 - 1`                                 | `0`                                |
| `[[[[2]]]] - 1` | `'2' - 1`                               | numeric                       | `2 - 1`                                 | `1`                                |
| `[] + {}`       | `'' + '[object Object]'`                | string concat                 | `'' + '[object Object]'`                | `'[object Object]'`                |
| `{} + {}`       | `'[object Object]' + '[object Object]'` | string concat                 | `'[object Object]' + '[object Object]'` | `'[object Object][object Object]'` |
| `{} - {}`       | `'[object Object]' - '[object Object]'` | numeric                       | `NaN - NaN`                             | `NaN`                              |
