## Solution by [@Dharanish-V](https://github.com/dharanish-v)

* Add **white-space: nowrap;** to stop the text from wrapping to next line
* Add **overflow: hidden;** to hide the text overflowing outside its container element
* Add **text-overflow: ellipsis;** to truncate the text overflow and display ellipsis(**...**)

``` 
.one-line {
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}
```
***
