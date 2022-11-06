```js
function undefinedToNull(arg) {
   switch (true) {
      case arg == undefined: 
        return null
      case typeof arg != 'object':
        return arg
      case Array.isArray(arg):
        return arg.map(undefinedToNull)
      default:
        let obj = {}
        for (let key in arg) {
          obj[key] = undefinedToNull(arg[key])
        }
        return obj
   }
}
```
Would you like to [contribute to the solution](https://github.com/BFEdev/BFE.dev-solutions/blob/main/problem/undefined-to-null_en.md)? [Contribute guideline](https://github.com/BFEdev/BFE.dev-solutions#how-to-contribute)
