### Solution by [Anton Galich](https://github.com/GalichAnton)

### 1. This custom hook useTimeout allows you to set a callback function and a delay, after which this function will be called. 

### 2. The hook uses two hooks from the React library: useEffect and useRef. 
```
import {useEffect, useRef} from 'react'

export function useTimeout(callback: () => void, delay: number) {
  
  /*  useRef is used to create a reference to the timer and save 
  the callback function in a variable that will not change between component renders. */

  const timer = useRef<number>()
  const cb = useRef<Function>(callback)

  /* In line below, the passed callback function is being saved in the cb.current variable, which is created using useRef. This allows to preserve the reference to the function between component renders and use it in useEffect to call the function after a specified delay. Thus, when the timer ends, the saved function will be called. */
  cb.current = callback

  /* useEffect is used to set the timer using the setTimeout function and */

  useEffect(() => {

    /* In this below, a handler function is created that calls the current function saved in the variable cb.current(). So when the handler is called, it calls the saved function. */

    const handler = () =>  cb.current()
     // When the delay ends, the saved callback function is called. 
    timer.current = setTimeout(() => handler(), delay)

    /* clear the timer using the clearTimeout function when the component is unmounted or the delay is changed. */ 
    return () => {
      clearTimeout(timer.current)
    }

  }, [delay])
}
```

#### This hook can be useful for creating a delay before performing any actions, such as displaying a loading animation or delaying before sending a request to the server.