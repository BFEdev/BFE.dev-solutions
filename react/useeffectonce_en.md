```typescript
import { EffectCallback, useEffect } from 'react'

export function useEffectOnce(effect: EffectCallback) {
  useEffect(() => {
    return effect() //Returns effect's cleanup function
  }, []) //An empty dependency array means the effect will only run once on component mount
}
```

