# Task: #10. Equal

## Tags: 

JavaScript

## Difficulty:

Easy

## Question:

What does the code snippet output by console.log?

## Explanation:

```javascript
console.log(0 == false)          // true - boolean converts to number false = 0 -> 0 == 0 = true
console.log('' == false)         // true - boolean and string convert to number -> 0 == 0 = true
console.log([] == false)         // true - array converts to string ('') and then to number (0) -> 0 == 0 = true
console.log(undefined == false)  // false - undefined only == to undefined or null
console.log(null == false)       // false - null only == to null or undefined
console.log('1' == true)         // true - both convert to number -> 1 == 1 = true
console.log(1n == true)          // true - same as above
console.log(' 1     ' == true)   // true - same as above plus trim
```

## Answer:

```javascript
true
true
true
false
false
true
true
true
```
