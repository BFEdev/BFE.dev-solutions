# Task: #5. block scope

## Question:

What does the code snippet output by console.log?


## Explanation:

```javascript
for (var i = 0; i < 5; i++) {
  setTimeout(() => console.log(i), 0);
}
// VAR doesn't have block scope. FOR will log: 5 5 5 5 5

for (let i = 0; i < 5; i++) {
  setTimeout(() => console.log(i), 0);
}
// LET has block scope. FOR will log: 0 1 2 3 4 
```

## Answer:

```javascript
5
5
5
5
5
0
1
2
3
4
```
