Solution by [Arun Kumar](https://github.com/arun05hp)

## Using Overflow, Text-Overflow, display, line-clamp and box-orient

```js
.max-three-lines {
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;
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

### -webkit-line-clamp: 3

```
This value specifies the number of lines after which the content will be clamped.

It only works in combination with the display property set to -webkit-box or -webkit-inline-box and the -webkit-box-orient property set to vertical.
```

### -webkit-box-orient: vertical;

```
The box lays out its contents vertically.
```
