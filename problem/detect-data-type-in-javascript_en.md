@vivekdogra02

## Detecting in data type

## Explanation

##### 1. Firstly, we need to find out the string representing the Object that can be achieved through Object.prototype.toString, basically in JavaScript, each built-in type has its own toString method And for Object It will return like this

```
eg: const arr = [1,2,3,4]
Object.prototype.toString.call(arr) // return [object Array]
const str ='hello'
Object.prototype.toString.call(str) // return [object String]
```

##### 2. We need a regex now to bifurcate the return object (/\[object (.\*)]/g)

##### 3. Now we need to execute the rawObject with the regex to find out the correct data type

##### 4. Lastly return the exact type which we get it from executing the regex.

```
 ***Simpler implementation***

const detectType = (input) => {
	const rawObject = Object.prototype.toString.call(input).toLowerCase();
	const typeRegex = /\[object (.*)]/g;
	const type = typeRegex.exec(rawObject)[1];
	return type;
}
```

```
eg: detectType([1,2,3,4]) // return array

detectType({a: 1, b: 1}); // return object

detectType(new Date()) // return date

detectType(new Map()) // return map

detectType( () => console.log('hello')) // return function
```

```
This will not be achieved through built-in typeof method in javascript.

typeof(new Map()) // return object

typeof([1,2,3]) // return object

typeof({a: 1, b:1}) // return object

typeof(new Date()) // return object
```

Would you like to [contribute to the solution](https://github.com/BFEdev/BFE.dev-solutions/blob/main/problem/detect-data-type-in-javascript_en.md)? [Contribute guideline](https://github.com/BFEdev/BFE.dev-solutions#how-to-contribute)
