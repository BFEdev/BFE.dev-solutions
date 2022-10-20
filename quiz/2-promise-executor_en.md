# Task: #2. Promise executor

## Question:

What does the code snippet output by console.log?


## Explanation:

```javascript
new Promise((resolve, reject) => {
  resolve(1); // 1 will be printed, but next lines will be ignored
  resolve(2);
  reject("error");
}).then(
  (value) => {
    console.log(value);
  },
  (error) => {
    console.log("error");
  }
);
```

## Answer:

```javascript
1
```
