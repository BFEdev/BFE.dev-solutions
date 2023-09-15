### Problem
The task requires you to write a function that can compare two semantic versioning (SemVer) strings.

Semantic versioning is a versioning scheme for software that aims to convey meaning about the underlying changes in a release. A SemVer string is composed of three parts: Major, Minor, and Patch, and it is written in the format of `Major.Minor.Patch`.

For example, version `1.2.3` has a Major version of `1`, a Minor version of `2`, and a Patch version of `3`.

In this task, your function should return `1` if the first SemVer string is greater than the second, `-1` if it's smaller, and `0` if they're equal.

### Solution
The provided solution first splits the SemVer strings into arrays by using the `split` function with '.' as the separator. This splits the Major, Minor, and Patch versions into separate elements in the array.

The `map` function is used to convert all the string elements in the array to numbers, which are easier to compare.

Then the function iterates through the elements of the first array (which correspond to the Major, Minor, and Patch versions, respectively), and compares each element with the corresponding element in the second array.

If the element in the first array is smaller than the corresponding element in the second array, the function returns `-1`, indicating that the first SemVer string is smaller.

If the element in the first array is larger, the function returns `1`, indicating that the first SemVer string is larger.

If neither of these conditions is met, the function continues to the next element.

If all elements are equal, the function returns `0`, indicating that the two SemVer strings are equal.

Here is the JavaScript code for the solution:

```javascript
/**
 * @param {string} v1
 * @param {string} v2
 * @returns 0 | 1 | -1
 */
function compare(v1, v2) {
  const arr1 = v1.split('.').map(num => Number(num));
  const arr2 = v2.split('.').map(num => Number(num));

  for (let i = 0; i < arr1.length; i++) {
    if (arr1[i] < arr2[i]) {
      return -1;
    }

    if (arr1[i] > arr2[i]) {
      return 1;
    }
  }

  return 0;
}
```

### Complexity

This solution has a time complexity of O(n), where n is the number of elements in the version string (which is constant and equals to 3 in this case), and a space complexity of O(1) as no additional space that scales with input size is used.
