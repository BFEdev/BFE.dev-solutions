Please refer to the [React homepage](https://legacy.reactjs.org/docs/hooks-effect.html) and [usehooks-ts](https://usehooks-ts.com/react-hook/use-effect-once) to learn more about hooks.

## The Problem: 

Implement useEffectOnce() as the name says itself, it runs an effect only once.

# useEffect()

React has a built-in hooks that let you perform side effects in functional components. The Class component comparison to useEffect are the methods componentDidMount, componentDidUpdate, and componentWillUnmount. UseEffect will run when the component renders, which can sometimes be more times than expected. 

```js
import React, { useEffect } from 'react';

function App() {
  useEffect(() => {
    fetch("http://example.com/movies.json")
    .then(response => response.json())
    .then(data => console.log(data))
  });

  return (
    <div>
    </div>
  );
}

```

In the above example, it’s likely the useEffect will run only once. However, by default, useEffect() runs both after the first render and after every update, so you may end up fetching multiple times, which would be inefficient. For this reason, you may want to implement an useEffectOnce() function that's executed only one time, at the mounting time. To do this, you should pass in an array as an optional second argument to useEffect to tell React to skip applying an effect if certain values haven’t changed between re-renders.

# Solution

```js
import { EffectCallback, useEffect } from 'react'

function useEffectOnce(effect: EffectCallback) {
  // eslint-disable-next-line react-hooks/exhaustive-deps
  useEffect(effect, [])
}

export default useEffectOnce
```

