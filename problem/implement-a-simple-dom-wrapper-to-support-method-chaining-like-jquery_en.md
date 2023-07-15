> [GeekyAsif](https://github.com/geekyasif)
```
function $(el) {

  return {
    css: function(property, value){

      const elements = el instanceof HTMLElement ? [el] : document.querySelectorAll(el)

      elements.forEach((element) => {
          element.style[property] = value
      })

      return this;
    }
  }
}
```
