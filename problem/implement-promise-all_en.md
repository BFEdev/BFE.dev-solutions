## Overview

The `all` function is a polyfill for the native `Promise.all` method. It takes an array of promises and returns a new promise that resolves when all the promises in the array have resolved or rejects if any of the promises in the array reject. This custom implementation also handles cases where the input array might contain non-promise values.

## Implementation

Here's the implementation of the `all` function:

```javascript
/**
 * @param {Array<any>} promises - Notice the input might have non-Promises
 * @return {Promise<any[]>}
 */

function all(promises) {
  if (promises.length === 0) {
    return Promise.resolve([]);
  }

  let resultArray = [];
  let resolvedCount = 0;
  return new Promise((resolve, reject) => {
    promises.forEach(async (p, index) => {
      try {
        const promiseResult = await p;
        resultArray[index] = promiseResult;
        resolvedCount++;
        if (resolvedCount === promises.length) {
          resolve(resultArray);
        }
      } catch (error) {
        reject(error);
      }
    });
  });
}
```

## Explanation

1. **Handling Empty Array:**

   - The first check in the function is to see if the `promises` array is empty. If it is, the function returns a resolved promise with an empty array: return `Promise.resolve([]);`.
   - This is because if there are no promises to wait for, the result should be an empty array.

2. **Variables Initialization:**

   - `resultArray`: An array to store the resolved values of the promises.
   - `resolvedCount`: A counter to keep track of how many promises have resolved.

3. **Returning a New Promise:**

   - The function returns a new promise that will eventually resolve or reject based on the input promises.
   - Inside the promise, we use `promises.forEach` to iterate over each promise in the input array.

4. **Handling Each Promise:**

   - For each promise, the `await` keyword is used to wait for the promise to resolve.
   - The `await` keyword also handles any non-promise value.
   - The resolved value is then stored in `resultArray` at the corresponding index.
   - The `resolvedCount` is incremented by 1.

5. **Resolving the Result:**

   - After incrementing `resolvedCount`, we check if it matches the length of the input `promises` array.
   - If all promises have resolved (`resolvedCount === promises.length`), the `resolve` function is called with the `resultArray`.

6. **Error Handling:**
   - If any promise rejects, the `catch` block catches the error, and the `reject` function is called to reject the entire promise.
