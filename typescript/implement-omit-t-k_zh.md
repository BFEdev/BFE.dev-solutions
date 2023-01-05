// @jichen257

注意`K extends keyof any`，K 约束为任意object的键类型
```ts
type MyOmit<T, K extends keyof any> = {
  [P in keyof T as P extends K ? never : P]: T[P]
}
```