# Simple Solution For The Merge-Sorted-Array
```js
// Make A Function
function merge(arrList) {
  let newArr = arrList.flat();
  //Flat Is The New Method Used To Join The SubArray Of Array
  newArr.sort((a,b) => a-b);
  //Sort The Array In Assending Order
  console.log(newArr);
  return newArr;
}

merge(
  [
    [1,1,1,100,1000,10000],
    [1,2,2,2,200,200,1000],
    [1000000,10000001],
    [2,3,3]
  ]
);
```
