# Task: #69. undefined

## Tags: 

JavaScript

## Difficulty:

Easy

## Question:

What does the code snippet output by console.log?

## Explanation:

```javascript
function foo(a, b, undefined, undefined) {
  console.log('BFE.dev'); // will not log, because function is not executed
}
console.log(foo.length); // 4 - function's length === count of function's arguments
```

## Answer:

```javascript
4
```
