> This awesome solution is created by [@gsriram24]([https://github.com/pinglu85](https://github.com/gsriram24)) !


In this solution, we use the concept of bitwise operators and `Array.prototype.reduce` function to get the only element that is available.

### XOR Bitwise operator in Javascript

In JavaScript, the bitwise XOR(^) Operator is used to compare two operands and return a new binary number which is 1 if both the bits in operators are different and 0 if both the bits in operads are the same. The operation is represented by the `^` symbol. This operator can be used to find the missing numbers in an array of natural numbers.

Eg. 
```
     9 (base 10) = 00000000000000000000000000001001 (base 2)
    14 (base 10) = 00000000000000000000000000001110 (base 2)
                   --------------------------------
14 ^ 9 (base 10) = 00000000000000000000000000000111 (base 2) = 7 (base 10)
```

Read more about bitwise XOR [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_XOR)

We can use this to determine which of the numbers have a missing counterpart in our array using the following steps. 

1. Have a result variable initialized to 0. 
2. Iterate through each element and apply the XOR operation between the result and the element. 
3. Return the result element. 

Let's take a simple example to better understand these steps. 

`arr = [2,1,2]`

`result = 0`

Iteration 1: Element: `2`. Binary representation: `10`.  `result = result ^ element => 00 ^ 10 => 10 => 2`.
Iteration 2: Element: `1`. Binary representation: `01`.  `result = result ^ element => 10 ^ 01 => 11 => 3`.
Please stay with me here, although 3 is not present in the array, we will cancel it out. :)
Iteration 3: Element: `2`. Binary representation: `10`.  `result = result ^ element => 11 ^ 10 => 01 => 1`.

Now we return the result of 1. 

The javascript implementation of this using `Array.prototype.forEach` will be as follows: 

```javascript
/**
 * @param {number[]} arr
 * @returns number
 */
function findSingle(arr) {
  let result = 0;
  arr.forEach(el => result = result ^ el)
  return result;
}
```
This is a very valid simple 3-liner solution to solve this problem. But we can simplify this further.

### Array.prototype.reduce

The Javascript `arr.reduce()` method in JavaScript is used to reduce the array to a single value and executes a provided function for each value of the array (from left to right) and the return value of the function is stored in an accumulator. 

In our case, the accumulator is the `result` variable. And we would xor it with itself each iteration.

Read more about `Array.prototype.reduce` [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)

### Final implementation
```javascript
/**
 * @param {number[]} arr
 * @returns number
 */
function findSingle(arr) {
  return arr.reduce((acc, value) => acc ^= value, 0)
}
```

### Time and space complexity
The time complexity of this approach is `O(n)` as we would be iterating through the entire array once.
The space complexity of this approach is `O(1)` as we don't need any additional space.
