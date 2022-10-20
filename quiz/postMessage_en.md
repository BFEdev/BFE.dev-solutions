# Task: #60. postMessage

## Tags: 

JavaScript

## Difficulty:

Easy

## Question:
What does the code snippet output by console.log?

## Explanation:

```javascript
console.log(1);

window.onmessage = () => {
  console.log(2);
};

Promise.resolve().then(() => {
  console.log(3);
});

setTimeout(() => {
  console.log(4);
}, 0);

console.log(5);

window.postMessage('');

console.log(6);

/*
1 - sync code
5 - sync code
6 - sync code
3 - promise is microtask
2 - postMessage is microtask
4 - timeout is macrotask
*/
```

## Answer:

```javascript
1
5
6
3
2
4
```
