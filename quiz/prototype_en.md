# Task: #48. Prototype

## Tags: 

JavaScript

## Difficulty:

Easy

## Question:

What does the code snippet output by console.log?

## Explanation:

```javascript
function Foo() {}
Foo.prototype.bar = 1;
const a = new Foo();
console.log(a.bar); // 1

Foo.prototype.bar = 2;
const b = new Foo();
console.log(a.bar); // 2
console.log(b.bar); // 2

Foo.prototype = {bar: 3};
const c = new Foo();
console.log(a.bar); // 2
console.log(b.bar); // 2
console.log(c.bar); // 3
```

## Answer:

```javascript
1
2
2
2
2
3
```
