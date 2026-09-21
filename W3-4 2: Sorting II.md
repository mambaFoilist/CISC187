# W3-4 2/3 Sorting II

## Task 1 Average Case Analysis of Insertion Sort

Assume an array contains N elements. 

Insertion sort starts out by dividing the array into a sorted and unsorted portion. It initially
takes the first element to be the sorted portion and the rest to be unsorted. 

Insertion sort will walk across the unsorted portion and for each element, determine where that element belongs in 
the sorted portion.
It will then, fittingly, insert that element into the sorted portion at that place. This grows the sorted portion
just a tad. Then, it continues through the unsorted portion until the sorted part has totally consumed the unsorted and everything is sorted.

Consult the diagram below for a worked through example:

<img width="815" height="896" alt="image" src="https://github.com/user-attachments/assets/5a84d424-ae80-46d1-802e-318eb5d88023" />

Pass i (i = 1 to N-1) compares the key against about i/2 elements of the sorted
portion on average, since the key lands near the middle. Summing over all passes:

$$\sum_{i=1}^{N-1} \frac{i}{2} = \frac{1}{2} \cdot \frac{N(N-1)}{2} = \frac{N(N-1)}{4} \approx \frac{N^2}{4}$$

The shifts follow the same pattern, so the total work is about N²/2 operations,
which is O(N²).

## Task 2 Changing the starting position of insertion sort.

### Part A

<img width="686" height="932" alt="image" src="https://github.com/user-attachments/assets/bd1190e7-a4e2-4702-80ba-3618c698b200" />

### Part B

<img width="791" height="932" alt="image" src="https://github.com/user-attachments/assets/962eeb8b-e3f6-4fae-95a1-60ff440af049" />

### Part C

<img width="870" height="870" alt="image" src="https://github.com/user-attachments/assets/baaa6ec2-30a2-4fea-aa01-8f9e762544f2" />

### Part D

Insertion normally starts at i = 1 because it needs the prefix to be sorted. An array of size 1 is always sorted.

It assumes that everything to the left of i is already sorted.

i = 2 and i = 3 do NOT guarantee that the entire array will be sorted. It only works if the first 2 or 3 elements, respectively,
are already sorted.

Reducing the number of iterations with out proper assumptions or compensation through modifying the algorithm can lead break
the rules. It is like how baking a cake without spraying the pan with oil or mixing the ingredients is theoretically
faster but can affect the end result. 

Through the end result, we can tell our "sorted" arrays are clearly not fully sorted. We have [1,2,3,5,4] and [1,2,5,4,3]. Cutting corners led to a poor result.

## Task 3

### Part A

Regardless of whether X is the first character, somewhere in the middle, the final character, or does not occur
in the string, it still takes the same exact time because it needs to go through the entire loop before returning a result.

The best case, average case, and worst case bound are all O(n).

Thus, if the boolean were to be flipped before the loop is done, it still needs to go through the thing, wasting time.

### Part B Improve the Algorithm

```cpp
#include <string>
//I like cpp :P
bool containsX(const std::string& str){
    for (size_t i = 0; i < str.length(); i++){
        if (str[i] == 'X'){
            return true;
        }
    }

    return false;
}
```

```js
function containsX(string) {
  for (let i = 0; i < string.length; i++) {
    if (string[i] === "X") {
      return true;
    }
  }
  return false;
}
```

### Part C Analyze the Improved Version

It's best case is that it is the first character. Thus, the best case bound is $\Omega(1)$.

The average case is that it is in the middle ~n/2. Thus, the average case bound is $\Theta(n)$.

The worst case is that it is either last or not in the string at all. Thus, the worst case bound is $O(n)$.

While the worst and average case is still the same, the early exit means that there is on average less operations because it doesn't need to waste
time getting to the end of the string to return a value.

## Analysis and Reflection

Insertion sort is quadratic because pass i compares against and shifts up to i elements: about i/2 on average and about i in the worst case. Summing over all
passes gives about n²/4 (average) or n²/2 (worst), both O(n²).

The starting position i = 1 is critical to the correctness of insertion sort because it assumes that the prefix is sorted. 
An array of 1 will always be sorted, but if you were to start with i = 2 or 3 or something not 1, you better be sure that that section
is sorted.

Similar to early exits, reducing the number of operations involves cutting down on unnecessary steps or potentially
executing the same tasks in a smarter way. You can still cut down on steps by half or a third, etc. but it still be the same Big-O
notation. Changing that means changing the growth of operations with the growth of inputs, often involving performing 
the task in a smarter way.

Two implementations with the same worst-case Big-O complexity can still perform differently in practice because not all implementations are the worst case.
It is highly likely that they will encounter datasets that do not need as much manipulation or comparisons, etc. and then the better of their best-case and average-time 
complexities will benefit.
