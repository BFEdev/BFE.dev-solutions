This is a follow-up on [1. implement curry()](https://bigfrontend.dev/problem/implement-curry), so please first be familiar with our [solution to 1. implement curry()](https://bigfrontend.dev/problem/implement-curry/solution) first.

## Problem Analysis

In the previous question, we just append the new arguments in the order of occurance.

```js
curriedJoin(1)(2)(3) // '1_2_3'
curriedJoin(3)(2)(1) // '3_2_1'
```

Now we are asked to implement a placeholder which allows us to change this behavior.

```js
curriedJoin(_, 2)(1, 3) // '1_2_3'
```

What this means is that **the latter arguments will fill the placeholders first before getting appended**. So we put `1` in the position of first placeholder, above example now equals to

```js
curriedJoin(1, 2)(3) // '1_2_3'
```

Accordingly, for the last example we can get following steps till the result

```js
curriedJoin(_, _, _)(1)(_, 3)(2)
// 1 gets placed at 1st placeholder
curriedJoin(1, _, _)(_, 3)(2)
// 3 gets placed at the 2nd placeholder
curriedJoin(1, _, 3)(2)
// 2 gets placed at the 1st placeholder
curriedJoin(1, 2, 3) // '1_2_3'
```

## What is missing in previous solution we had?

Below is the original approach to [previous question](https://bigfrontend.dev/problem/implement-curry/solution). We chose the last one because we cannot handle args merging in `Function.prototype.bind()`.

```js
function curry(fn) {
  return function curried(...args) {
    // if number of arguments match
    if (args.length >= fn.length) {
      return fn.call(this, ...args)
    }
    return function (...nextArgs) {
      return curried.call(this, ...args, ...nextArgs)
    }
  }
}
```

Since there are placeholders in the arguments now, the first if branch doesn't work any more,
and we need to merge the arguments, let's create some skeleton with some TODO

> Separating logic in different functions not only makes your code easier to read,
> but it also sometimes saves you time in a real interview.

```js
function curry(fn) {
  return function curried(...args) {
    // if number of arguments match
    if (isArgsMet(args, fn, curry.placeholder)) {
      return fn.call(this, ...args)
    }
    return function (...nextArgs) {
      const mergedArgs = mergeArgs(args, nextArgs)
      return curried.call(this, ...mergedArgs)
    }
  }
}
curry.placeholder = Symbol()

function isArgsMet(args, fn, placeholder) {
  // TODO
}

function mergeArgs(argsTo, argsFrom) {
  // TODO
}
```

Now let's implement these 2 util functions.

## isArgsMet()

For `isArgsMet()`, it should be straightforward.

```ts
function isArgsMet(args, fn, placeholder) {
  if (args.length < fn.length) {
    return false
  }

  for (let i = 0; i < fn.length; i++) {
    if (args[i] === placeholder) {
      return false
    }
  }

  return true
}
```

It costs linear time `O(n)` and constant space `O(1)`.

Alright, above code could also be simplified with `Array.prototype.slice()` and `Array.prototype.every()`.

```ts
function isArgsMet(args, fn, placeholder) {
  if (args.length < fn.length) {
    return false
  }

  return args.slice(0, fn.length).every((item) => item !== placeholder)
}
```

Because of slicing, it costs linear time `O(n)` and linear space `O(n)`

## mergeArgs()

The approach would be simple:

1. create a new array to hold the result
2. use 2 cursors to traverse both list
   1. if item in `argsTo` is placeholder, use the item in `argsFrom`
   2. otherwise use the item in `argsTo`
3. push the rest items in `argsFrom` to the result array.

Pay attention that we need to return a new list rather than modify the `argsTo` inline, since the function might be called multiple times, think about cases like below:

```js
const curried4 = curried(4)
curried4(3)
curried4(2) // if we modify args inline, this would generate wrong result
```

Here is the code.

```js
function mergeArgs(argsTo, argsFrom, placeholder) {
  const result = []
  let indexFrom = 0
  let indexTo = 0
  while (indexTo < argsTo.length || indexFrom < argsFrom.length) {
    // push the rest times of `argsFrom` if `argsTo` is done traversal
    if (indexTo >= argsTo.length) {
      result.push(argsFrom[indexFrom])
      indexFrom += 1
      continue
    }

    if (indexFrom >= argsFrom.length) {
      result.push(argsTo[indexTo])
      indexTo += 1
      continue
    }

    if (argsTo[indexTo] === placeholder) {
      result.push(argsFrom[indexFrom])
      indexFrom += 1
    } else {
      result.push(argsTo[indexTo])
    }
    indexTo += 1
  }

  return result
}
```

The time complexity is linear `O(m + n)` since elements in both list are traversed once.

[@nexus](https://bigfrontend.dev/user/nexus) has put a much simpler way of same logic with usage of `Array.prototype.map()`. The original solution is [here](https://bigfrontend.dev/problem/implement-curry-with-placeholder/discuss/271), but we'll adapt the lines to code blow.

The idea is to loop through `argsTo` as base instead, each time a placeholder is found shift the first item from `argsFrom` and place it in `argsTo`. Append the rest items in `argsFrom` at the end.

```ts
function mergeArgs(argsTo, argsFrom, placeholder) {
  const mappedArgsTo = argsTo.map((item) =>
    item === placeholder ? argsFrom.shift() : item
  )
  return [...mappedArgsTo, ...argsFrom]
}
```

This piece of code is more preferrable, easy to understand but with some trivial differences in time and space complexity.

1. `Array.prototype.shift()` is not constant time, it requires some index shifting. If internal implementation is linked list, then it is constant, but generally we should not have such assumption. We can avoid the issue by use `reverse()` + `pop()` instead.
   > This is a trivial issue, but you should mention this during interview
2. If we ignore above issue, the overal time complexity is still `O(m + n)`
3. We need extra linear O(n) auxiliary space to hold the intermediate result.

## Implementation 1 - longer version.

Now we can put the code together to form a working solution.

```js
function curry(fn) {
  return function curried(...args) {
    // if number of arguments match
    if (isArgsMet(args, fn, curry.placeholder)) {
      return fn.call(this, ...args)
    }

    // otherwise return a function which merges the args
    return function (...nextArgs) {
      const mergedArgs = mergeArgs(args, nextArgs, curry.placeholder)
      return curried.call(this, ...mergedArgs)
    }
  }
}
curry.placeholder = Symbol()

function mergeArgs(argsTo, argsFrom, placeholder) {
  const result = []
  let indexFrom = 0
  let indexTo = 0
  while (indexTo < argsTo.length || indexFrom < argsFrom.length) {
    // push the rest times of `argsFrom` if `argsTo` is done traversal
    if (indexTo >= argsTo.length) {
      result.push(argsFrom[indexFrom])
      indexFrom += 1
      continue
    }

    if (indexFrom >= argsFrom.length) {
      result.push(argsTo[indexTo])
      indexTo += 1
      continue
    }

    if (argsTo[indexTo] === placeholder) {
      result.push(argsFrom[indexFrom])
      indexFrom += 1
    } else {
      result.push(argsTo[indexTo])
    }
    indexTo += 1
  }

  return result
}

function isArgsMet(args, fn, placeholder) {
  if (args.length < fn.length) {
    return false
  }

  for (let i = 0; i < fn.length; i++) {
    if (args[i] === placeholder) {
      return false
    }
  }

  return true
}
```

## Implementation 2 - A more compact solution

```js
function curry(fn) {
  return function curried(...args) {
    // if number of arguments match
    if (isArgsMet(args, fn, curry.placeholder)) {
      return fn.call(this, ...args)
    }

    // otherwise return a function which merges the args
    return function (...nextArgs) {
      const mergedArgs = mergeArgs(args, nextArgs, curry.placeholder)
      return curried.call(this, ...mergedArgs)
    }
  }
}
curry.placeholder = Symbol()

function mergeArgs(argsTo, argsFrom, placeholder) {
  const mappedArgsTo = argsTo.map((item) =>
    item === placeholder && argsFrom.length ? argsFrom.shift() : item
  )
  return [...mappedArgsTo, ...argsFrom]
}

function isArgsMet(args, fn, placeholder) {
  if (args.length < fn.length) {
    return false
  }

  return args.slice(0, fn.length).every((item) => item !== placeholder)
}
```

If you prefer more compact code, you can rewrite above code into a few lines below.

```js
function curry(fn) {
  return function curried(...args) {
    // if number of arguments match
    if (
      args.length >= fn.length &&
      args.slice(0, fn.length).every((item) => item !== curry.placeholder)
    ) {
      return fn.call(this, ...args)
    }

    // otherwise return a function which merges the args
    return function (...nextArgs) {
      const mappedArgsTo = args.map((item) =>
        item === curry.placeholder && nextArgs.length ? nextArgs.shift() : item
      )
      return curried.call(this, ...mappedArgsTo, ...nextArgs)
    }
  }
}
curry.placeholder = Symbol()
```
