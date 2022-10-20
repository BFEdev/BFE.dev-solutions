## Background Knowledge of Event Loop

Event loop in JavaScript determines how the code is executed. 

For details, please refer to [The explanation on JavaScript.info](https://javascript.info/event-loop), it is very helpful and easy to understand. 

Simply put

1. Event loop keeps executing the tasks.
2. Macro Tasks are created by running scripts, event handlers, `setTimeout()`, .etc.
3. Micro Tasks are created by function calls like `Promise.then()`, `queueMicrotask()` .etc. 
4. After each Macro task is executed, if there are Micro tasks created, Event loop executes them first before executing next Macro task.

OK, now let's breakdown the code in the question.

## Step-sy-step Question Analysis 

```js
console.log(1)
const promise = new Promise((resolve) => {
  console.log(2)
  resolve()
  console.log(3)
})

console.log(4)

promise.then(() => {
  console.log(5)
}).then(() => {
  console.log(6)
})

console.log(7)

setTimeout(() => {
  console.log(8)
}, 10)

setTimeout(() => {
  console.log(9)
}, 0)
```

When above script is executed, the execution of the whole script is one Macro task. 

|Macro|Micro| console logs |
-------|------|-----
| script run | | |

We meet the first `console.log()`

|Macro|Micro| console logs |
-------|------|-----
| script run | | 1 |

Now comes the creation of Promise.

```js
const promise = new Promise((resolve) => {
  console.log(2)
  resolve()
  console.log(3)
})
```

The arrow function passed to `Promise()` is the executor, from the [ECMA262 Spec](https://tc39.es/ecma262/multipage/control-abstraction-objects.html#sec-promise-executor), we can see that the executor function is executor during the construction of Promise object, so it is synchrouns.

`resolve()` changes the internal state of Promise object to `fulfilled`, but doesn't block the next line of code, thus we have following tasks and logs.

|Macro|Micro| console logs |
-------|------|-----
| script run | | 1 2 3 |

Again we meet a `console.log()` call.

|Macro|Micro| console logs |
-------|------|-----
| script run | | 1 2 3 4 |

```
promise.then(() => {
  console.log(5)
}).then(() => {
  console.log(6)
})
```

[Promise.then()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/then) sets up the success and failure callbacks for the Promise object. From the [ECMA262 Spec](https://tc39.es/ecma262/multipage/control-abstraction-objects.html#sec-performpromisethen), we can clearly see that when the Promise object is already fullfilled or rejected, `then()` would [HostEnqueuePromiseJob](https://tc39.es/ecma262/multipage/executable-code-and-execution-contexts.html#sec-hostenqueuepromisejob), which basically means it is add to the Micro Tasks. 

|Macro|Micro| console logs |
-------|------|-----
| script run | () => console.log(5) | 1 2 3 4 |

[Promise.then()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/then) also returns a new Promise, since there is no return value in the callback `() => console.log(5)`, the new Promise is resolved with value `undefined`, and next `then()` does repeats above process. 

|Macro|Micro| console logs |
-------|------|-----
| script run | () => console.log(5), () => console.log(6) | 1 2 3 4 |

Another `console.log()` call.

|Macro|Micro| console logs |
-------|------|-----
| script run | () => console.log(5), () => console.log(6) | 1 2 3 4 7 |

Here comes `setTimeout()`.

```js
setTimeout(() => {
  console.log(8)
}, 10)

setTimeout(() => {
  console.log(9)
}, 0)
```

`setTimeout()` is not part of JavaScript language spec, but it is a host API, which is under [HTML spec](https://html.spec.whatwg.org/multipage/timers-and-user-prompts.html#dom-settimeout-dev), basically the host(most of the time, it means the browser or node.js) tries to add the Macro task after certain delay.

Notice that the delay you set is the minimum delay since the timer is to add Macro tasks, the execution is still handled by Event loop. 

|Macro|Micro| console logs | host timer |
-------|------|-----|----
| script run | () => console.log(5), () => console.log(6) | 1 2 3 4 7 | 0ms, 10ms|

Now all code in the first Macro task are executed, Event loop tries to execute all the Micro tasks

|Macro|Micro| console logs | host timer |
-------|------|-----|----
|  | () => console.log(6) | 1 2 3 4 7 5| 0ms, 10ms|

|Macro|Micro| console logs | host timer |
-------|------|-----|----
|  | | 1 2 3 4 7 5 6| 0ms, 10ms|

Now all tasks are empty, and time is up for the 1st timer

|Macro|Micro| console logs | host timer |
-------|------|-----|----
| () => console.log(9) | | 1 2 3 4 7 5 6| 10ms|

Event loop picks up the Macro task. 

|Macro|Micro| console logs | host timer |
-------|------|-----|----
|  | | 1 2 3 4 7 5 6 9 | 10ms|

Now all tasks are empty, and time is up for the 1st timer

|Macro|Micro| console logs | host timer |
-------|------|-----|----
| () => console.log(9) | | 1 2 3 4 7 5 6| 10ms|

The last timer gets executed. 

|Macro|Micro| console logs | host timer |
-------|------|-----|----
| () => console.log(8) | | 1 2 3 4 7 5 6 9 | |

Here is our final result.

|Macro|Micro| console logs | host timer |
-------|------|-----|----
| | | 1 2 3 4 7 5 6 9 8| |


