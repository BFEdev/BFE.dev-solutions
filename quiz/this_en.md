# Task: #19. `this`

## Tags: 

JavaScript

## Difficulty:

Easy

## Question:

What does the code snippet output by console.log?

## Explanation:

```javascript
const obj = {
    a: 1,
    b: function() {
      console.log(this.a)
    },
    c() {
      console.log(this.a)
    },
    d: () => {
      console.log(this.a)
    },
    e: (function() {
      return () => {
        console.log(this.a);
      }
    })(),
    f: function() {
      return () => {
        console.log(this.a);
      }
    }
  }
  
  console.log(obj.a)  // 1
  obj.b()             // 1
  ;(obj.b)()          // 1
  const b = obj.b
  b()                 // undefined - reference was lost
  obj.b.apply({a: 2}) // 2
  obj.c()             // 1
  obj.d()             // undefined - arrow function doesn't have own 'this'
  ;(obj.d)()          // undefined - same as above
  obj.d.apply({a:2})  // undefined - same as above
  obj.e()             // undefined - same as above
  ;(obj.e)()          // undefined - same as above
  obj.e.call({a:2})   // undefined - same as above
  obj.f()()           // 1 - because of closure 
  ;(obj.f())()        // 1 - same as above
  obj.f().call({a:2}) // 1 - closure already did lexical environment
  ```

## Answer:

```javascript
1
1
1
undefined
2
1
undefined
undefined
undefined
undefined
undefined
undefined
1
1
1
```
