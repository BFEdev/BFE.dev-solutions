Solution by: [Amyth](https://github.com/amitg01)

```
function createCounter() {
  let value = 0
  return {
    get count(){
      return(value++)
    },
    set count(_val){
      return
    }
  }
}
```
