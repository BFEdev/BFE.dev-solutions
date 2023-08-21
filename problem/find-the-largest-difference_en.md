Created by [@vekaev](https://github.com/vekaev)

```javascript
/**
 * @param {number[]} arr
 * @return {number}
 */
function largestDiff(arr) {
  let smallest = Infinity;
  let largest = -Infinity;

  for (const num of arr) {
    if (smallest > num) smallest = num;
    if (num > largest) largest = num
  }

  if (smallest === Infinity) smallest = 0;
  if (largest === -Infinity) largest = 0;

  return Math.abs(smallest - largest);
}
```
