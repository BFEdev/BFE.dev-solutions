
## Simple Solution For Decode Message Problem

```js
/**
 * @param {string[][]} message
 * @return {string}
 */
function decode(grid) {
  if(!grid || grid.length==0 || !grid[0] || grid[0].length==0 ) return '';
  let res = '';
  let rows = grid.length;
  let cols = grid[0].length;
  let rowDir = 1;
  let row = 0;
  let col = 0;
  while(col < cols){
    res = res += grid[row][col];
    if(row == rows - 1){
      rowDir = -1;
    }else if(row == 0){
      rowDir = 1;
    }
    row+=rowDir;
    col++;
  }
  return res;
}
```
