# Valid Parentheses Checker

This code implements a solution to check if a given string containing different types of brackets is valid or not. It uses a stack data structure to maintain the order of opening and closing brackets and ensure their validity.

You can watch a video explanation of this solution [here](https://youtu.be/lwN4SDGTBaY?si=HTgiwVLHvQjaxMuK).

## Explanation

The solution employs a stack to manage the opening and closing brackets. The algorithm iterates through the characters of the input string, and for each character:

1. If it's an opening bracket, it's pushed onto the stack.
2. If it's a closing bracket, the stack's top element is popped and compared to the expected opening bracket based on the `mapping` dictionary.
   - If they don't match, the brackets are not valid.
3. If the character is neither an opening nor a closing bracket, it's ignored.

After iterating through all characters, the stack should be empty if all brackets are properly matched. Otherwise, it's not a valid string.

```javascript
function isValid(s) {
    const stack = [];  // Initialize an empty stack to store opening brackets
    const mapping = { ')': '(', '}': '{', ']': '[' };  // Mapping of closing brackets to their corresponding opening brackets

    for (const char of s) {  // Iterate through each character in the input string
        if (mapping[char]) {  // If the character is a closing bracket
            const top = stack.pop();  // Pop the top element from the stack (last opening bracket)
            if (mapping[char] !== top) {  // Check if the closing bracket corresponds to the popped opening bracket
                return false;  // Brackets are not valid
            }
        } else {  // If the character is an opening bracket
            stack.push(char);  // Push it onto the stack
        }
    }
    
    return stack.length === 0;  // If the stack is empty, all brackets are valid; otherwise, the string is not valid
}

// Test cases
console.log(isValid("()"));        // Output: true
console.log(isValid("()[]{}"));    // Output: true
console.log(isValid("(]"));        // Output: false
```

## Time and Space Complexity

The time complexity of this solution is O(n), where 'n' represents the length of the input string. The algorithm iterates through each character in the string exactly once, and the operations performed within each iteration are constant time operations.

The space complexity is also O(n). The space used by the stack is proportional to the length of the input string in the worst case, as it stores opening brackets. The mapping dictionary occupies a constant amount of space, irrespective of the input size.
