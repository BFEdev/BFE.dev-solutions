# Task: #13. Operator precedence

## Tags: 

JavaScript

## Difficulty:

Easy

## Question:

What does the code snippet output by console.log?

## Explanation:

```javascript
console.log(0 == 1 == 2); // false - false == 2
console.log(2 == 1 == 0); // true - false == 0
console.log(0 < 1 < 2);   // true - true < 2 => 1 < 2
console.log(1 < 2 < 3);   // true - true < 3 => 1 < 3
console.log(2 > 1 > 0);   // true - true > 0 => 1 > 0
console.log(3 > 2 > 1);   // false - true > 1 => 1 > 1
```

## Answer:

```javascript
false
true
true
true
true
false
```
