Looking at the code example, we can see that the requirment is to add _readonly_ on each property name.

To achieve this, we can just use [Mapped Type](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html), which allows us to basically map the property to the readonly verison.

```ts

type MyReadonly<T> = { 
  readonly [P in keyof T]: T[P] 
}
```
