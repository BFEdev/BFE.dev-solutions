Looking at the code example, we can see that the requirment is to **remove the ? on each property name**.

To achieve this, we need to use the `-` , introduced in [TypeScript 2.8](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-8.html#improved-control-over-mapped-type-modifiers).

`-` allows us to remove modifiers like `readonly` and `?`.

```ts
type MutableRequired<T> = { -readonly [P in keyof T]-?: T[P] }; // Remove readonly and ?
type ReadonlyPartial<T> = { +readonly [P in keyof T]+?: T[P] }; // Add readonly and ?
```

So the answer to this question is simply a [Mapped Type](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html) with `-`.

```ts
type MyRequired<T> = { 
  [P in keyof T]-?: T[P] 
}
```
