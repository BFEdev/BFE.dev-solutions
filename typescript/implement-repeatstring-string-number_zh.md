// @jichen257

分析题意可知 `repeat` 函数的两个特点：1. 明确临界条件 2.每次处理逻辑类似
显然可用 **递归** 来处理

明确思路后，我们根据递归三部曲来分析:
1. 先确定每次递归的参数: 当前泛型 T，重复次数 C 
2. 其次是临界条件: `T['length'] extends C` ,其中 T 应当取上一次递归返回的值，所以我们在递归参数中增加一项：L(表示last T)
3. 最后确定递归逻辑:
   - 如果不满足临界条件，取 ``${T}${RepeatString<T, C, [T, ...L]>}`` (T重复一次，L的长度+1)
   - 满足则取上一次的递归返回值，即 `T`

综上可得
```ts
type RepeatString<T extends string, C extends number, L extends string[] = []> = L['length'] extends C
  ? ''
  : `${T}${RepeatString<T, C, [T, ...L]>}`;
```

