# Solution : Find First Duplicate Character In String
```js
//Make A Function 
const firstDuplicate = (str) => {
  //Make a Empty Set 
  const charSet = new Set();
  //Make A For Of Loop
  for(const char of str){
    if(charSet.has(char)){
      return char;
    }
    charSet.add(char);
  }
  return null;
}

console.log(firstDuplicate("programming"));
```
## Easy Solution For New Ones With New Concept Of Set Keyword
