This solution is translated from [Arun Kumar](https://github.com/arun05hp)

以下提供十种方式

## 方法一：使用flex布局，结合margin属性

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

## 方法二：使用flex布局，结合align-items、justify-content属性

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

## 方法三：使用flex布局，结合justify-content和align-self属性

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

## 方法四：使用grid布局，结合margin属性
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

## 方法五：使用grid布局，结合place-items属性

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

## 方法六：使用grid布局，结合place-self属性

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

## 方法七：使用gird布局，结合align-self和justify-self属性

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

## 方法八：使用grid布局，结合align-items和justify-content属性

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

## 方法九：使用relative定位，结合tranform属性

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

## 方法十：使用relative定位，结合margin属性

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
