> This awesome solution is created by [@gsriram24]([https://github.com/pinglu85](https://github.com/gsriram24)) !

This problem enables us to understand the basics of React rendering and re-rendering and how the `children` prop works. Before we dive into the details, let's look at the solution to the problem. 

```
"App"
"A"
"B"
"C"
"D"
"A"
```
Let's go through how rendering works in React, and how the console logs get printed. 

Rendering and displaying any UI in React involves 3 steps. 

1. Triggering a render. 
2. Rendering the component. 
3. Committing to the DOM. 

We will focus on the first two steps which would help us understand this problem better. 

### Triggering a render

A render gets triggered (request to render), under 2 circumstances. 

1. Initial render of the component: When a component is rendered at first, or if your app starts for the first time. In this case, the app is rendered through the `createRoot` function with the target DOM node. 
This explains the first set of console logs. 

2. Upon state updates: Whenever a component is initially rendered, updating the state of a given component will automatically queue a re-render. This explains the second set of console logs. 

### Rendering the component

Let's look at what **Rendering** is. Rendering is React calling your components. It is a function call to the component, hence everything in the function is executed. This explains how the console logs get printed. 

Note: This process is **recursive**. This means, that if the updated component returns some other component, React will render that component next, and if that component returns that component, it will render that component next, and so on. That explains why all the console logs get printed again. 

Now that we understand the underlying workings of rendering and render triggers, let's look at the flow at which the components are rendered in this problem. 

1. Component `App()` is called, Hence logging `'App'` to the console. 
2. Components `A()` and `D()` are called. <br>
    a. 'A' is logged onto the console. We notice that A() returns children, which is in turn B(), hence 
        component B() is called. <br>
    b. `'B'` is logged onto the console. And component `C()` is called. <br>
    c. `'C'` is logged onto the console. <br>
    d.  Component `D()` is called.  `'D'` is logged onto the console.
3. `useEffect` under component `A()` gets executed, and `state` is updated using the `setState` 
     function. 
4. This triggers a **re-render** of the `A()` component. And `"A"` is logged onto the console.

Wait a minute, why didn't `"B"`, `"C"` and `"D"` get console logged again? Isn't `A()` returning its children `B()`? The answer is no. Returning children is not equivalent to calling a component. 
Why is that and how does it work? Let's look at it in depth. 

Let's look at our code again but in terms of how it works under the hood. This is our `App()` function, transpiled. 

``` 
function App() {
   console.log("App");
   return /*#__PURE__*/ React.createElement(
     "div",
     null,
     /*#__PURE__*/ React.createElement(
       A,
       null,
       /*#__PURE__*/ React.createElement(B, null)
     ),
     /*#__PURE__*/ React.createElement(D, null)
   );
 }
```
As you can see, component `B()` represented by `React.createElement(B, null)`, is passed in as a prop(parameter) to component `A()`, and is not called in component `A()`. 
Now since `B()` is initialized and **called** in `App()` (`<B />` is equivalent to `B()`) and not in `A()`, and since all React components are **pure components**, `A()` will return the exact same output for the passed prop `B()`. If a parent keeps returning the exact same child element reference as part of its render output, React will skip re-rendering that child. 

This can be a bit confusing, but that's how react works. 

Now, this can lead to a very interesting optimization point. If you have some heavy component that you don't want re-rendered, always lift it up to the parent and pass it down as a prop. Or you can simply just memoize the component. 

References: <br>
https://react.dev/learn/render-and-commit <br>
https://react.dev/learn/state-as-a-snapshot <br>
https://kentcdodds.com/blog/optimize-react-re-renders <br>
https://www.reddit.com/r/reactjs/comments/15k98lf/why_does_passing_child_as_prop_to_parent_prevent/#:~:text=So%20that%20Child%20is%20passed,won't%20re%2Drender.
