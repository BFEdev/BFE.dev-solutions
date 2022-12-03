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

The [equality (==) operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Equality) checks whether its two operands are equal, and unlike the strict equality operator ===, it attempts to convert and compare operands that are of different types. We have,

```
[0].toString() // "0"
0..toString() // "0"
```

In this example, we are comparing different data types which means `==` will convert the operands to strings, and then compare. So, the first comparison evaluates to `false`, and the second one is `true`

```js
console.log([0] == ""); // "0" == "" 👉🏻 false
console.log([0] == 0); // "0" == "0" 👉🏻 true
```
