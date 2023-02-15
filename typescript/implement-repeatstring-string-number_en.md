// @jichen257

Two features of the `repeat` function can be seen from the analysis of the question: 1. Explicit critical conditions 2. Similar logic for each processing
Obviously, we can use **recursion** to process

Once the idea is clear, we analyze it according to the recursive triad:
1. first determine the parameters of each recursion: the current generalization T, the number of repetitions C
2. Next is the critical condition: `T['length'] extends C` , where T should take the value returned by the last recursion, so we add one of the recursion parameters: L (for last T)
3. finalize the recursion logic:
    - If the critical condition is not satisfied, take ``${T}${RepeatString<T, C, [T, . . L]>}`` (T repeats once, L's length + 1)
    - If it is satisfied, the last recursive return value is taken, i.e. `T`

Summing up, we can get
```ts
type RepeatString<T extends string, C extends number, L extends string[] = []> = L['length'] extends C
  ? ''
  : `${T}${RepeatString<T, C, [T, ...L]>}`;
```