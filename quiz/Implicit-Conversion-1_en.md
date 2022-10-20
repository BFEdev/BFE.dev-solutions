# Task: #8. Implicit Coercion I

## Tags: 

JavaScript

## Difficulty:

Easy

## Question:

What does the code snippet output by console.log?

## Explanation:

```javascript
console.log(Boolean("false"));  // true - only '' will false
console.log(Boolean(false));    // false - no conversion
console.log("3" + 1);           // "31" - consist string and operator "+" -> triggered toSrting()
console.log("3" - 1);           // 2 - operator "-" always triggered toNumber()
console.log("3" - " 02 ");      // 1 - same as previous and trim spaces: (3 - 2) = 1
console.log("3" * " 02 ");      // 6 - same as previous and trim spaces: (3 * 2) = 6
console.log(Number("1"));       // 1 - from sting to number
console.log(Number("number"));  // NaN - not valid
console.log(Number(null));      // 0 - see spec ECMA
console.log(Number(false));     // 0 - see spec ECMA
```

## Answer:

```javascript
true
false
"31"
2
1
6
1
NaN
0
0
```
