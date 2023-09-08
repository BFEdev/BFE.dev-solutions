> Created by [@pavelgorbach](https://github.com/pavelgorbach)
## useFirstRender Hook with useRef

The `useIsFirstRender` hook is a custom React hook that returns a boolean value indicating whether a component has been rendered for the first time or not. This hook is useful for scenarios where you want to execute certain logic only on the initial render of a component.

### Usage

```javascript
import { useEffect } from 'react';

function MyComponent() {
  const isFirstRender = useIsFirstRender();
  // Rest of your component code...

  return (
    <div>
      <div>Is first render: {isFirstRender ? 'yes' : 'no'}</div>
      {/* JSX for your component... */}
    </div>
  );
}
```

### Implementation

The `useRef` hook is a fundamental part of the React ecosystem, often used to create mutable references to DOM elements, but it can also be employed for various other purposes. In the case of the `useIsFirstRender` hook, we utilize `useRef` to maintain state across multiple renders without triggering re-renders.

Here's an explanation of how `useRef` works within the `useFirstRender` hook:

1. **Initialization:** When you declare a ref using `useRef`, it creates an object with a `current` property initialized to the initial value provided as an argument. For example:

    ```javascript
    const isFirstRenderRef = useRef(true);
    ```

    In the `useIsFirstRender` hook, we initialize `isIsFirstRenderRef` with `true`, indicating that the component is in its initial render state.

2. **Preservation of Value:** The key property of `useRef` is that the `current` property preserves its value across renders. Even if a component re-renders, the `current` value of the ref remains the same unless explicitly modified.

3. **Checking First Render:** In the `useIsFirstRender` hook, we return `current` value of the `isFirstRenderRef` ref. If `current` is `true`, it means the component is being rendered for the first time. Subsequently, we set `isFirstRenderRef.current` to `false` to indicate that the initial render has occurred.

    ```javascript
      if (isFirstRenderRef.current) {
        isFirstRenderRef.current = false;
        return true;
      }

      return isFirstRenderRef.current;
    ```

    This function allows you to query whether the component is on its first render or not. If it's the first render, it returns `true`, and `false` on subsequent renders.

4. **Avoiding Rerenders:** This approach ensures that we can keep track of whether the component has been rendered for the first time without causing re-renders. If we used state (e.g., `useState`) to track this, each change to the state would trigger a re-render, which is not what we want when just checking for the first render.

In summary, `useRef` is utilized in the `useIsFirstRender` hook to maintain a persistent value (`true` or `false`) that persists across renders, allowing us to efficiently check if the component is being rendered for the first time or on subsequent renders without causing unnecessary re-renders.

#### Final Code

```typescript
import { useRef } from 'react';

export function useIsFirstRender() {
  const isFirstRenderRef = useRef(true);

  if(isFirstRenderRef.current) {
    isFirstRenderRef.current = false;
    return true;
  }

  return isFirstRenderRef.current;
}

export default useIsFirstRender;
```
