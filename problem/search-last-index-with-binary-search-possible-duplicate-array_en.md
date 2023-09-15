### Solution

```javascript
function lastIndex(arr, target){
  let l = 0;
  let r = arr.length - 1;

  while (l < r) {
    const mid = Math.floor((l + r + 1) / 2); // Calculate mid as the ceiling of (l + r) / 2

    if (arr[mid] <= target) { 
      l = mid; // If target is greater than or equal to arr[mid], set l to mid
    } else {
      r = mid - 1; // If target is less than arr[mid], set r to mid - 1
    }
  }

  if(arr[l] === target) { // Check if element at l index is the target
    return l;
  } else {
    return -1; // Return -1 if target was not found
  }
}
```

### Explanation

This JavaScript function lastIndex(arr, target) is designed to find the last occurrence of a target value in a sorted array using a variation of the binary search algorithm.

Parameters:

**arr**: a sorted array of values
**target**: the value to search for in the array

The function maintains two pointers, **l** (left) and **r** (right), which initially point to the beginning and the end of the array, respectively.

The function enters a loop that continues as long as the left pointer is less than the right pointer. Inside this loop:

It first calculates the middle index mid of the current search range. This is done by taking the average of the left and right pointers and rounding up to the nearest integer. The rounding up ensures that the middle index moves to the right when there are only two elements left to check, preventing the loop from getting stuck.

If the value at the middle index is less than or equal to the target, it means the target (if it exists in the array) must be at the middle index or somewhere to the right. So, the function moves the left pointer to the middle index.

If the value at the middle index is greater than the target, it means the target (if it exists in the array) must be somewhere to the left of the middle index. So, the function moves the right pointer to one position to the left of the middle index.

After the loop ends, the left pointer l is at the index of the last occurrence of the target value (or at the index where the target would be inserted if it's not in the array). So, the function checks if the value at this index is the target. If it is, it returns the index. If it's not, it means the target is not in the array, so the function returns -1.

Overall, this function is an efficient way to find the last occurrence of a target value in a sorted array, as it reduces the number of elements it needs to check by half in each iteration of the loop. Its time complexity is O(log n), where n is the number of elements in the array.
