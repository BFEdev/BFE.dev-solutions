// author by @jichen257

Kはkeyof any`を拡張し、`string | number | symbol`、すなわちオブジェクトのすべての望ましいタイプのキーにバインドすることに注意してください。
```ts
// 解決策1
type MyOmit<T, K extends keyof any> = {
  [P in keyof T as P extends K ? never : P]: T[P]
}
```

もちろん、PがKを拡張するようにTのキーの中の`P? never : P` は `P in Exclude<keyof T, K>` とも略せますが、どちらも「TからKに置き換えられるメンバーを取り除いた後の型P」という意味で、`Exclude` の使用はここでは繰り返しません（後に対応するトピックがあります）。

```ts
// 解決策2
type MyOmit<T, K extends keyof any> = {
  [P in Exclude<keyof T, K>]: T[P]
}
```