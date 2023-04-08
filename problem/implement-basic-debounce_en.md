```javascript
function debounce(func, wait) {
  let timeout = 0
  return (...args) => {
    clearTimeout(timeout)
    timeout = setTimeout(() => func(...args), wait)
  }
}
```
