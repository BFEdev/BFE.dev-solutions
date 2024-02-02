
A simple and elegant Binary Search solution. Cheers, Batman!

Time: O[log(n)], Space: O[1]


```javascript

/**
 * @param {any} x
 * @return {number}
 */
function mySqrt(x) {
  let high = x / 2;  // reduces the binary search by 50%!!
  let low = 0;
  let result = 1;

  if(x < 0 || isNaN(x)) return NaN;

  while(low <= high) {
    mid = Math.floor(low + (high - low) / 2);  // modified to handle overflow errors
    if(mid * mid === x) {
      return mid;
    }
    if(mid * mid > x) {
      high = mid - 1;
    } else {
      result = Math.max(result, mid)
      low = mid + 1;
    }
  }
  return result;
}

const result = mySqrt(49);
console.log(result);
```
