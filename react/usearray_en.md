```typescript
import React, { useState, useCallback } from 'react'

type UseArrayActions<T> = {
  push: (item: T) => void,
  removeByIndex: (index: number) => void
}

export function useArray<T>(initialValue: T[]): { value: T[] } & UseArrayActions<T> {
  const [array, setArray] = useState<T[]>(initialValue);

  const push = useCallback((item : T) => {
    const copy = array.map((x) => x) // Mapping with the identity function creates a shallow copy
    copy.push(item);
    setArray(copy);
  }, []); // useCallback makes it so that React does not create a new instance of the method on each usage of the hook


  const removeByIndex = useCallback((index : number) => {
    const copy = array.map((x) => x) // Same as above
    copy.splice(index, 1); // Removes 1 element at the specified index
    setArray(copy);
  }, []) 
  
  return {
    value: array,
    push: push,
    removeByIndex: removeByIndex
  }
}
```
