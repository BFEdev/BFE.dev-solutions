## Problem Analysis

Suppose we are NOT familiar with [Currying](https://en.wikipedia.org/wiki/Currying), 
let's take a close look at the example.

```ts
const join = (a, b, c) => {
   return `${a}_${b}_${c}`
}

const curriedJoin = curry(join)

curriedJoin(1, 2, 3) // '1_2_3'
curriedJoin(1)(2, 3) // '1_2_3'
curriedJoin(1, 2)(3) // '1_2_3'
```

We can gather the following requirments from above.

1. `curry()` should return a function
2. The returned function should 
    - if number of arguments matches original function, return the final result
function
    - otherwise return a function which expects the missing arguments, 
also this function needs to be currieds as well.

We need to know **the expected number of arguments of a function**, 
[Function.prototype.length](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/length) exactly
serve this purpose.

## Implementation attemp 1 - Function.prototype.bind() on original function

Let's start with the basic skeleton, 
[Spread operator (...)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)
allows us to handle the arguments easily.

```ts
function curry(fn) {
  return function(...args) {
    // if number of arguments match
    if (args.length >= fn.length) {
      // use `fn.call()` or `fn.apply()` to preserve `this`.
      return fn.call(this, ...args)
    } 
    // TODO
  }
}
```

Pay attention to the `fn.call(this, ...args)` this is to preserve the `this`. 
You can try out our JavaScript quizzes about `this` on BFE to get familiar with it(
[19. this](/quiz/this), 
[33. this II](/quiz/this-II),
[41. this III](/quiz/this-III),
[49. this IV](/quiz/this-4),
[97. this V](/quiz/this-V)).


Above code supports the first example `curriedJoin(1, 2, 3)`. 

Now comes the tricky part, 
how can we return a function which expects the missing arguments ? 

Notice that we need to insert the `args` to the arguments of this new function 
in order to get the final result,
so [Function.prototype.bind()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind) 
might be able to help, since it allows us to pre-fill the args. 

Once nice thing about `bind()` is that it reflects the right 
`Function.prototype.length`.

```ts
function fn(a, b, c) {}
console.log(fn.length) // 3

const boundFn = fn.bind(null, 1)
console.log(boundFn.length) // 2
```

With this we have a working solution as below.

```ts
function curry(fn) {
  return function(...args) {
    // if number of arguments match
    if (args.length >= fn.length) {
      return fn.call(this, ...args)
    } 
    // create a new function which expects right number of arguments
    // and then curry it
    const boundFn = fn.bind(this, ...args)
    return curry(boundFn)
  }
}
```


## Implementation attemp 2 - improved approach

For the above approach, we create a new function `boundFn` and 
`curry(boundFn)` at last line.

But we are already inside of `curry()`, why don't we just `bind()` the first returned function ? 

In order to do this, we need give the returned function a name. 

```ts
function curry(fn) {
  return function curried(...args) {
    // if number of arguments match
    if (args.length >= fn.length) {
      return fn.call(this, ...args)
    } 
    // just return a bounded curried() with args pre-filled
    return curried.bind(this, ...args)
  }
}
```

This is simpler and is basically the same as the code below.

```ts
function curry(fn) {
  return function curried(...args) {
    // if number of arguments match
    if (args.length >= fn.length) {
      return fn.call(this, ...args)
    } 
    return function(...missingArgs) {
      return curried.call(this, ...args, ...missingArgs)
    }
  }
}
```









