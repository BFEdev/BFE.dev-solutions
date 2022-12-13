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
