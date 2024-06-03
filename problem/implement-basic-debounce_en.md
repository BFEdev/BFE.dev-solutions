
function debounce(func, wait) {
  // your code here

  let timer
  return function(...args){
      clearTimeout(timer)
    timer = setTimeout(()=>{
      func.apply(this,args)
    },wait)

  }
}

Would you like to [contribute to the solution](https://github.com/BFEdev/BFE.dev-solutions/blob/main/problem/implement-basic-debounce_en.md)? [Contribute guideline](https://github.com/BFEdev/BFE.dev-solutions#how-to-contribute)



