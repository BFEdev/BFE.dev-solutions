> This solution is created by [@Himanshu](https://www.yadavhimanshu.com) !

## Solution 
```js
"App"
"A1"
"fallback"
"A1"
"A2"
```
## Step by Step 
1. Mouting of <code>App</code> Component
   1. App component will be executed and log ` "App" ` will be printed
2. Component `A` is wrapped in suspense boundary. Component `A` will be executed.
   1. The lof `"A1"` will be logged.
   2. `resource.get()` will trigger the asynchronous task. Which will suspend the rendering of `A`
3. Now `Fallback` Component Renders and log `"fallback"`
4. React does not preserve any state for renders that got suspended before they were able to mount for the first time. When the component has loaded, React will retry rendering the suspended tree from scratch. [Reference](https://react.dev/reference/react/Suspense#caveats).
   1. According to React documentation Component `A` will re-render 
      1. It will log `A1` first then `resource.get()` called again. It will return `1`
      2. Then it will log `A2`.
```js
"App"
"A1"
"fallback"
"A1"
"A2"
```