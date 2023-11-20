## Umesh Malik
``` typescript
function uncompress(compressed: string): string {
  let i = 0;
  const openingIndexes: Array<number> = [];
  const nums: Array<number> = [];
  let num = "";
  while (compressed.includes('(')) {
    if (compressed.charCodeAt(i) >= 48 && compressed.charCodeAt(i) <= 57) {
      num += compressed[i];
    } else {
      num !== "" ? nums.push(parseInt(num)) : null;
      num = "";
    }
    if (compressed[i] === '(') {
      openingIndexes.push(i);
    }
    if (compressed[i] === ')') {
      const lastOpening = openingIndexes.pop() as number;
      const multiplier = nums.pop() as number;
      const stringToPush = compressed.slice(lastOpening + 1, i).repeat(multiplier);
      compressed = compressed.slice(0, lastOpening - multiplier.toString().length) + stringToPush + compressed.slice(i + 1, compressed.length);
      i = lastOpening + stringToPush.length - multiplier.toString().length - 2;
    }
    i++;
  }
  return compressed;
}
```
