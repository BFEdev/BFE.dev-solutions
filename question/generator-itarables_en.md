> This answer is provided by [@hello-foma](https://github.com/hello-foma)

**Iterators** is a special interface that JS can use to iterate through objects or data structures' members.

Iterator functions can be assigned to an object by `[Symbol.iterator]` or `[Symbol.asyncIterator]` symbols.

When created, the **Iterator** must return an object with the `next()` method, which provides the next value of an iterable object.

```JavaScript
// Object with iterator function
const range = {
  from: 1,
  to: 5,

  // for..of range calls this method once in the very beginning
  [Symbol.iterator]() {
    let current = this.from;
    let last = this.to;
    
    return {
      // next() is called on each iteration by the for..of loop
      next: () => {
        // it should return the value as an object {done:.., value :...}
        if (current <= last) {
          return { done: false, value: current++ };
        } else {
          return { done: true };
        }
      }
    };
  }
};

for(let value of range) {
  console.log(value); // 1 then 2, then 3, then 4, then 5
}
```

**Generators** is a special type of function at JS that is marked with the `*` symbol. Generator functions simplify the process of declaring and using `Iterators`. When called, **Generator** creates an `Iterator`. Generator functions have the special keyword `yield`.

Using `yield` is similar to `return` at functions. But the `yield` doesn't break the execution of a function. Instead, it pauses the function and provides a value. When the iterator will be called next time, it continues execution from this yield position.

```JavaScript
//  generator funciton
function* generateSequence(start, end) {
  for (let i = start; i <= end; i++) yield i;
}

const iterator = generateSequence(1, 5);

console.log(iterator.next()); // {value: 1, done: false}

console.log([...iterator]); // [2,3,4,5]

console.log(iterator.next()) // {value: undefined, done: true}
```

See reference: [Async iteration and generators](https://javascript.info/async-iterators-generators)

Would you like to [contribute to the solution](https://github.com/BFEdev/BFE.dev-solutions/blob/main/question/generator-itarables_en.md)? [Contribute guideline](https://github.com/BFEdev/BFE.dev-solutions#how-to-contribute)
