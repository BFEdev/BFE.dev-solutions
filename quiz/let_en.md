# Task: #89. let

## Tags: 

JavaScript

## Difficulty:

Easy

## Question:

What does the code snippet output by console.log?

## Explanation:

```javascript
let a = 1;
(function () {
  let foo = () => a;
  let a = 2;
  console.log(foo()); // 2 - look at the function invocation, not at the function declaration
})();
```

## Answer:

```javascript
2
```
