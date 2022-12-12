## Recursion is the first choice

For questions of arrays and strings, it is very likely we can find a recursion approach. It is quite important to come up with some ideas during interview, and then try to improve the solution.

In order to find any solution, a useful technique is to start with simple cases.

```ts
flat([1, 2, 3, 4])
// [1, 2, 3, 4]
```

If all items are not array, then we don't need to do anything, just a call of `Array.prototype.map()` will do.

```ts
flat([1, [2], 3, 4])
```

Now there an item in array - `[2]`, we need to flatten it first, which could be done by calling `flat()` again. This could obviously be done by recursion.

```js
function flat(arr, depth = 1) {
  const result = []

  // for each item,
  // if it is an array, flatten it first then add it to the result
  // if not an array, just add it to result
  for (const item of arr) {
    if (Array.isArray(item) && depth > 0) {
      const flattened = flat(item, depth - 1)
      for (const item of flattened) {
        result.push(item)
      }
    } else {
      result.push(item)
    }
  }

  return result
}
```

Above form is a perfect case for `Array.prototype.reduce()`, we can make it more concise.

```js
function flat(arr, depth = 1) {
  return arr.reduce((result, item) => {
    if (Array.isArray(item) && depth > 0) {
      result.push(...flat(item, depth - 1))
    } else {
      result.push(item)
    }
    return result
  }, [])
}
```

For time complexity, each item and bracket pair is traversed once so it is linear to the count of them.

For space complexity, we have a recursion stack and intermediate result array, which in total linear space.

## Iteration approach after recursion - naive approach

Recursion is easy to come up with and easy to understand, after it is done we can start thinking about the improve it with memoization .etc, or try to come up with an iterative solution. Iteration approach is generally more performant since there is no recursion stack, but sometimes it is more tricky to get it right.

A simple idea is we can flatten the array with depth=1 for multiple times.

```js
function flat(arr, depth = 1) {
  let result = arr.slice(0)
  while (depth-- > 0) {
    // get the items from head
    // flatten them if array
    // push them back at the end
    let count = result.length
    while (count-- > 0) {
      const head = result.shift()
      if (Array.isArray(head)) {
        result.push(...head)
      } else {
        result.push(head)
      }
    }
  }
  return result
}
```

This causes error is depth is passed with `Infinity`, since we loop through the array over and over again even if there is no array inside, to overcome this we can use a flat to end it more quickly.

```js
function flat(arr, depth = 1) {
  let result = arr.slice(0)
  let hasArrayItem = true
  while (depth-- > 0 && hasArrayItem) {
    hasArrayItem = false
    let count = result.length
    while (count-- > 0) {
      const head = result.shift()
      if (Array.isArray(head)) {
        hasArrayItem = true
        result.push(...head)
      } else {
        result.push(head)
      }
    }
  }
  return result
}
```

## Iteration approach - improved version

Above code works, but still not performant, we traverse through the items that are not arrays unnecessarily. Think about case like below, to flatten the inner array, `1` `3` and `5` are looped 4 times.

```
[1, 2, 3, [[[[4]]]], 5]
```

We need a way to narrow down the traversal to inner arrays. In above code, we always traverse the whole array by shifting and pushing, we can avoid unnecessary traversal by **only push when the item is done flattening**.

So rather than pushing right away, we can put it back at the head if we need to flatten the item furthur. Since there might be more array items when we put it back, we need to attach the `depth` to the items as well.

Since we are using one single result array, we need to know when to end the traversal.

```js
function flat(arr, depth = 1) {
  // attach depth to each item
  const result = arr.map((item) => [item, depth])

  // add a terminator
  const end = Symbol('end')
  result.push(end)

  while (result.length > 0) {
    const head = result.shift()
    if (head === end) {
      break
    }
    const [item, itemStep] = head
    // if item is an array and we can flatten furthur
    // then put its items back with a less depth
    if (Array.isArray(item) && itemStep > 0) {
      result.unshift(...item.map((arrayItem) => [arrayItem, itemStep - 1]))
    } else {
      result.push(item)
    }
  }

  return result
}
```

## Iteration approach - shift is expensive

One problem of above problem is `shift()` and `unshift()`. In a sense of time complexity, these 2 options could be expensive in array since the items needs to be re-indexed. Well you can argue that this depends on the internal implementation of Array, but avoiding them with a possible better solution would definitely shine yourself in real interviews.

Since `push()` and `pop()` doesn't require re-indexing, we can rewrite above with a stack.

```js
function flat(arr, depth = 1) {
  const stack = arr.map((item) => [item, depth])
  const result = []

  while (stack.length > 0) {
    const [item, itemDepth] = stack.pop()
    // if item is an array and we need to flatten further
    // push its items back with a smaller depth
    if (Array.isArray(item) && itemDepth > 0) {
      stack.push(...item.map((arrayItem) => [arrayItem, itemDepth - 1]))
    } else {
      result.push(item)
    }
  }

  // the result array is in reversed order we need to reverse it back
  return result.reverse()
}
```

For time complexity, each round we remove an item or a bracket pair from the stack, so time cost is linear to the count of them. (The final `reverse()` is linear to the count of items, which doesn't affect the overall complexity)

For space complexity, we used a stack so also linear.
