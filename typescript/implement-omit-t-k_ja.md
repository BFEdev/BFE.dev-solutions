// @jichen257

`K extends keyof any` に注意, K は、任意のオブジェクトのキー タイプに制約されます。
```ts
type MyOmit<T, K extends keyof any> = {
  [P in keyof T as P extends K ? never : P]: T[P]
}
```