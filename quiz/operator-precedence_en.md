## Operator precedence at-a-glance

Operator precedence describes the order in which operations are performed in an expression.

The order of execution for comparison operators in JavaScript follows standard operator precedence rules. Here are some of the key comparison operators and their precedence levels:

| **Precedence** |  1  |  2  |  3  |  4  |  5  |  6  |  7  |  8  |
| -------------- | --- | --- | --- | --- | --- | --- | --- | --- |
| **Operator**   | `<` | `<=`| `>` | `>=`| `==`| `!=`| `===`| `!==`|

Priority means that if there are operators with different priorities in an expression, the operator with the higher priority will be executed first. For example, in the expression `a == b < c`, `b < c` will be executed first, and then the result will be compared to a using `==`.

If there are operators with the same priority in the expression, they will be executed from left to right. For example, in the expression `a < b < c`, `a < b` will be executed first, and then the result of that comparison (which will be true or false) will be compared with `c`.

Comparison operators usually return a Boolean value (true or false), but in expressions, they can be coerced to other data types according to the type coercion rules in JavaScript.

Let's remind ourselves of these implicit type conversion rules. When there is a comparison between a boolean and a number, JavaScript automatically converts the boolean to a number. Thus, `false` is converted to `0` and `true` to `1`. So, if you write `false == 0`, JS coerces `false` to `0`, and `0 == 0` returns `true`. Similarly, `true == 1` will be `true`, because `true` is coerced to `1`.

Knowing this, let's answer the question.

## Step-by-step Question Analysis 

```js
console.log(0 == 1 == 2)
console.log(2 == 1 == 0)
console.log(0 < 1 < 2)
console.log(1 < 2 < 3)
console.log(2 > 1 > 0)
console.log(3 > 2 > 1)
```

```console.log(0 == 1 == 2)``` will output ```false```, because ```0 == 1 == 2``` is equivalent to ```(0 == 1) == 2```. The first comparison ```0 == 1``` is ```false```, so it becomes ```false == 2``` or ```0 == 2```, which is ```false```.

```console.log(2 == 1 == 0)``` will output ```true```, because ```2 == 1 == 0``` is equivalent to ```(2 == 1) == 0```. The first comparison ```2 == 1``` is ```false```, so it becomes ```false == 0``` or ```0 == 0```, which is ```true```.

```console.log(0 < 1 < 2)``` will output ```true```, because ```0 < 1 < 2``` is equivalent to ```(0 < 1) < 2```. The first comparison ```0 < 1``` is ```true (1)```, so it becomes ```1 < 2```, which is ```true```.

```console.log(1 < 2 < 3)``` will output ```true```, because ```1 < 2 < 3``` is equivalent to ```(1 < 2) < 3```. The first comparison ```1 < 2``` is ```true (1)```, so it becomes ```1 < 3```, which is ```true```.

```console.log(2 > 1 > 0)``` will output ```true```, because ```2 > 1 > 0``` is equivalent to ```(2 > 1) > 0```. The first comparison ```2 > 1``` is ```true (1)```, so it becomes ```1 > 0```, which is ```true```.

```console.log(3 > 2 > 1)``` will output ```false```, because ```3 > 2 > 1``` is equivalent to ```(3 > 2) > 1```. The first comparison ```3 > 2``` is ```true (1)```, so it becomes ```1 > 1```, which is ```false```.
