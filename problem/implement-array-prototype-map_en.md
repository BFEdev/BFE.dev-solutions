The function in this code takes two parameters - the callback function and `this` argument. We assign `this` to a new variable called array.
We use forEach to loop over the calling array because forEach will skip the empty elements. We call the callback function using the `.call` method
so that we can pass the this argument. We assign the output of the function to the index in the output so that the output has the corresponding
empty elements too. For example, if `this` has empty elements at index 2 and 4, the output too will have empty elements at the same indices.

```javascript
  Array.prototype.myMap = function(callbackFn, thisArg) {
  const array = this;
  const output = [];

  this.forEach((element, index) => {
    output[index] = callbackFn.call(thisArg, element, index, array);
  });
  return output;
}
```