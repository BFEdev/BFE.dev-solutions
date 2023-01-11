*Solution by => Uday Singh.*

``` javascript
/**
 * @param {Array<any>} promises - notice input might have non-Promises
 * @return {Promise<any[]>}
 */

function all(promises)  {

  // your code here
  const length = promises.length;
  const results =  new  Array(length);
  let promiseResolved =  0; // to maintain the count of total resolved promise.

  return  new  Promise((resolve, reject)  =>  {
    if(length ===  0){ 
      // if we are getting an empty array, will resolve to []
      resolve([]);
    }

    promises.forEach((promise, i)  =>  {
      if(promise instanceof  Promise){
        promise
          .then((value)  =>  {
            // Assigning each value to it's respective index in results.
            results[i]  = value;
            promiseResolved++;
            if(promiseResolved === length){
              resolve(results);
            }
          })
          .catch(reject);
      }  else  { 
        // if passes element type is other than Promise,
        // then no need to resolve just add that value to results array and increment our promiseResolved.
        results[i]  = promise;
        promiseResolved++;
    }
    });
  });
}
```
