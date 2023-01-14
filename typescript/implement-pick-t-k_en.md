Looking at the description, `MyPick<T, K>` should generate an object type which has property names listed in K, so we should use [Mapped Type](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html).

```ts
type MyPick<T, K> = {
  [P in K]: T[P]
}
```

This doesn't work since TypeScript complaints about `T[P]` unless `P` is the property key of `T`, we can add the type constraint on K. 


```ts
type MyPick<T, K extends keyof T> = {
  [P in K]: T[P]
}
```
