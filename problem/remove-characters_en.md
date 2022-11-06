## Problem analysis
This problem is a classic example of a `stack` problem as you need to remember the last `char` you have while you are going through the string. 

## Solution

```ts
  let stack: string[] = []
  for (let i = 0; i < input.length; i++) {
    let char = input[i]
    if (stack.length != 0 && char === 'c' && stack[stack.length - 1] === 'a') {
      stack.pop()
    } else if (char !== 'b') {
      stack.push(char)
    }
  }

  return stack.join('')
```

## Go through example 

**Input:** `removeChars('cabbaabcca')`

**Output:** `caa`

1. At first the stack will be empty. 
2. The two letters `ca` are not `b` so they will be added to stack `[c, a]`. 
3. The next `bb` will not be added to the stack. Stack still `[c, a]`
4. The next `aa` will be added to the stack as they are not `b`. Stack will be `[c, a, a, a]`
5. The next `b` wil not be added to the stack. Stack still `[c, a, a, a]`
6. Becuase the next char is `c` and the last element added to the stack was `a`, `a` will be removed and `c` will not be added. Stack will be `[c, a, a]`
7. The same will apply for the next `c`. Stack will be `[c, a]`
8. Finaly, the last `a` in the input will be added to the stack. Stack will be `[c, a, a]` (**our output**)
