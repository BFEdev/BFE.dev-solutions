# Task: #61. onClick

## Tags: 

JavaScript

## Difficulty:

Easy

## Question:

What does the code snippet output by console.log?

## Explanation:

```javascript
console.log(1)

document.body.addEventListener('click', () => {
  console.log(2)
})

Promise.resolve().then(() => {
  console.log(3)
})

setTimeout(() => {
  console.log(4)
}, 0)

console.log(5)

document.body.click()

console.log(6)

/*
1 - sync code
5 - sync code
2 - click now is sync
6 - sync code
3 - microtask
4 - macrotask
*/
```

## Answer:

```javascript
1
5
2
6
3
4
```
