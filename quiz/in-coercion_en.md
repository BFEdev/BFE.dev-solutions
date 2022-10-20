# Task: #63. in

## Tags: 

JavaScript

## Difficulty:

Easy

## Question:

What does the code snippet output by console.log?

## Explanation:

```javascript
const obj = {
  foo: 'bar',
};

console.log('foo' in obj); // true
console.log(['foo'] in obj); // true - ['foo'] converts to string
```

## Answer:

```javascript
true
true
```
