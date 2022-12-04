## TL;DR

```js
console.log(JSON.stringify([1, 2, null, 3]))
// "[1,2,null,3]" as it is
console.log(JSON.stringify([1, 2, undefined, 3]))
// "[1,2,null,3]"
// undefined in array is represented as "null",
console.log(null === undefined)
// false, null and undefined are different
console.log(null == undefined)
// true, this is by design, null and undefined are loosely equal
console.log(null == 0)
// false. For `==`, null is only loosely equal to undefined, no other values.
console.log(null < 0)
// false.  since both of them are not string, null is converted to number, which is 0. 
// 0 < 0 is false
console.log(null > 0)
// false. since both of them are not string, null is converted to number, which is 0. 
// 0 > 0 is false
console.log(null <= 0)
// true. `a <= b` is internally done by revsersing the result of `b < a`, 
// in turn it is `a < b`, since `0 <= null` is false. the result is true
console.log(null >= 0)
// true. `a >= b` is internally done by revsersing the result of `a < b`, 
// since `0 <= null` is false. the result is true
console.log(undefined == 0)
// false. For `==`, null is only loosely equal to undefined, no other values.
console.log(undefined < 0)
// false. since both of them are not string, undefined is converted to number, which is NaN.
// NaN < 0 is false
console.log(undefined > 0)
// false. since both of them are not string, undefined is converted to number, which is NaN. 
// NaN > 0 is false
console.log(undefined <= 0)
// false. since both of them are not string, undefined is converted to number, which is NaN. 
// But internally NaN in `<` comparison results in `undefined`, which is diffrent from null case. 
// `undefined` is reversed in to `true` (Yeah I know it's weird)
console.log(undefined >= 0)
// false. since both of them are not string, undefined is converted to number, which is NaN. 
// But internally NaN in `<` comparison results in `undefined`, which is diffrent from null case. 
// `undefined` is reversed in to `true` (Yeah I know it's weird)
```

These quizzes are quite straightforward in ECMAScript spec. Let's dive into it.

## JSON.stringify

In [ECMAScript spec](https://tc39.es/ecma262/#sec-json.stringify), we can know see that

> Symbolic primitive values are rendered as follows:
>
> - The **null** value is rendered in JSON text as the String value "**null**".
> - The **undefined** value is not rendered.
> - The **true** value is rendered in JSON text as the String value "**true**".
> - The **false** value is rendered in JSON text as the String value "**false**".

Also it says

> Values that do not have a JSON representation (such as **undefined** and functions) do not produce a String. Instead they produce the undefined value. In arrays these values are represented as the String value "**null**". In objects an unrepresentable value causes the property to be excluded from stringification.

This explains why `undefined` becomes `null` in JSON.stringfiy.

## IsStrictlyEqual ( x, y ), ===

According to [ECMAScript spec](https://tc39.es/ecma262/multipage/abstract-operations.html#sec-isstrictlyequal) Strict equal first checks types. `null` and `undefined` are not the same types, so false.

## IsLooselyEqual ( x, y ), ==

From [ECMAScript spec](https://tc39.es/ecma262/#sec-islooselyequal), loosely equal check works as below.

> 1. If Type(_x_) is the same as Type(_y_), then
>    1. Return IsStrictlyEqual(_x_, _y_).
> 2. If _x_ is null and _y_ is undefined, return true.
> 3. If _x_ is undefined and _y_ is null, return true.
> 4. NOTE: This step is replaced in section B.3.6.2.
> 5. If _x_ is a Number and _y_ is a String, return ! IsLooselyEqual(_x_, ! ToNumber(_y_)).
> 6. If _x_ is a String and _y_ is a Number, return ! IsLooselyEqual(! ToNumber(_x_), _y_).
> 7. If _x_ is a BigInt and _y_ is a String, then
>    1. Let n be StringToBigInt(_y_).
>    2. If n is undefined, return false.
>    3. Return ! IsLooselyEqual(_x_, n).
> 8. If _x_ is a String and _y_ is a BigInt, return ! IsLooselyEqual(_y_, _x_).
> 9. If _x_ is a Boolean, return ! IsLooselyEqual(! ToNumber(_x_), _y_).
> 10. If _y_ is a Boolean, return ! IsLooselyEqual(_x_, ! ToNumber(_y_)).
> 11. If _x_ is either a String, a Number, a BigInt, or a Symbol and _y_ is an Object, return ! IsLooselyEqual(_x_, ? ToPrimitive(_y_)).
> 12. If _x_ is an Object and _y_ is either a String, a Number, a BigInt, or a Symbol, return ! IsLooselyEqual(? ToPrimitive(_x_), _y_).
> 13. If _x_ is a BigInt and _y_ is a Number, or if _x_ is a Number and _y_ is a BigInt, then
>     1. If _x_ is not finite or _y_ is not finite, return false.
>     2. If ℝ(_x_) = ℝ(_y_), return true; otherwise return false.
> 14. Return false.

We can see that

1. null and undefined are handled specially if there are compared, in step 2 and 3
2. null and undefined cannot be compared to other values in equality check.

## Relational Operators <, >

Here are the definitions of relational operators in [ECMAScript spec](https://tc39.es/ecma262/multipage/ecmascript-language-expressions.html#sec-relational-operators-runtime-semantics-evaluation)

> RelationalExpression : RelationalExpression < ShiftExpression
>
> 1. Let _lref_ be ? Evaluation of RelationalExpression.
> 2. Let _lval_ be ? GetValue(_lref_).
> 3. Let _rref_ be ? Evaluation of ShiftExpression.
> 4. Let _rval_ be ? GetValue(_rref_).
> 5. Let r be ? IsLessThan(_lval_, _rval_, true).
> 6. If r is undefined, return false. Otherwise, return r.

> RelationalExpression : RelationalExpression > ShiftExpression

> 1. Let _lref_ be ? Evaluation of RelationalExpression.
> 2. Let _lval_ be ? GetValue(_lref_).
> 3. Let _rref_ be ? Evaluation of ShiftExpression.
> 4. Let _rval_ be ? GetValue(_rref_).
> 5. Let r be ? IsLessThan(_rval_, _lval_, false).
> 6. If r is undefined, return false. Otherwise, return r.

The core logic for both lies in [IsLessThan()](https://tc39.es/ecma262/multipage/abstract-operations.html#sec-islessthan) is a bit long.

> IsLessThan ( x, y, LeftFirst )
>
> 1. If the LeftFirst flag is true, then
>    1. Let _px_ be ? **ToPrimitive**(x, number).
>    2. Let _py_ be ? **ToPrimitive**(y, number).
> 2. Else,
>    1. NOTE: The order of evaluation needs to be reversed to preserve left to right evaluation.
>    2. Let _py_ be ? **ToPrimitive**(y, number).
>    3. Let _px_ be ? **ToPrimitive**(x, number).
> 3. If _px_ is a String and _py_ is a String, then
>    1. Let _lx_ be the length of _px_.
>    2. Let _ly_ be the length of _py_.
>    3. For each integer i such that 0 ≤ i < min(_lx_, _ly_), in ascending order, do
>       1. Let cx be the integer that is the numeric value of the code unit at index i within _px_.
>       2. Let cy be the integer that is the numeric value of the code unit at index i within _py_.
>       3. If cx < cy, return true.
>       4. If cx > cy, return false.
>    4. If _lx_ < _ly_, return true. Otherwise, return false.
> 4. Else,
>    1. If _px_ is a BigInt and _py_ is a String, then
>       1. Let _ny_ be StringToBigInt(_py_).
>       2. If _ny_ is undefined, return undefined.
>       3. Return BigInt::lessThan(_px_, _ny_).
>    2. If _px_ is a String and _py_ is a BigInt, then
>       1. Let _nx_ be StringToBigInt(_px_).
>       2. If _nx_ is undefined, return undefined.
>       3. Return BigInt::lessThan(_nx_, _py_).
>    3. NOTE: Because _px_ and _py_ are primitive values, evaluation order is not important.
>    4. Let _nx_ be ? **ToNumeric**(_px_).
>    5. Let _ny_ be ? **ToNumeric**(_py_).
>    6. If Type(_nx_) is the same as Type(_ny_), then
>       1. If _nx_ is a Number, then
>          1. Return Number::lessThan(_nx_, _ny_).
>       2. Else,
>          1. Assert: _nx_ is a BigInt.
>          2. Return BigInt::lessThan(_nx_, _ny_).
>    7. Assert: _nx_ is a BigInt and _ny_ is a Number, or _nx_ is a Number and _ny_ is a BigInt.
>    8. If _nx_ or _ny_ is NaN, return undefined.
>    9. If _nx_ is -∞𝔽 or _ny_ is +∞𝔽, return true.
>    10. If _nx_ is +∞𝔽 or _ny_ is -∞𝔽, return false.
>    11. If ℝ(_nx_) < ℝ(_ny_), return true; otherwise return false.

Notice that both values are converted to numeric values in step 4.4 and 4.5, where null and undefined has different numeric values returned.

Also pay attention that in step 8, undefined is returned if either operand is NaN.

> ToNumber ( argument )
>
> 1. If _argument_ is a Number, return _argument_.
> 2. If _argument_ is either a Symbol or a BigInt, throw a TypeError exception.
> 3. If _argument_ is **undefined**, return **NaN**.
> 4. If _argument_ is either **null** or **false**, return +0𝔽.
> 5. If _argument_ is **true**, return 1𝔽.
> 6. If _argument_ is a String, return StringToNumber(_argument_).
> 7. Assert: _argument_ is an Object.
> 8. Let primValue be ? ToPrimitive(_argument_, number).
> 9. Assert: primValue is not an Object.
> 10. Return ? ToNumber(primValue).

In step 3 and 4, we can clearly see that undefined is `NaN` and null is `0`.

This explains why `null > 0`, `null < 0`, `undefined < 0`, `undefined > 0` are all false, but with different reasons.

## Relational Operators <=, >=

Here are the definitions of relational operators in [ECMAScript spec](https://tc39.es/ecma262/multipage/ecmascript-language-expressions.html#sec-relational-operators-runtime-semantics-evaluation)

> RelationalExpression : RelationalExpression <= ShiftExpression
>
> 1. Let _lref_ be ? Evaluation of RelationalExpression.
> 2. Let _lval_ be ? GetValue(_lref_).
> 3. Let _rref_ be ? Evaluation of ShiftExpression.
> 4. Let _rval_ be ? GetValue(_rref_).
> 5. Let r be ? IsLessThan(_rval_, _lval_, false).
> 6. If r is true or undefined, return false. Otherwise, return true.

> RelationalExpression : RelationalExpression >= ShiftExpression

> 1. Let _lref_ be ? Evaluation of RelationalExpression.
> 2. Let _lval_ be ? GetValue(_lref_).
> 3. Let _rref_ be ? Evaluation of ShiftExpression.
> 4. Let _rval_ be ? GetValue(_rref_).
> 5. Let r be ? IsLessThan(_lval_, _rval_, true).
> 6. If r is true or undefined, return false. Otherwise, return true.

Tricky part here is that we can see result of `>=` and `<=` are derived by **reversing the result of `<` and `>`**.

So `null >= 0` and `null <= 0` are true.

But for undefined since `IsLessThan` returns undefined because numeric value of undefined is `NaN`, step 6 treats `undefined` as true, so the result is false.

Thus `undefined >= 0` and `undefined <=0` are both false.
