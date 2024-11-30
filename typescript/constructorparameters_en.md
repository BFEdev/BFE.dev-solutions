> author by @beta3-z and Bing

The type is a conditional type that takes a generic parameter `T` that must extend a constructor function (a function that can be called with a `new` keyword).

The type then uses an `infer` keyword to extract the parameters of the constructor function and assign them to a new type variable `A`.

If `T` is indeed a constructor function, then the type returns `A`, which is a tuple of the parameters.

If `T` is not a constructor function, then the type returns `never`, which means no value can have this type.


```ts
type MyConstructorParameters<T extends { new(...args: any): any }> = T extends {
  new (...args: infer A): any
} ? A : never
```

Would you like to [contribute to the solution](https://github.com/BFEdev/BFE.dev-solutions/blob/main/typescript/constructorparameters_en.md)? [Contribute guideline](https://github.com/BFEdev/BFE.dev-solutions#how-to-contribute)
