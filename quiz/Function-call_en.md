# Task: #44. Function call

What does the code snippet output by console.log?

## Explanation:

```javascript
function a() {
  console.log(1);
  return {
    a: function () {
      console.log(2);
      return a();
    },
  };
}

a().a(); // 1, 2, 1
```

## Answer:

```javascript
1
2
1
```
