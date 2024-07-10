import {useEffect, useState, useRef} from 'react'
export function useTimeout(callback: () => void, delay: number) {
  const timer = useRef<number|null>(null)
  const callbackRef = useRef(callback)
  callbackRef.current = callback;
  useEffect(()=>{
    clearTimeout(timer.current)
    timer.current = setTimeout(()=>callbackRef.current(), delay)
    return ()=>{
      clearTimeout(timer.current)
    }
  }, [delay])
}

Would you like to [contribute to the solution](https://github.com/BFEdev/BFE.dev-solutions/blob/main/react/usetimeout_en.md)? [Contribute guideline](https://github.com/BFEdev/BFE.dev-solutions#how-to-contribute)
