function uncompress(compressedString) {
    // Create an empty stack to store characters and intermediate results
    let stack = [];
  
    // Iterate over each character in the compressedString
    for (let char of compressedString) {
        // If the current character is ')', it indicates the end of a compressed substring
        if (char === ')') {
            let str = ''; // Initialize an empty string to store the substring
            // Pop characters from the stack until '(' is encountered
            while (stack.length > 0 && stack[stack.length - 1] !== '(') {
                str = stack.pop() + str; // Concatenate characters to form the substring
            }
            stack.pop(); // Remove the '(' from the stack

            let num = ''; // Initialize an empty string to store the multiplier
            // Pop characters from the stack while they are numeric digits
            while (stack.length > 0 && !isNaN(stack[stack.length - 1])) {
                num = stack.pop() + num; // Concatenate digits to form the multiplier
            }
            const multiplier = parseInt(num); // Parse the multiplier as an integer
            stack.push(str.repeat(multiplier)); // Repeat the substring and push it back to the stack
        } else {
            stack.push(char); // If the character is not ')', push it to the stack
        }
    }

    // Join all characters in the stack to form the decompressed string
    return stack.join('');
}

// Example usage
console.log(uncompress('3(ab)')); // Output: "ababab"
