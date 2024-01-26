So far this is JavaScript

###Let me try to understand this
so when the function is called with 3 arguments then it will mapped with variables a,b and c hence the length of arguments is 3.

Now we are trying to reassign the value of the argument like argument[0] = 'bfe' it will change the value of first argument which is referenced to the variable a.

Now when we do 
```
arguments[3] = 'dev'
```
will add the new value to 3rd index but the argument length remains the same and it is never mapped to the variable d

so variable d remains unchanged which means it will be undefined

Visit the below image and see how changes happed here-



![image](https://github.com/BFEdev/BFE.dev-solutions/assets/6812755/58715229-9acc-4317-a261-a5e3c70adc91)
