## TL;DR

```js
console.log(Boolean('false')) // true
console.log(Boolean(false)) // false
```

`Boolean()` converts arbitrary types to boolean. Only a few are converted to `false`, including
`undefined`, `null`, `0`, `-0`, `NaN`,`0n`,`''`, all other types converted to `true`.

```js
console.log('3' + 1) // '31`
```

For addition operator `+`, if one of the operands is string, then it is treated as string concatenation.
In the above example, `1` is converted to `'1'` and concatenated to `'31'`

```js
console.log('3' - ' 02 ') // 1
console.log('3' * ' 02 ') // 6
```

Other than addition operator `+`, there is no overload, all will be treated as numeric calculations,
when string is converted to numbers, leading and trailing spaces are ignored, thus `3 - 2 = 1` and `3 * 2 = 6`

```js
console.log(Number('1')) // 1
console.log(Number('number')) // NaN
console.log(Number(null)) // 0
console.log(Number(false)) // 0
```

`Number()` converts arbitrary types to number. For Object, its primitive values are used. For string, it depends of if it is valid number string,
otherwise falsy values are converted to 0.

Below is longer version of detailed explanation.

## ToBoolean

From [ECMAScript spec](https://tc39.es/ecma262/#sec-toboolean), the logic of converting to boolean is straightforward.

> | Argument Type | Result                                                                                      |
> | ------------- | ------------------------------------------------------------------------------------------- |
> | Undefined     | Return false.                                                                               |
> | Null          | Return false.                                                                               |
> | Boolean       | Return argument.                                                                            |
> | Number        | If argument is +0<sub>𝔽</sub>, -0<sub>𝔽</sub>, or NaN, return false; otherwise return true. |
> | String        | If argument is the empty String (its length is 0), return false; otherwise return true.     |
> | Symbol        | Return true.                                                                                |
> | BigInt        | If argument is 0<sub>ℤ</sub>, return false; otherwise return true.                          |
> | Object        | Return true.                                                                                |

One thing to pay attention is that for String, it only checks its length.

```js
console.log(Boolean('')) // false
console.log(Boolean(' ')) // true
```

Back to our problem the results below are obvious.

```js
console.log(Boolean('false')) // true
console.log(Boolean(false)) // false
```

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

This brings us following answers.

```js
console.log('3' + 1) // '31`
console.log('3' - ' 02 ') // 1
console.log('3' * ' 02 ') // 6
```

## ToNumber

From [ECMAScript spec](https://tc39.es/ecma262/#sec-tonumber), the convertion to numer is also straightforward.

> | Argument Type | Result                                                                                                           |
> | ------------- | ---------------------------------------------------------------------------------------------------------------- |
> | Undefined     | Return NaN.                                                                                                      |
> | Null          | Return +0<sub>𝔽</sub>.                                                                                           |
> | Boolean       | If argument is true, return 1<sub>𝔽</sub>. If argument is false, return +0<sub>𝔽</sub>.                          |
> | Number        | Return argument (no conversion).                                                                                 |
> | String        | Return ! StringToNumber(argument).                                                                               |
> | Symbol        | Throw a TypeError exception.                                                                                     |
> | BigInt        | Throw a TypeError exception.                                                                                     |
> | Object        | Apply the following steps: 1. Let primValue be ? ToPrimitive(argument, number). 2. Return ? ToNumber(primValue). |

1. even though undefined and null are very much alike in many ways, they are converted to different number values
2. BigInt throws an error

You might wonder why BigInt throws, here is an example illustrating this

```js
1n + 1 // TypeError: Cannot mix BigInt and other types, use explicit conversions
Number(1n) + 1 // 2
```

Number() has the logic that covers BigInt.

## Number()

From [ECMAScript spec](https://tc39.es/ecma262/multipage/numbers-and-dates.html#sec-number-constructor-number-value), `Number()` does blow things when called.

1. If _value_ is present, then
   a. Let _prim_ be ? ToNumeric(value).
   b. If _prim_ is a BigInt, let n be 𝔽(ℝ(prim)).
   c. Otherwise, let _n_ be _prim_.
2. Else,
   a. Let _n_ be +0<sub>𝔽</sub>s.
3. If NewTarget is undefined, return n.
4. Let _O_ be ? OrdinaryCreateFromConstructor(NewTarget, "%Number.prototype%", « [[NumberData]] »).
5. Set _O_.[[NumberData]] to n.
6. Return _O_.

Notice that ToNumeric() is different from ToNumber(), according to the [ECMAScript spec](https://tc39.es/ecma262/multipage/abstract-operations.html#sec-tonumeric).

1. Let _primValue_ be ? ToPrimitive(value, number).
2. If _primValue_ is a BigInt, return primValue.
3. Return ? ToNumber(primValue).

We can see from here that if BigInt is used, then **𝔽(ℝ(prim))** returns the number value.

And here comes the answers like below.

```js
console.log(Number('1')) // 1
console.log(Number('number')) // NaN
console.log(Number(null)) // 0
console.log(Number(false)) // 0
```
