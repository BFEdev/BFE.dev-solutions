Looking at the code example, we can see that `Partial` means **turn each property to be nullable**.

To get the properties we can use `keyof`

```ts
type A = {
  a: number
  b: string
}

type C = keyof A // 'a' | 'b'
```

Then we can just use [Mapped Types](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html) to map each type to nullable by `| undefined`.

```ts
type MyPartial<T> = {
  [P in keyof T]?: T[P] | undefined
}
```

Also notice `?` is needed because the property key itself could be omitted as well.
