# Task: #9. null and undefined

## Question:

What does the code snippet output by console.log?

## Explanation:

```javascript
console.log(JSON.stringify([1,2,null,3]))       // "[1,2,null,3]"
console.log(JSON.stringify([1,2,undefined,3]))  // "[1,2,null,3]" - JSON doesn't have undefined value
console.log(null === undefined)                 // false - null == 0 and undefined == NaN
console.log(null == undefined)                  // true - the rule: null olny == null/undefined
console.log(null == 0)                          // false - the rule: null olny == null/undefined
console.log(null < 0)                           // false - 0 < 0
console.log(null > 0)                           // false - 0 > 0
console.log(null <= 0)                          // true - 0 <= 0
console.log(null >= 0)                          // true - 0 >= 0
console.log(undefined == 0)                     // false - the rule: undefined olny == undefined/null
console.log(undefined < 0)                      // false - NaN < 0
console.log(undefined > 0)                      // false - NaN > 0
console.log(undefined <= 0)                     // false - NaN <= 0
console.log(undefined >= 0)                     // false - NaN >= 0
```

## Answer:

```javascript
"[1,2,null,3]"
"[1,2,null,3]"
false
true
false
false
false
true
true
false
false
false
false
false
```
