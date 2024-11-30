## Very basic implementation of debouncing.


```javascript

// This is a JavaScript coding problem from BFE.dev 

/**
 * @param {(...args: any[]) => any} func
 * @param {number} wait
 * @returns {(...args: any[]) => any}
 */
function debounce(func, delay) {
    let timeoutID;

    return function(...args) {
//Clearing the timeout to avoid previous function call
    clearTimeout(timeoutID);
//New function is passed to the setTimeout using the JS bind method.
      timeoutID = setTimeout(func.bind(this, ...args),delay);
    }
}



```
