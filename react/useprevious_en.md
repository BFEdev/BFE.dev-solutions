```typescript
import {useRef, useEffect} from "react";

export function usePrevious<T>(value: T): T | undefined {
  const previous = useRef<T|undefined>(undefined)

  useEffect(() => {
    previous.current = value // Does not trigger rerender unlike a state variable
  }, [value]); // Run on rerender only if the value has changed

  return previous.current; //As unintuitive as it sounds, this actually returns the previous value, since useEffect only runs on rerender and modifying a ref's value does not trigger a rerender
}
```
