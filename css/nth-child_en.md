Solution by [Arun Kumar](https://github.com/arun05hp)

## Using Adjacent Sibling Selector (+) and :nth-child(n) CSS pseudo-class

```js
.row {
  background-color: #eee;
  height: 50px;
  border-top: 1px solid #ccc;
  border-bottom: 1px solid #ccc;
}

.row + .row {
  border-top:0;
}

.row:nth-child(even) {
  background-color: #ddd;
}
```

### Adjacent Sibling Selector (+)

```
It is placed between two CSS selectors. It matches only those elements matched by the second selector that are the next sibling element of the first selector.
```

### :nth-child(n) CSS pseudo-class

```
It takes a single argument that describes a pattern for matching element indices in a list of siblings.

n can be a number, a keyword (odd or even), or a formula (like an + b).
```
