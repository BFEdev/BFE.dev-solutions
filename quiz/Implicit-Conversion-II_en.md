# Task: #11. Implicit Coercion II

## Tags: 

JavaScript

## Difficulty:

Easy

## Question: 

What does the code snippet output by console.log?

## Explanation:

```javascript
console.log([] + [])        // "" - both convert to string because of "+" operator -> "" + "" = ""
console.log([] + 1)         // "1" - same as above -> "" + 1 = "1"
console.log([[]] + 1)       // "1" - same as above -> "" + 1 = "1"
console.log([[1]] + 1)      // "11" - same as above -> "1" + 1 = "11"
console.log([[[[2]]]] + 1)  // "21" - same as above -> "2" + 1 = "21"
console.log([] - 1)         // -1 - both convert to number because of "-" operator -> 0 - 1 = -1
console.log([[]] - 1)       // -1 - same as above -> 0 - 1 = -1
console.log([[1]] - 1)      // 0 - same as above -> "1" - 1 = 0
console.log([[[[2]]]] - 1)  // 1 - same as above -> "2" - 1 = 1
console.log([] + {})        // "[object Object]" - both convert to string -> "" + "[object Object]" = "[object Object]"
console.log({} + {})        // "[object Object][object Object]" - same as above -> "[object Object]" + "[object Object]" = "[object Object][object Object]"
console.log({} - {})        // NaN - "[object Object]" converts to number (wrong - is not a number) -> NaN
```

## Answer:

```javascript
""
"1"
"1"
"11"
"21"
-1
-1
0
1
"[object Object]"
"[object Object][object Object]"
NaN
```
