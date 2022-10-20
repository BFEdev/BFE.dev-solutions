# Task: #1. Promise order

## Question:

What does the code snippet output by console.log?

## Explanation:

```javascript
console.log(1); // run synchronously
const promise = new Promise((resolve) => {
  console.log(2); // run synchronously, because promise resolves immediately
  resolve();
  console.log(3); // run synchronously, because promise resolves immediately
});

console.log(4); // run synchronously

promise
  .then(() => {
    console.log(5); // add to MICRO task queue
  })
  .then(() => {
    console.log(6); // add to MICRO task queue
  });

console.log(7); // run synchronously

setTimeout(() => {
  console.log(8); // add to MACRO task queue
}, 10);

setTimeout(() => {
  console.log(9); // add to MACRO task queue
}, 0);
```

## Answer:

```javascript
1
2
3
4
7
5
6
9
8
```
