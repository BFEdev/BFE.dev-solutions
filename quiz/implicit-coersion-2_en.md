# Task: #46. Implicit Coercion IV

What does the code snippet output by console.log?

## Explanation:

```javascript
const foo = [0];
if (foo) {
  console.log(foo == true); // false - when using '==' both sides convert to numbers: 0 == 1 -> false
} else {
  console.log(foo == false);
}
```

## Answer:

```javascript
false
```
