> This awesome solution is created by [@yanina-nas](https://github.com/yanina-nas) !

## Goal
Our goal is to implement a function that:
- takes one parameter of type any
- returns a copy of it where all `undefined` values are replaced by `null`

## Observations
We can make several observations here:
- We don't know from the prompt how deeply nested our `undefined` can be, so our function must be recursive (although this might be not memory safe at all if there are thousands of levels of nesting). 
- We need to return a copy with all `undefined` replaced with `null`, so we cannot mutate our original input.

## Implementation
First, we need to understand what can be passed to our function. 
There are three cases we care about,
our input is:
- `undefined`
- an array or an object (that can potentially have `undefined` inside)
- anything else (that doesn't fall into one of the cases above)

_Case 1. `undefined`_

We handle `undefined` case by returning `null` instead.

```js
function undefinedToNull(arg) {
  if (arg === undefined) {
    return null;
  }
}
```


_Case 2A. An array_

We use the `Array.prototype.map()` method to create a new array populated with the results of calling our `undefinedToNull` function on every element in our original array.

```js
function undefinedToNull(arg) {
  if (arg === undefined) {
    return null;
  }
  else if (Array.isArray(arg)) {
    return arg.map(undefinedToNull)
  }
}
```


_Case 2A. An object_

To create a new object, we can use `Array.prototype.reduce()` method on an array of keys of our object, while passing an empty object as an accumulator to `reduce`, and using the spread operator `...` to populate our new object with original keys and values returned by our `undefinedToNull` function.

```js
function undefinedToNull(arg) {
  if (arg === undefined) {
    return null;
  }
  else if (Array.isArray(arg)) {
    return arg.map(undefinedToNull)
  }
  else if (Object.prototype.toString.call(arg) === '[object Object]') {
    return Object.keys(arg).reduce((acc, currentKey) => ({
      ...acc,
      [currentKey]: undefinedToNull(arg[currentKey]),
    }), {});
  }
}
```

_Case 3. Anything else_

Given anything else, just return it. 
And we get our solution.

## Complete solution code

```js
function undefinedToNull(arg) {
  if (arg === undefined) {
    return null;
  }
  else if (Array.isArray(arg)) {
    return arg.map(undefinedToNull)
  }
  else if (Object.prototype.toString.call(arg) === '[object Object]') {
    return Object.keys(arg).reduce((acc, currentKey) => ({
      ...acc,
      [currentKey]: undefinedToNull(arg[currentKey]),
    }), {});
  }
  return arg;
}
```
