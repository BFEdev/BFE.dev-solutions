## TL;DR

For `==` it loosly checks equality with rules including:

1. `null` and `undefined` are equal
2. for `string` and `number`, string is converted to `number` first
3. for `bigint` and `string`, string is converted to `bigint` first
4. if either is `boolean`, `boolean` is converted to `number`
5. if either is primitive and the other is Object, convert the Object to primitive first

Above is not a full list, please refer to the detailed spec explanation in the section after this.

```js
console.log(0 == false)
// true
// booleans are converted to number first, 0 == 0
console.log('' == false)
// true
// booleans are converted to number first, '' == 0
// string vs number, string is converted to number. 0 == 0
console.log([] == false)
// true
// booleans are converted to number first, [] == 0
// 0 is primitive, so object(the array) is converted to primitive
// there is no preferred type here and [] is converted to '', '' == 0
// see details at https://bfe.dev/quiz/Implicit-Conversion-II/solution
// again, '' is converted to 0, 0 == 0
console.log(undefined == false)
// false
// booleans are converted to number first, undefined == 0
// no cases is defined for `undefined` vs `number`, so false
console.log(null == false)
// false, same as above
console.log('1' == true)
// true
// booleans are converted to number first, '1' == 1
// string vs number, string is converted to number. 1 == 0
console.log(1n == true)
// true
// booleans are converted to number first, 1n == 1
// bigint vs number, they are equal in value, so true
console.log(' 1     ' == true)
// true
// booleans are converted to number first, ' 1     ' == 1
// string vs number, string is converted to number. 1 == 1
```

## ECMAScript Spec about loosely equal `==`

From [ECMAScript spec](https://tc39.es/ecma262/#sec-islooselyequal), loosely equal check works as below.

> 1. If Type(_x_) is the same as Type(_y_), then
>    1. Return IsStrictlyEqual(_x_, _y_).
> 2. If _x_ is null and _y_ is undefined, return true.
> 3. If _x_ is undefined and _y_ is null, return true.
> 4. NOTE: This step is replaced in section B.3.6.2.
> 5. If _x_ is a Number and _y_ is a String, return ! IsLooselyEqual(_x_, ! ToNumber(_y_)).
> 6. If _x_ is a String and _y_ is a Number, return ! IsLooselyEqual(! ToNumber(_x_), _y_).
> 7. If _x_ is a BigInt and _y_ is a String, then
>    1. Let n be StringToBigInt(_y_).
>    2. If n is undefined, return false.
>    3. Return ! IsLooselyEqual(_x_, n).
> 8. If _x_ is a String and _y_ is a BigInt, return ! IsLooselyEqual(_y_, _x_).
> 9. If _x_ is a Boolean, return ! IsLooselyEqual(! ToNumber(_x_), _y_).
> 10. If _y_ is a Boolean, return ! IsLooselyEqual(_x_, ! ToNumber(_y_)).
> 11. If _x_ is either a String, a Number, a BigInt, or a Symbol and _y_ is an Object, return ! IsLooselyEqual(_x_, ? ToPrimitive(_y_)).
> 12. If _x_ is an Object and _y_ is either a String, a Number, a BigInt, or a Symbol, return ! IsLooselyEqual(? ToPrimitive(_x_), _y_).
> 13. If _x_ is a BigInt and _y_ is a Number, or if _x_ is a Number and _y_ is a BigInt, then
>     1. If _x_ is not finite or _y_ is not finite, return false.
>     2. If ℝ(_x_) = ℝ(_y_), return true; otherwise return false.
> 14. Return false.

The steps are pretty straightforward it explains itself.

One thing is about ToPrimitive() though, let's continue.

## ToPrimitive() in ECMAScript Spec

This defines how we get primitive values from any data types, according to the [ECMAScript spec](https://tc39.es/ecma262/multipage/abstract-operations.html#sec-toprimitive):

> 1. If _input_ is an Object, then
> 1. Let _exoticToPrim_ be ? GetMethod(input, @@toPrimitive).
> 1. If _exoticToPrim_ is not undefined, then
>    1. If _preferredType_ is not present, let hint be "default".
>    2. Else if _preferredType_ is string, let hint be "string".
>    3. Else,
>       1. Assert: _preferredType_ is number.
>       2. Let hint be "number".
>    4. Let _result_ be ? Call(_exoticToPrim_, _input_, « _hint_ »).
>    5. If _result_ is not an Object, return _result_.
>    6. Throw a TypeError exception.
> 1. If _preferredType_ is not present, let _preferredType_ be number.
> 1. Return ? OrdinaryToPrimitive(_input_, _preferredType_).
> 1. Return _input_.

Here it says, if [Symbol.toPrimitive](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/toPrimitive) is defined in the object, then the method will be used in ToPrimitive(). This is pretty obvious, below is an example.

```js
const obj =
  {
    [Symbol.toPrimitive](hint) {
      if (hint === 'number') {
        return 100
      }
      return 'obj'
    }
  } + obj // 100
```

If [Symbol.toPrimitive](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/toPrimitive) is not defined, the default _OrdinaryToPrimitive()_ is used.

## OrdinaryToPrimitive() in ECMAScript spec

This is the default process before Symbol.toPrimitive is a thing. According to the [ECMAScript spec](https://tc39.es/ecma262/multipage/abstract-operations.html#sec-ordinarytoprimitive):

> 1. If hint is string, then
> 1. Let _methodNames_ be « "toString", "valueOf" ».
> 1. Else,
> 1. Let _methodNames_ be « "valueOf", "toString" ».
> 1. For each element _name_ of _methodNames_, do
> 1. Let _method_ be ? Get(O, name).
> 1. If IsCallable(_method_) is true, then
> 1. Let _result_ be ? Call(_method_, _O_).
> 1. If _result_ is not an Object, return _result_.
> 1. Throw a **TypeError** exception.

What it says is basically

1. if needs a string, first `toString()` then `valueOf()` is tried and return the first primitive value;
2. if needs a number, first `valueOf()` then `toString()` is tried and return the first primitive value;

For empty array `[]` in our question

1. there is no [Symbol.toPrimitive]() defined in Array, continue.
2. Array inherits [Object.prototype.valueOf()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/valueOf#description) is tried, but it just returns the array itself, not a primitive value, continue
3. Different from [Object.prototype.toString()](https://tc39.es/ecma262/multipage/fundamental-objects.html#sec-object.prototype.tostring), Array has its own [Array.prototype.toString()](https://tc39.es/ecma262/multipage/indexed-collections.html#sec-array.prototype.tostring)
   1. we can see from the [spec](https://tc39.es/ecma262/multipage/indexed-collections.html#sec-array.prototype.tostring) that `Array.prototype.join()` is used here.
   2. in [Array.prototype.join()](https://tc39.es/ecma262/multipage/indexed-collections.html#sec-array.prototype.join), it tries to convert all elements to string, which recursively calls [Array.prototype.toString()](https://tc39.es/ecma262/multipage/indexed-collections.html#sec-array.prototype.tostring).
