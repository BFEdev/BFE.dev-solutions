
There is no solution yet.

Would you like to [contribute to the solution](https://github.com/BFEdev/BFE.dev-solutions/blob/main/problem/find-the-single-integer_en.md)? [Contribute guideline](https://github.com/BFEdev/BFE.dev-solutions#how-to-contribute)
// horrible, but its working :D

function findSingle(arr) {
  // your code here
  const newArr = arr.sort(function(a, b) {
    return a - b
  })
  const res = [];
  for(let el of newArr) {
    if(res.includes(el)) {
      res.pop(el)
    } else {
      res.push(el)
    }
  }
  return res.length === 1 ? res[0] : res
}
