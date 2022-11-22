## Solution by [@Dharanish-V](https://github.com/dharanish-v)

* The counter app is implemented with useState hook and event handler functions
* The useState hook is initialized with 0, so that when the component displays 0 on its initial render
* The useState hook return two objects, one is the value and other is dispatch function
* The value is used directly to show the current value of the state
* The dispatch function when called by passing the new value will change the old state and triggers a re-render
* The handleCounterDecrement is used to decrement the value by 1 and handleCounterIncrement is used to increment the value by 1
* The event handler functions are assigned to the onClick attribute of the buttons


```
import React from 'react'

export function App() {
  const [counterValue, setCounterValue] = React.useState(0);

  const handleCounterDecrement = () => {
    setCounterValue(counterValue-1)
  }

  const handleCounterIncrement = () => {
    setCounterValue(counterValue+1)
  }

  return (
    <div>
      <button data-testid="decrement-button" onClick={handleCounterDecrement}>-</button>
      <button data-testid="increment-button" onClick={handleCounterIncrement}>+</button>
      <p>clicked: {counterValue}</p>
    </div>
  )
}
```
