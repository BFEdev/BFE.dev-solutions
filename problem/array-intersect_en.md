> This awesome solution is created by [@btjl](https://github.com/btjl) !

## Goal

Implement a function that takes the intersection of 2 arrays. Remove any duplicates values.

## Observations

1. arrays are not sorted, and might have duplicates.

    - Sorting will help to identify duplicates, but doing so will immediately increase the time-complexity to `O(nlogn)` hence lets bear this in mind.

2. you can modify the arrays

    - Since arrays are reference types, we might not want to modify their values as it may lead to unintended effects outside of this function scope.

3. you can return the items in any order, but without duplicates.

    - `Set` is a data structure that ensures no duplicates may exist in it.

## Implementation

```js
/**
 * @param {any[]} arr1
 * @param {any[]} arr2
 * @returns {any[]}
 */
function getIntersection(arr1, arr2) {
  const arr1Set = new Set(arr1);
  const addedSet = new Set();

  return arr2.filter((item) => {
    if (arr1Set.has(item) && !addedSet.has(item)) {
      addedSet.add(item);
      return item;
    }
  });
}
```
