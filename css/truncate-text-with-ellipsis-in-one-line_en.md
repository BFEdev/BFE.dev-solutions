Solution by [Arun Kumar](https://github.com/arun05hp)

## Using Overflow, White-Space and Text-Overflow

```js
.one-line {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
```

### text-overflow: ellipsis

```
It display the ellipsis `...` to represent clipped text. It doesn't force an overflow to occur.
```

### overflow: hidden

```
It cutout the part of the text that are overflowed from the container.
```

### white-space: nowrap;

```
Every sequence of two or more white-spaces will appear as a single white-space. It suppresses line breaks (text wrapping) within the source.
```
