# Task: #53. Prototype 2

## Tags: 

JavaScript

## Difficulty:

Easy

## Question:

What does the code snippet output by console.log?

## Explanation:

```javascript
function F() {
  this.foo = 'bar';
}

const f = new F();
console.log(f.prototype); // undefined
```

## Answer:

```javascript
undefined
```
