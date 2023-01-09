// author by @jichen257

Note that `K extends keyof any`, which means that K binds to `string | number | symbol`, i.e. to all desirable types of keys in the object
```ts
// solution 1
type MyOmit<T, K extends keyof any> = {
  [P in keyof T as P extends K ? never : P]: T[P]
}
```

Of course, where `P in keyof T as P extends K ? never : P` can also be abbreviated to `P in Exclude<keyof T, K>`, which both mean "returns P by removing from T the union members that are assignable to K.", and the usage of `Exclude` is not repeated here (there is a corresponding topic later)

```ts
// solution 2
type MyOmit<T, K extends keyof any> = {
  [P in Exclude<keyof T, K>]: T[P]
}
```