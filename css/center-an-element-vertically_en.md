Solution by [Arun Kumar](https://github.com/arun05hp)

## Using Flex and Margin

```js
.outer {
  width: 100%;
  height: 100%;
  background-color: #efefef;
  display: flex;
}

.inner {
  width: 100px;
  height: 100px;
  background-color: #f44336;
  margin: auto;
}
```

## Using Flex, Align-Items and Justify-content

```js
.outer {
  width: 100%;
  height: 100%;
  background-color: #efefef;
  display: flex;
  align-items: center;
  justify-content: center;
}

.inner {
  width: 100px;
  height: 100px;
  background-color: #f44336;
}
```

## Using Flex, Justify-content and Align-Self

```js
.outer {
  width: 100%;
  height: 100%;
  background-color: #efefef;
  display: flex;
  justify-content: center;
}

.inner {
  width: 100px;
  height: 100px;
  background-color: #f44336;
  align-self: center;
}
```

## Using Grid and Margin

```js
.outer {
  width: 100%;
  height: 100%;
  background-color: #efefef;
  display: grid;
}

.inner {
  width: 100px;
  height: 100px;
  background-color: #f44336;
  margin: auto;
}
```

## Using Grid and Place-Items

```js
.outer {
  width: 100%;
  height: 100%;
  background-color: #efefef;
  display: grid;
  place-items: center;
}

.inner {
  width: 100px;
  height: 100px;
  background-color: #f44336;
}
```

## Using Grid and Place-Self

```js
.outer {
  width: 100%;
  height: 100%;
  background-color: #efefef;
  display: grid;
}

.inner {
  width: 100px;
  height: 100px;
  background-color: #f44336;
  place-self: center;
}
```

## Using Grid, Align-Self and Justify-Self

```js
.outer {
  width: 100%;
  height: 100%;
  background-color: #efefef;
  display: grid;
}

.inner {
  width: 100px;
  height: 100px;
  background-color: #f44336;
  align-self: center;
  justify-self: center;
}
```

## Using Grid, Align-Items and Justify-Content

```js
.outer {
  width: 100%;
  height: 100%;
  background-color: #efefef;
  display: grid;
  justify-content: center;
  align-items: center;
}

.inner {
  width: 100px;
  height: 100px;
  background-color: #f44336;
}
```

## Using Position and Transform

```js
.outer {
  width: 100%;
  height: 100%;
  background-color: #efefef;
  position: relative;
}

.inner {
  width: 100px;
  height: 100px;
  background-color: #f44336;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%,-50%);
}
```

## Using Position and Margin

```js
.outer {
  width: 100%;
  height: 100%;
  background-color: #efefef;
  position: relative;
}

.inner {
  width: 100px;
  height: 100px;
  background-color: #f44336;
  position: absolute;
  inset: 0;
  margin: auto;
}
```
