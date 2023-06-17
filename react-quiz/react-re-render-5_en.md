The answer is 
```
"App"
"A"
"B"
"C"
"App"
"B"
"C"
```
Initial render ends with in console:
``` 
"App"
"A"
"B"
"C"
```
Second render triggers by useEffect(which is executed after initial render) + useState, which leads to changes in MyContext(useContext will trigger rerender whenever the context value is changed), but we have only 1 re-render, because of batching. 
Although components A and B are already memoized and not re-rendered, component B has useContext in itself, which leads to re-render B component.

Second render ends with in console: 
```
"App"
"B"
"C"
```


