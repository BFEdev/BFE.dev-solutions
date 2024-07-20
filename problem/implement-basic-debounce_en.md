## Overview

Debouncing is a programming practice used to ensure that time-consuming tasks do not fire so often, helping to improve performance and responsiveness. This is particularly useful in scenarios like handling input events, resizing windows, or fetching data from an API.

The `debounce` function prevents a function from being called too frequently. It ensures that the function is called only once after a specified wait time has elapsed since the last time the debounced function was invoked.

## Usage

The `debounce` function takes two parameters:

1. `func`: The function to be debounced.
2. `wait`: The number of milliseconds to wait before invoking `func` after the last call.

It returns a new function that, when invoked, will delay the execution of `func` until after `wait` milliseconds have elapsed since the last time the returned function was invoked.

## Implementation

```javascript
/**
 * @param {(...args: any[]) => any} func
 * @param {number} wait
 * @returns {(...args: any[]) => any}
 */
function debounce(func, wait) {
  let timeoutId;

  return function (...args) {
    if (timeoutId) clearTimeout(timeoutId);
    timeoutId = setTimeout(() => {
      func(...args);
    }, wait);
  };
}
```
