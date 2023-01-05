// @jichen257

Pay attention to `K extends keyof any`, K constraint is the key type of any Object
```ts
type MyOmit<T, K extends keyof any> = {
  [P in keyof T as P extends K ? never : P]: T[P]
}
```