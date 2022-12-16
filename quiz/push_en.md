[Array.prototype.push](https://tc39.es/ecma262/#sec-array.prototype.push) returns the new length of the array.

```js
const a = [1,2,3]
const b = a.push(4) // new length is 4, so b is 4
const c = b.push(5) // there is no `push()` method on Number so Error(TypeError) is thrown
console.log(c)
```

Below is the detailed [spec](https://tc39.es/ecma262/#sec-array.prototype.push)

> 1. Let _O_ be ? ToObject(this value).
> 2. Let _len_ be ? LengthOfArrayLike(_O_).
> 3. Let _argCount_ be the number of elements in items.
> 4. If _len_ + _argCount_ > 2 ^ 53 - 1, throw a TypeError exception.
> 5. For each element E of items, do
>    a. Perform ? Set(O, ! ToString(𝔽(_len_)), E, true).
>    b. Set _len_ to _len_ + 1.
> 6. Perform ? Set(_O_, "length", 𝔽(_len_), true).
> 7. Return 𝔽(_len_).
