# Week 2 Searching Assignment

## Task 1 Linear Search

Linear search is O(n) and this particular case would take 4 comparisons to find 8. 
It must compare to 2, then 4, then 6, and then finally 8.

## Task 2 Binary Search

Binary search is O(log n) and this particular case would only require one comparison since binary compares the 
middle value and keeps cutting the data set in half until it reaches the target value. 8 conveniently sits at
that halfway point.

## Task 3 Binary Search on a Large Dataset

Assuming the worst case possible, (that the target value sits at the very end of
the implicit search tree that binary search walks) it would take ~log2(100,000) comparisons 
because it divides the data set in half every unsuccessful comparison. In this case, it would  
take approximately 17.

## Task 4 Linear Search vs. Binary Search

```cpp
#include <vector>
#include <iostream>

int linearSearch(const std::vector<int>& arr, int target, int& comparisons){
    comparisons = 0;
    for (int i = 0; i < arr.size(); i++){
        comparisons++;
        if (arr[i] == target){
            return i + 1;
        }
    }

    return -1;
}

int binarySearch(const std::vector<int>& arr, int target, int& comparisons){

    int lo = 0, hi = arr.size() - 1, n = 0;
    comparisons = 0;
    while (lo <= hi){
        n++;
        comparisons++;
        int mid = lo + (hi - lo) / 2;
        if(arr[mid] == target) return n;
        else if (arr[mid] < target) lo = mid + 1;
        else hi = mid - 1;
    }
    return -1;
}

void runTrial(const std::vector<int>& arr, int target) {
    int linComp, binComp;
    int linResult = linearSearch(arr, target, linComp);
    int binResult = binarySearch(arr, target, binComp);

    std::cout << "Target: " << target << "\n";
    std::cout << "Linear search: " << (linResult != -1 ? "Found" : "Not found") << "\tComparisons:\t" << linComp << "\n";
    std::cout << "Binary search: " << (binResult != -1 ? "Found" : "Not found") << "\tComparisons:\t" << binComp << "\n\n";
}

int main(void) {
    std::vector<int> arr(100000);
    for (int i = 0; i < arr.size(); i++){
        arr[i] = i + 1;
    }

    runTrial(arr, 12);      //beginning
    runTrial(arr, 50000);   //middle
    runTrial(arr, 100000);  //end
    runTrial(arr, 100001);  //doesnt exist

    return 0;
}
```

## Analysis
Linear search's worst case scenario is that the target value is at the very end of the data set. 
Thus, it would require going through n values. Each comparison takes only 1 away from the remaining 
search space. Therefore, it is O(n).

Binary search's worst case scenario is that the target value sits at the very bottom of the search tree.
Each time it cuts the remaining search space in half, meaning that it would go through a 2^n data set in n
comparisons. Through taking log2 of both sides, we conclude it has roughly go through log2(n) values to get
to the worst case scenario position. Thus, it is O(log n).

In addition, binary search requires the data to be sorted from smallest to largest (or vice versa). The 
entire premise that cutting off half the remaining search space after finding an unsuccessful value hinges
on there being no other viable values either before or after it. Due to this assumption, binary does not work on 
unsorted data.

## Task 5 Randomized Search

### Part A - Pseudocode

My idea for a randomized search algorithm is to create a second array of indices (0 -> n-1).
I will then go linearly through that second array and use a random number generator to shuffle 
that index with a random index in the array. Thus, at the end, I will have an array of randomly shuffled
indices. Because it is a shuffle, no numbers are overwritten and they show up exactly once. Then,
I will use a for loop through the second array and use those indices to pick an element out of my first array
to check. That way, I am checking random values exactly once in the first array. If it matches the target, I will
return the place it is at (the value of the second array) and if not, I will return -1. 

It will look something like the following. 

randomSearch(array, target)

create second array called indices (n elements from 0 to n-1)

for (entire second array)

&emsp;int temp = indices[i];

&emsp;indices[i] = indices[RNG];

&emsp;indices[RNG] = temp;

for (entire second array)

&emsp;if (array[indices[i]] == target) //NOTICE: the index if from the second array

&emsp;&emsp;return indices[i];

return -1;

### Part B - Complexity Analysis

**Best case:** The best possibility is that the target element 
happens to be at the index that gets randomly swapped into the 
second array. Therefore, it still requires the entire set up costing
around ~n + 1 operations. Therefore, the best case scenario is O(n) time.

**Average case:** The average can be inferred that the target value sits at
the index of whatever n/2 is in the second array, meaning that we find it around
half way looking through the first array. That means we go through ~n + n/2 operations.
Therefore the average case scenario is O(n) time.

**Worst-case:** The worst case is that the index of the target value gets randomly swapped
to the very end of the second array. Therefore, we must do the entire setup (~n operations)
and then must go through the entire first array (~n operations). Therefore, the worst case 
is O(n) time. 

If the value is not present in the search space, it requires to go through the entire worst case
for us to not find it. Again, O(n) time. 

### Part C - Implementation

```cpp
int randomSearch(const std::vector<int>& arr, int target) {
    int n = static_cast<int>(arr.size());

    std::vector<int> indices(n);
    for (int i = 0; i < n; ++i) {
        indices[i] = i;
    }

    std::random_device rd;
    std::mt19937 rng(rd());
    for (int i = n - 1; i > 0; --i) {
        std::uniform_int_distribution<int> dist(0, i);
        int j = dist(rng);
        int temp = indices[i];
        indices[i] = indices[j];
        indices[j] = temp;
    } //I saw the Fisher-Yates method and used that to correct the bias in my original method.

    int comparisons = 0;
    for (int i = 0; i < n; ++i) {
        ++comparisons;
        if (arr[indices[i]] == target) {
            std::cout << "randomized search: \t" << comparisons << " comparisons\n";
            std::cout << "Found\n";
            return indices[i];
        }
    }

    std::cout << "randomized search: \t" << comparisons << " comparisons\n";
    std::cout << "Not found\n";
    return -1;
}
```
### Part D - Comparison
**1. Time complexity**

**Best case:**

Linear Search&emsp;O(1)

Binary Search&emsp;O(1)

Randomized Search&emsp;O(n)

**Average Case:**

Linear Search&emsp;O(n)

Binary Search&emsp;O(log n)

Randomized Search&emsp;O(n)

**Worst Case:**

Linear Search&emsp;O(n)

Binary Search&emsp;O(log n)

Randomized Search&emsp;O(n)

In terms of time complexity, binary search takes the cake, especially as data grows much larger.
The randomized search, while growing similar to the linear search, does not perform as well with smaller
data sets.

**2. Sorted Data:** The good thing about randomized and linear is that the data does not need to be sorted. Binary is must be
sorted either increasing or decreasing (depending on how the binary method is written).

**3. Memory or Bookkeeping Requirements:** The linear search is the most minimalist of the three as it only checks one index at a time. Binary must
keep track of lo, high, and mid. Randomized search requires a way to generate random numbers as well as
a whole other array to grab random indices from. 

**4. Practical Efficiency:** When searching 100,000 elements. The binary search is the clear winner as it can get to anything in at most 17
comparisons. Similar to what was previously mentioned, linear and randomized are the lesser choices for larger 
data sets such as this one. 

**5. Pros & Cons:** 

Linear is great for any array that is not too large, or is disorganized. It gets outclassed when data sets get larger
or if there is a pattern in the data that can be utilized. 

Binary is very strong when data grows larger. Unfortunately, it requires sorted data which can take extra time
to prepare. 

Randomized doesn't have any bias for picking numbers. But, it is just objectively slower than linear. 

**6. When to use:** Use linear with small, unsorted stuff. Use binary on sorted data. Use randomized when you need to be unpredictable.
