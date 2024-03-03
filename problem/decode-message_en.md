> This solution was created by [@keego](https://github.com/keego) :)

## The Problem

Given a 2D array of characters, we're tasked with finding the "hidden" message contained within it by creating a diagonal path that zig-zags through the array and returning the characters collected along that path.

In the provided example, we see the following message
```
I B C A L K A
D R F C A E A
G H O E L A D
```
decodes to
```
IROCLED
```
i.e
```
I       L    
  R   C   E  
    O       D
```

## The Approach

One way to approach this is to have a starting point and a direction, and change the direction of travel based on certain boundary positions.
We do this until an end condition is met.

Since we have a 2D array, we can assume this message grid is represented as rows of columns, i.e.

```
const message = [
  ['I', 'B', ...],
  ['D', 'R', ...],
  ['G', 'H', ...],
]
```

Starting at row 0 column 0, we'll want to descend to the right (incrementing the row and incrementing the column),
then eventually ascend to the right (decrementing the row and incrementing the column).
We end when there are no columns remaining.
We can start with an empty string and, at each step, append the character found at the current position to the string.

We can represent this logically with the following pseudo code
```
// start at 0,0
// while there are still columns
//  1. append current character
//  2. if we've reached a row boundary
//      change direction
//  3. update position based on direction
// return accumulated string
```

## The Catches

We'll want to keep in mind the following edge cases:
- the naive empty input `[[]]`
- the other potential empty input `[]`
- inputs with only 1 row
- inputs with only 1 column

We should also be mindful of the ordering of the main parts of our loop and how they interact:
- capturing the character
- changing the direction
- updating position

What should our starting position and direction be to allow our logic to remain simple?

## The Solution

```js
/**
 * @param {string[][]} message
 * @return {string}
 */
function decode(message) {
  // the direction in which we update our row position
  // we want to start in the "upward" direction, since
  // that will be the initial direction we'll have whenever
  // we reach the top row boundary later on
  let direction = -1
  // current row and column indexes, starting top left
  let row = 0
  let column = 0
  // our final string to be built up as we traverse
  let decoded = ''

  // we continue to loop until there are no more columns,
  // as soon as the size of the current row stops being
  // greater than the column index, we stop.
  // we also include an optional chaining operator
  // to stop us in case there are no more rows.
  while (message[row]?.length > column) {
    // 1. append current character
    decoded += message[row][column]

    // 2. if we've reached a row boundary
    if (row === 0 || row === message.length - 1) {
      // change direction
      direction *= -1
    }

    // 3. update position based on direction
    row += direction
    column += 1
  }

  // return accumulated string
  return decoded
}
```

## The Test

```js
/**
 * @param {string[][]} message
 * @return {string}
 */
function decode(message) {
  let direction = -1
  let row = 0
  let column = 0
  let decoded = ''

  // log our input
  console.log({ message })

  while (message[row]?.length > column) {
    decoded += message[row][column]

    if (row === 0 || row === message.length - 1) {
      direction *= -1
    }

    row += direction
    column += 1
  }

  // log our output
  console.log({ decoded })

  return decoded
}

// given example input
const message1 = [
  'I B C A L K A',
  'D R F C A E A',
  'G H O E L A D',
].map(line => line.split(' '))

// run it and verify output
decode(message1)
```

We can also add additional test cases to verify our solution, as well as add additional logging for debugging.

## Complexity

Given `Nr` rows of `Nc` columns...

This solution should be linear time complexity, scaling with the number of columns `Nc`.
- That is the number of iterations we loop over, and all operations in the loop are O(1)

This solution should be linear space complexity, also scaling with the number of columns `Nc`.
- All variables are O(1) except the accumulated string, which will have `Nc` characters.
- This is non-recursive and calls no other functions so the stack size requirements will also be 1.
