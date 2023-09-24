# Output
`2` logged first  
<br>
`1` logged next



## Code Explanation

The code outputs the following values to the console using `console.log`:

1. **"2"** is logged first: This log occurs during the initialization of `state2` using the `useState` hook with a function as the initial state. When `state2` is initialized, this function is called, resulting in "2" being logged to the console.

2. **"1"** is logged next: This log statement outputs the value of `state1`, which is initialized with the value "1" using the `useState` hook.

3. The `useEffect` hook is used to update `state1` to the value "3" with `setState1(3)`, but this effect runs with an empty dependency array (`[]`), meaning it runs only once after the initial render. In this case, it doesn't result in any additional console logs.

## Conclusion

This code snippet demonstrates the behavior of React hooks `useState` and `useEffect`. It showcases how to initialize state variables using both a constant value and a function, and it highlights the order of execution of these hooks during component rendering. Understanding these concepts is fundamental when working with React components.
