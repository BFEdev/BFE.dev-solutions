# Task: #39. var

## Question:

What does the code snippet output by console.log?

## Explanation:

```javascript
function foo() {
  console.log(i); // var will hoisted -> i === undefined
  for (var i = 0; i < 3; i++) {
    console.log(i);
  }
}

foo(); // undefined, 0, 1, 2
```

## Answer:

```javascript
undefined
0
1
2
```
