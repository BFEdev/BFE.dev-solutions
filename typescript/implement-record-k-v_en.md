Looking at the description, we need to somehow create an object out of the union type. 

This is perfect use case of [Mapped Type](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html).

```ts
type MyRecord<K, V> = {
   [P in K]: V
}
```

But only `string`, `symbol` and `number` can be used as object key, so we need to add constraint by `extends`

```ts
type MyRecord<K extends string | number | symbol, V> = {
   [P in K]: V
}
```

A useful trick is that we can use `keyof any` to shorten the restraint, since `keyof any` would return all the possible types of object keys.

```
type MyRecord<K extends keyof any, V> = {
   [P in K]: V
}
```
