// author by @jichen257

注意 `K extends keyof any`，表示 K 约束于`string | number | symbol`，即对象中键的所有可取类型
```ts
// 解法一
type MyOmit<T, K extends keyof any> = {
  [P in keyof T as P extends K ? never : P]: T[P]
}
```

当然，其中 `P in keyof T as P extends K ? never : P` 也可以简写为 `P in Exclude<keyof T, K>`，它们都表示"从T中去掉能代入K的成员后的类型P"，关于`Exclude`的用法这里不再赘述（后面有对应题目）

```ts
// 解法二
type MyOmit<T, K extends keyof any> = {
  [P in Exclude<keyof T, K>]: T[P]
}
```