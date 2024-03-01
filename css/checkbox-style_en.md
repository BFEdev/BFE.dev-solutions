```
input {
  display: none;
}

span {
  display: flex;
  align-items: center;
}

span::before {
  content: '';
  display: block;
  width: 10px;
  height: 10px;
  border-radius: 5px;
}

input:checked + span::before {
  background-color: green;
}

input:not(:checked) + span::before {
  background-color: gray;
}
```
