> This solution is submitted by [@CaptainOfFlyingDutchman](https://github.com/CaptainOfFlyingDutchman)
>
To implement this solution we need to figure out 2 things

1. How many times we need to iterate through the array.
2. Should not modify the original array, which is a good practice in itself.

---

We just cannot iterate through the array elements one by one, if we do that then we need to maintain additional state variables to track **how many elements** we need to iterate for before collecting them in a new array and then start again collecting then in a new iteration.

Did you notice **how many elements** in last paragraph? We have this information already through the second parameter `size`. We can easily use that to iterate through the array using the convention old school `for` statement.

The following code will increase the variable `i` by `size` instead of traditional `i++` syntax (that can be written like `i += 1` by the way).

```javascript
for (let i = 0; i < items.length; i += size) {
    // do stuff
}
```

As we should not modify the original array we can use the `Array.prototype.slice` method, that slice the array using two given indexes and returns a new array leaving original array intact (a bit of **immutability** genius right there).

One thing to note that the second parameter to `slice` is optional and can be greater than the array's length, in that case `slice` will return all the remaining elements starting `start` parameter to end of the array.

```javascript
const sliced = items.slice(0, 3);
// items before slice => [1, 2, 3, 4, 5]
// slices => [1, 2, 3]
// items after slice => [1, 2, 3, 4, 5]
```

The last thing to do is to capture this `slice` result in an array because that is what `chunk` needs to return, an *Array*.

```javascript
const chunks = [];

chunks.push(items.slice(0, 3));
```

We also need to take care of an edge case where if user supplies `0` as the size then return an empty array `[]`, or else our loop will become an `infinite loop` because what would we get adding `zero` in the already `zero` initialized variable `i`, `0`.

Here is the full implementation

```javascript
/**
 * @param {any[]} items
 * @param {number} size
 * @returns {any[][]}
 */
function chunk(items, size) {
  if (size < 1) {
    return [];
  }

  const chunks = [];

  for (let i = 0; i < items.length; i += size) {
    chunks.push(items.slice(i, size + i));
  }

  return chunks;
}
```
