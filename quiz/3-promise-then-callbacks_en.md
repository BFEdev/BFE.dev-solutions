# Task: #3. Promise then callbacks

## Question:

What does the code snippet output by console.log?


## Explanation:

```javascript
Promise.resolve(1)             // pass 1   
  .then(() => 2)               // pass 2
  .then(3)                     // skip - there is no function in then() - will be skipped
  .then((value) => value * 3)  // pass 6 - took last not skipped value: 2 * 3 = 6
  .then(Promise.resolve(4))    // skip - there is no function in then() - will be skipped  
  .then(console.log);          // log 6 - took last not skipped value 6
```

## Answer:

```javascript
6
```
