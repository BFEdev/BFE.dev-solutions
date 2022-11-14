Looking at the code example, we can see that `Partial` means **turn each property to be optional**.

To get the properties we can use `keyof`

```ts
type A = {
  a: number
  b: string
}

type C = keyof A // 'a' | 'b'
```

Then we can just use [Mapped Type](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html) to map each type to optional by `?`.

```ts
type MyPartial<T> = {
  [P in keyof T]?: T[P]
}
```
