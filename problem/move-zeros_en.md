> Created by [@manjeet2k](https://github.com/manjeet2k)

## Move Zeroes

### Explanation

The problem is to move all zeroes in an array to the end while maintaining the relative order of the non-zero elements.

### Approach Explanation:

1. **Initialization**: We start by initializing a pointer called `nonZeroPointer` to keep track of the position where a non-zero element can be placed.

2. **Iterating through the array**: We loop through the array using a `for` loop. For each element:

    - If the element is non-zero, we move it to the position indicated by the `nonZeroPointer` and then increment the `nonZeroPointer`. This effectively moves all non-zero elements to the front of the array while maintaining their relative order.

3. **Filling remaining positions with zeroes**: After moving all non-zero elements to the front, we iterate again from the `nonZeroPointer` position till the end of the array and fill all remaining positions with zeroes.


#### Code

```javascript
/**
 * Function to move all zeroes to the end of the array while maintaining
 * the relative order of the non-zero elements.
 * @param {number[]} nums - Array of integers
 */
function moveZeroes(nums) {
    // Initialize a pointer to keep track of the position where a non-zero element
    // can be placed
    let nonZeroPointer = 0;

    // Loop through the array
    for (let i = 0; i < nums.length; i++) {
        // If the current element is not zero
        if (nums[i] !== 0) {
            // Move the non-zero element to the position indicated by the non-zero pointer
            nums[nonZeroPointer] = nums[i];
            // Increment the non-zero pointer to prepare for the next non-zero element
            nonZeroPointer++;
        }
    }

    // After moving all non-zero elements to the front, fill the remaining positions
    // with zeroes starting from the position indicated by the non-zero pointer
    for (let i = nonZeroPointer; i < nums.length; i++) {
        nums[i] = 0;
    }
}

// Example usage:
const nums = [0, 1, 0, 3, 12];
moveZeroes(nums);
console.log(nums); // Output: [1, 3, 12, 0, 0]
