The difference is in notation and how these functions are used and interpreted in JS.

**function declaration** uses the keyword `function` to create a function.

If a function was declared by assigning to a property with the keyword `var`, `let`, or `const`, it's called the **function expression**.

```JavaScript

// 'function declaration'
function foo() {
  this.a = 'a';
}

// 'function expression'
const bar = () => {
  const a = 'a';
};
```

For **function declaration**, when the interpreter starts to parse the code block, it places all the declared functions in the local context. You can use this function by its name at any place in the code block;

Conversely, **function expression** will be inited only when the interpreter executes a line with the prop declaration. Before that, a value of the prop that holds the function is `undefined` and can't be used.

Also, **function declaration** has its own context that holds by the `this` keyword, and `this` points to the inner context of the function.

For **function expression**, the `this` keyword points to the outer (parent) context, and the function expression doesn't have its own `this` context.


See reference: [Function Expression vs Function Declaration](https://javascript.info/function-expressions#function-expression-vs-function-declaration)



Would you like to [contribute to the solution](https://github.com/BFEdev/BFE.dev-solutions/blob/main/question/function-expression-vs-function-declaration_en.md)? [Contribute guideline](https://github.com/BFEdev/BFE.dev-solutions#how-to-contribute)
