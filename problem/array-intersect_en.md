# Solution For Array Intersect In Javascript And Remove Duplicate values
### We Use Set To Remove Duplicate Values

```js
function getIntersection(arr1, arr2) {
  let a = new Set(arr1);
  let b = new Set(arr2);
  return [...a].filter(a => b.has(a));
}

console.log(getIntersection(
  [1,2,3,4,5,5,6,],[5,4,5,3,5,65,62,22,3,4,5]
))
```
