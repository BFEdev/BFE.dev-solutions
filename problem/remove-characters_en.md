> This awesome solution is created by [@Ahmed](https://github.com/AhmedIbrahim336)!

## Problem analysis

This problem is a classic example of a `stack` problem as you need to remember the last `char` you have while you are going through the string.

## Solution

```ts
const stack: string[] = [];
for (let i = 0; i < input.length; i++) {
  const char = input[i];
  if (stack.length != 0 && char === "c" && stack[stack.length - 1] === "a") {
    stack.pop();
  } else if (char !== "b") {
    stack.push(char);
  }
}

return stack.join("");
```

## Go through example

**Input:** `removeChars('cabbaabcca')`

**Output:** `caa`

At the stack the stack is empty.

| Char | Explanation                                                     | Stack          |
| ---- | --------------------------------------------------------------- | -------------- |
| `c`  | Any letter other than `b` should be added to the stack          | `[c]`          |
| `a`  | The same as `c`                                                 | `[c, a]`       |
| `bb` | Any `b` will not be added. stack still the same                 | `[c, a]`       |
| `aa` | `a, a` will be added to the stack                               | `[c, a, a, a]` |
| `b`  | `b` will not be added                                           | `[c, a, a, a]` |
| `cc` | `cc` will be removed with the last `aa` at the top of the stack | `[c, a]`       |
| `a`  | Last `a` will be added to the stack                             | `[c, a, a]`    |

## Space and time complexity

We are going throgh the string once so we have a time complexity of `O(n)` where the `n` is the length of the input.
Becuase we are using stack to make a copy of the output string so at the worse case we might store the whole input again! So we have a space complexity of `O(n)`.
