@Jaski

## QuickSort explanation

The QuickSort algorithm involves doing two things:
- ### Partition
- ### Recursion

It works as follow:
1. Partition the array.
    - Take any random element inside the input array (let's name it as **pivot**).
    - Place the pivot in such a way that the elements towards the _left_ of pivot are _smaller_ while the elements to _right_ of pivot are _greater_ than it.

2. Once the pivot is in place, treat the subarrays to the left and right of pivot as their own arrays and recursively repeat step 1 and step 2.
3. The base case of recursion is subarrays with _zero_ or _one_ element.

> recursion is the terms which refers to a function calling itself. [See this example](https://bigfrontend.dev/problem/Generate-Fibonacci-Number-with-recursion). In order to prevent a function from infinitely calling itself, we need to add an exit condition. This condition is referred to as _base case_.

### Steps to Partition the array
- Assume the _pivot_ to be the **last** element of the array. So `pivotIndex` is `arr.length - 1`
- Take two pointer, `leftPointer`,starting at left most value of the array i.e `0` and `rightPointer`, starting at right most value of array, _excluding_ pivot i.e `arr.length - 2`.
- Compare the value at `leftPointer` with _pivot_. Move the `leftPointer` one step towards right **only** if _pivot_ is greater than value at `leftPointer`.
- Once the `leftPointer` stops, activate the `rightPointer`.
- Compare the value at `rightPointer` with _pivot_. Move the `rightPointer` one step towards left **only** if value at _rightPointer_ is greater the _pivot_.
- Once the `rightPointer` has stopped, we reach a crossroads. If the `leftPointer` is beyond or equal to `rightPointer`, break out of the loop and move to the last step. Otherwise, swap the values at `leftPointer` and `rightPointer`, increment the `leftPointer` and repeat the above _three_ steps.
- Swap the value at `leftPointer` with _pivot_ and return the `leftPointer`.

> Note: Placing the _pivot_ at right place do not essentially mean the elements towards left and right of pivot are now sorted. It just signifies whatever lies to left of _pivot_ is _smaller_ than it, whatever lies on right is _greater_ than it.

```

function quickSort(arr, leftIndex = 0, rightIndex = arr.length - 1) {

    // base case: one or zero element in an subarray
    if( rightIndex - leftIndex <= 0) return ;

    // pivot index is at the right place.
    let pivotIndex = partition(arr, leftIndex, rightIndex);

    // recursively sort the left most elements to pivot
    quickSort(arr, leftIndex, pivotIndex - 1);

    // recursively sort the right most elements to pivot
    quickSort(arr, pivotIndex + 1, rightIndex);
}


function partition(arr, leftPointer, rightPointer) {
    // we always choose the pivot to be the right most value
    let pivotIndex = rightPointer;

    // grab the pivot value
    let pivot = arr[rightPointer];
     
    // right pointer starts as second last element or to the immediate right of pivot
    rightPointer -= 1;

    while(true){
        // move the left pointer towards right as long as the pivot is greater than left pointer value
        while(arr[leftPointer] < pivot) {
            leftPointer += 1;
        }

        // move the right pointer towards left as long as it points to value greater than pivot
        while(arr[rightPointer] > pivot) {
            rightPointer -= 1;
        }

        // if left and right pointer have met or crossed, break out of the loop
        if(leftPointer >= rightPointer) {
            break;
        } else {
            // swap the values at left and right pointer
            [arr[leftPointer], arr[rightPointer]] = [arr[rightPointer], arr[leftPointer]];
            // move the left pointer forwards to start the comparison again
            leftPointer += 1;
        }
    }

    // swap the value at left pointer with pivot
    [arr[leftPointer], arr[pivotIndex]] = [arr[pivotIndex], arr[leftPointer]];
    
    // since the left pointer essentially now points to pivot, return it
    return leftPointer;

}

```

### Efficiency
The speed or efficiency of algorithm is determined by Big O notation. It essentially answer the below question:
> - How many steps does the algorithm takes for input with `N` elements ?
> - More importantly, what effect does the increase of input elements(`N`) have on the total number of steps ? What will be the trajectory of steps?

- ### Worst Case Scenario 
    **When the input array is reversely sorted** like `[5,4,3,2,1]`
    - `O(N^2)`  

- ### Average Case Scenario
    - `O(N logN)`

- ### Best Case Scenario
    - `O(N logN)`