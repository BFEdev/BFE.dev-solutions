> This awesome solution is created by [@abhijeet-saxena](https://github.com/abhijeet-saxena)

## Problem

```js
console.log([0] == "");
console.log([0] == 0);
```

## Solution

```
false
true
```

## Explanation

Here, in both cases the left operand `[0]` is an `object` while the right operand is a [primitive](https://developer.mozilla.org/en-US/docs/Glossary/Primitive) (string in first line and number in the second)

The [equality (==) operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Equality) checks whether its two operands are equal, and unlike the strict equality operator ===, it attempts to convert and compare operands that are of different types.  To do this comparison, JS will follow the rules for [Loose equality using ==](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness#loose_equality_using) which states that:

> If one of the operands is an object and the other is a primitive, convert the object to a primitive.

This [Primitive coercion](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#primitive_coercion) effectively uses `toString` to get the primitive value i.e `[0]` will be treated as `"0"`

Now after this implicit coercion,
1. In the first comparison as both operands are `string` the comparison is straightforward and returns `false`
2. In the second comparison, we are comparing `string` to a `number`, here the string is first coerced into a number and then comparison happens i.e "0" is coerced to 0 in the returning `true`

```js
console.log([0] == '') // "0" == "" 👉🏻 false
console.log([0] == 0) // "0" == 0 👉🏻 0 == 0 👉🏻 true
```
