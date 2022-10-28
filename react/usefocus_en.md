There are 3 important points should be aware of:
1. Reset the state when the target element changes.
2. Do `addEventListener` & `removeEventListener` **only** when the target element changes, **NOT** on every render.
3. Keep the handler function as the **same reference** for `addEventListener` & `removeEventListener`, otherwise the event will **NOT** be removed. There're two mostly used options to do this:
  - Use `useCallback` to cache the **callback**.
  - Define the **callback** inside the `useEffect` function.(recommended)

```ts
import React, { Ref, useEffect, useRef, useState } from 'react'

export function useFocus<T extends HTMLElement>(): [Ref<T | undefined>, boolean] {
  const [isFocused, setFocused] = useState(false)
  const ref = useRef<T>()

  useEffect(() => {
    const currentElement = ref.current

    // !!IMPORTANT!! 
    // reset the focus state when the currentElement changes.
    setFocused(document.activeElement === currentElement)
  
    if (!currentElement)
    return

    const onFocus = () => setFocused(true)
    const onBlur = () => setFocused(false)

    currentElement.addEventListener('focus', onFocus)
    currentElement.addEventListener('blur', onBlur)

    return () => {
      currentElement.removeEventListener('focus', onFocus)
      currentElement.removeEventListener('blur', onBlur)
    }
  // now we can pass a dependency array to get much better performance.
  // So this will prevent duplicate `register` & `unregister` on every render. (which is widely appeared at the discuss)
  }, [ref.current])


  return [ref, isFocused]
}
```

Would you like to [contribute to the solution](https://github.com/BFEdev/BFE.dev-solutions/blob/main/react/usefocus_en.md)? [Contribute guideline](https://github.com/BFEdev/BFE.dev-solutions#how-to-contribute)
