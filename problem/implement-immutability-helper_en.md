
There is no solution yet.

Would you like to [contribute to the solution](https://github.com/BFEdev/BFE.dev-solutions/blob/main/problem/implement-immutability-helper_en.md)? [Contribute guideline](https://github.com/BFEdev/BFE.dev-solutions#how-to-contribute)



/**
 * @param {any} data
 * @param {Object} command
 */
// function update(data, command) {
//   // your code here
// }

function update(original, updates) {
  if (typeof updates === 'object' && updates !== null) {
    if ('$push' in updates) {
      return original.concat(updates.$push);
    }
    if ('$set' in updates) {
      return updates.$set;
    }
    if ('$merge' in updates) {
      if(typeof original === 'object'){
        return { ...original, ...updates.$merge };
      }else {
        throw 'error';
      }
      
    }
    if ('$apply' in updates) {
      return updates.$apply(original);
    }

    const updated = Array.isArray(original) ? [...original] : { ...original };
    for (const key in updates) {
      if (Object.prototype.hasOwnProperty.call(updates, key)) {
        updated[key] = update(updated[key], updates[key]);
      }
    }
    return updated;
  }
  
  return updates;
}

// // Example usage
// const arr = [1, 2, 3, 4];
// const newArr = update(arr, { 0: { $apply: (item) => item * 2 } });
// console.log(newArr); // [2, 2, 3, 4]

// const state = {
//   a: {
//     b: {
//       c: 1,
//     },
//   },
//   d: 2,
// };

// const newState = update(state, { a: { b: { c: { $set: 3 } } } });
// console.log(newState); // { a: { b: { c: 3 } }, d: 2 }
