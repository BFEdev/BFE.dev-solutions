```typescript
import {useState} from "react";

export function useToggle(on: boolean): [boolean, () => void] {
  const [status, setStatus] = useState(on);

  const toggleStatus = () => {
    setStatus(!status);
  }

  return [status, toggleStatus]
}
```
