This is the very basic example on [React homepage](https://reactjs.org/docs/hooks-state.html).

## useState() to hold the count

Since we need to persist the count and also render it on UI, `useState()` is the right approach here. 

```js
import React, {useState} from 'react'

export function App() {

  const [count, setCount] = useState(0)
  return (
    <div>
      <button data-testid="decrement-button" onClick={() => setCount(count - 1)}>-</button>
      <button data-testid="increment-button" onClick={() => setCount(count + 1)}>+</button>
      <p>clicked: {count}</p>
    </div>
  )
}
```

## better use useState((prevState) => newStae)

`setCount(count - 1)` is not the best option, [explained here](https://reactjs.org/docs/faq-state.html#what-is-the-difference-between-passing-an-object-or-a-function-in-setstate), let's try passing a function.

```js
import React, {useState} from 'react'

export function App() {

  const [count, setCount] = useState(0)
  return (
    <div>
      <button data-testid="decrement-button" onClick={() => setCount(count => count - 1)}>-</button>
      <button data-testid="increment-button" onClick={() => setCount(count => count + 1)}>+</button>
      <p>clicked: {count}</p>
    </div>
  )
}
```

## useCallback() to persist the callbacks

`onClick` now is set with an arrow function, this function is newly created on every render, we can use `useCallbac()` to avoid this.

```js
import React, {useState, useCallback} from 'react'

export function App() {

  const [count, setCount] = useState(0)
  const decrease = useCallback(() => setCount(count => count - 1), [])
  const increase = useCallback(() => setCount(count => count + 1), [])
  return (
    <div>
      <button data-testid="decrement-button" onClick={decrease}>-</button>
      <button data-testid="increment-button" onClick={increase}>+</button>
      <p>clicked: {count}</p>
    </div>
  )
}
```

## data attributs could be used to create single event handler

In above example, we need to create hander for decrease and increase, this is not scalable. 
We can create just one handler and config it by data attributes

```js
import React, {useState, useCallback} from 'react'

export function App() {

  const [count, setCount] = useState(0)
  const update = useCallback((e: React.MouseEvent<HTMLButtonElement>) => {
    const change = parseInt(e.currentTarget.dataset.change ?? '0')
    setCount(count => count + change)
  }, [])
  return (
    <div>
      <button data-testid="decrement-button" data-change="-1" onClick={update}>-</button>
      <button data-testid="increment-button" data-change="1" onClick={update}>+</button>
      <p>clicked: {count}</p>
    </div>
  )
}
```

## useReducer() could be used for even more complex interactions

This is the code demo from [React homepage](https://reactjs.org/docs/hooks-reference.html#usereducer), which demonstrrate `useReducer()` as an alternative to `useState()`, which might be more favorable if you are used to [Redux](https://redux.js.org/) toolchain.

```js
import React, { useReducer } from 'react';

const initialState = { count: 0 };

function reducer(state: {count: number}, action: {type: 'increment' | 'decrement'}) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
  }
}

export function App() {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <div>
      <button
        data-testid="decrement-button"
        onClick={() => dispatch({ type: 'decrement' })}
      >
        -
      </button>
      <button
        data-testid="increment-button"
        onClick={() => dispatch({ type: 'increment' })}
      >
        +
      </button>
      <p>clicked: {state.count}</p>
    </div>
  );
}
```
