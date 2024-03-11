Requirements
- `removeAtIndex` method
- `push` method

Now, the implementation of the above methods are standard problems with the notable exception that we must update the array immutably each time. This necessity arises due to the requirement of re-rendering the component wherever this hook is utilized. React employs a strict equality check for optimization purposes, as explained in this [article](https://react.dev/learn/updating-objects-in-state), to differentiate state changes.

```
// Solution by [Ankit Kumar](https://github.com/mynameisankit)

import { useState, useCallback, Dispatch, SetStateAction } from 'react'

type UseArrayActions<T> = {
  push: (item: T) => void,
  removeByIndex: (index: number) => void
}

function removeAtIndex<T>(index: number, array: T[], setArray: Dispatch<SetStateAction<T[]>>): void {
  const partBeforeIndex = array.slice(0, index) || [];
  const partAfterIndex = array.slice(index + 1) || [];

  const updatedArray = [...partBeforeIndex, ...partAfterIndex];

  setArray(updatedArray);
}

function pushValue<T>(value: T, array: T[], setArray: Dispatch<SetStateAction<T[]>>): void {
  const updatedArray = [...array, value];

  setArray(updatedArray);
}

export function useArray<T>(initialValue: T[]= []): { value: T[] } & UseArrayActions<T> {
  const [array, setArray] = useState([...initialValue]);

  const removeByIndex = useCallback((index: number) => removeAtIndex(index, array, setArray), []);
  const push = useCallback((value: T) => pushValue(value, array, setArray), []);
  
  return {
    value: array,
    removeByIndex,
    push,
  };
}
```
