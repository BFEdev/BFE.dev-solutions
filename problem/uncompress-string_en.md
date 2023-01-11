*Solution by => Lalit Garghate.*

``` javascript
/**
 * @param {string} str
 * @returns {string}
 */
function uncompress(str) {
  // your code here
  const stack = [];

  for (const letter of str) {
    if (letter === ')') {
      let str = '';

      while (stack.length > 0 && stack[stack.length-1] !== "(") {
        str = stack[stack.length-1] + str;
        stack.pop();
      }

      stack.pop();
      let k = ''; 

      while(stack.length > 0) {
        const top = stack[stack.length-1];
        if (isNaN(Number(top))) break; 
        k = stack[stack.length-1] + k;
        stack.pop();
      }

      k = +k;
      let updatedStr = '';
      
      for (let i=0; i<k; i++) {
        updatedStr += str;
      }
      
      stack.push(updatedStr);
    } else {
      stack.push(letter);
    }
  }

  return stack.join("");
}
```