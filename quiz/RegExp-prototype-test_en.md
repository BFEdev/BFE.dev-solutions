# Task: #40. RegExp.prototype.test

## Tags: 

JavaScript

## Difficulty:

Easy

## Question:

What does the code snippet output by console.log?

## Explanation:

```javascript
console.log(/^4\d\d$/.test('404'));   // true
console.log(/^4\d\d$/.test(404));     // true - converts to string
console.log(/^4\d\d$/.test(['404'])); // true - converts to string
console.log(/^4\d\d$/.test([404]));   // true - converts to string
```

## Answer:

```javascript
true
true
true
true
```
