## TL;DR

`++a` and `a++` both increments, but returns different values. `++a` returns new value while `a++` returns the old one.

```js
let a = 1
const b = ++a
// a is 2, b is 2
const c = a++
// a is 3, c is 2
console.log(a)
// 3
console.log(b)
// 2
console.log(c)
// 2
```

## ECMAScript spec for Prefix Increment Operator

From the [spec](https://tc39.es/ecma262/#sec-prefix-increment-operator), we can easily understand how it works for `++a`.

> UpdateExpression : ++ UnaryExpression
>
> 1. Let _expr_ be ? Evaluation of UnaryExpression.
> 2. Let _oldValue_ be ? ToNumeric(? GetValue(_expr_)).
> 3. If _oldValue_ is a Number, then
>    1. Let _newValue_ be Number::add(_oldValue_, 1𝔽).
> 4. Else,
>    1. Assert: _oldValue_ is a BigInt.
>    2. Let _newValue_ be BigInt::add(_oldValue_, 1ℤ).
> 5. Perform ? PutValue(_expr_, _newValue_).
> 6. Return _newValue_.

Simply the last line, we can see new value is returned.

## ECMAScript spec for Postfix Increment Operator

From the [spec](https://tc39.es/ecma262/#sec-postfix-increment-operator), we can easily understand how it works for `a++`.

> UpdateExpression : LeftHandSideExpression ++
>
> 1. Let _lhs_ be ? Evaluation of LeftHandSideExpression.
> 2. Let _oldValue_ be ? ToNumeric(? GetValue(_lhs_)).
> 3. If _oldValue_ is a Number, then
>    1. Let _newValue_ be Number::add(_oldValue_, 1𝔽).
> 4. Else,
>    1. Assert: _oldValue_ is a BigInt.
>    2. Let _newValue_ be BigInt::add(_oldValue_, 1ℤ).
> 5. Perform ? PutValue(_lhs_, _newValue_).
> 6. Return _oldValue_.

We can see that the steps are basically the same as Prefix Increment Operator, except that at last old value is returned.
