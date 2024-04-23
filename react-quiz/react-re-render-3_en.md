> This awesome solution is created by [@gsriram24]([https://github.com/pinglu85](https://github.com/gsriram24)) !


This problem enables us to understand the basics of React rendering and re-rendering and how React works. Before we dive into the details, let's look at the solution to the problem. 

```
'App'
'A'
'B'
'C'
'D'
'App'
'A'
'B'
'C'
'D'
```

This problem is very similar to [React re-render 1](https://bigfrontend.dev/react-quiz/React-re-render-1)

Now let's go through how rendering works in React, and how the console logs get printed. 

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

Now the question arises, why do some of the console log statements get printed twice? Does this mean the browser also re-paints each of the component's content? Let's look at this in the second step. 

### Rendering the component

Let's look at what **Rendering** is. Rendering is React calling your components. It is a function call to the component, hence everything in the function is executed. This explains how the console logs get printed. 

Note: This process is **recursive**. This means, that if the updated component returns some other component, React will render that component next, and if that component returns that component, it will render that component next, and so on. That explains why all the console logs get printed again. 

Now that we understand the underlying workings of rendering and render triggers, let's look at the flow at which the components are rendered in this problem. 

1. Component `App()` is called, Hence logging `'App'` to the console. 
2. Components `A()` and `D()` are called. <br>
    a. `'A'` is logged onto the console. We notice that `A()` returns `children`, which is in turn `B()`, 
         hence component `B()` is called. <br>
    b. `'B'` is logged onto the console. And component `C()` is called. <br>
    c. `'C'` is logged onto the console. <br>
    d. `'D'` is logged onto the console.
4. `useEffect` gets executed, and `state` is updated using the `setState` function. 
5. This triggers a **re-render** of the `App` component. Steps 1 and 2 are repeated. 

Now does this mean the DOM elements are also re-painted onto the browser? No. The browser elements are smartly updated by React, based on how they consume the state. In our case, none of the JSX elements use the `state` variable, hence none of the DOM elements are re-painted onto the browser. 


References: <br>
https://react.dev/learn/render-and-commit <br>
https://react.dev/learn/state-as-a-snapshot
