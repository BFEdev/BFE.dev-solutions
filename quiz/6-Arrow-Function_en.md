# Task: #6. Arrow Function

## Question:

What does the code snippet output by console.log?


## Explanation:

```javascript
const obj = {
  dev: "bfe",
  a: function () {
    return this.dev;
  },
  b() {
    return this.dev;
  },
  c: () => {
    return this.dev;
  },
  d: function () {
    return (() => {
      return this.dev;
    })();
  },
  e: function () {
    return this.b();
  },
  f: function () {
    return this.b;
  },
  g: function () {
    return this.c();
  },
  h: function () {
    return this.c;
  },
  i: function () {
    return () => {
      return this.dev;
    };
  },
};

console.log(obj.a());   // "bfe"
console.log(obj.b());   // "bfe"
console.log(obj.c());   // undefined - arrow func doesn't have context => window.dev === undefined
console.log(obj.d());   // "bfe" - arrow func in IIFE has context this
console.log(obj.e());   // "bfe" 
console.log(obj.f()()); // undefined - reference to func will lose context
console.log(obj.g());   // undefined - 'c' doesn't have contex and 'g' same
console.log(obj.h()()); // undefined - 'c' doesn't have contex and 'h' same
console.log(obj.i()()); // "bfe" - arrow func inside function declaration 
```

## Answer:

```javascript
"bfe"
"bfe"
undefined
"bfe"
"bfe"
undefined
undefined
undefined
"bfe"
```
