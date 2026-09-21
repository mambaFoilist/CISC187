# W3-4 3/3 Adapting Sorting

## Part A 

### Step 1 Analyze the Input Order

For my method, my idea was identical to the provided method:

```cpp
constexpr size_t N = 50;

double disorderRatio(const std::array<int, N>& arr) {
    int descents = 0;
    for (size_t i = 0; i + 1 < arr.size(); ++i) {
        if (arr[i] > arr[i + 1]) ++descents;
    }
    return static_cast<double>(descents) / (arr.size() - 1);
}
```

Important, this is a pretty primitive way of measuring disorder because it could be easily fooled by structured input.
Say we had [26...50, 1...25]. Clearly, the array is pretty messed up and is around half disordered. However, my method would only find one 
"inversion".

### Step 2 Define a Threshold

My method will return a number between 0 and 1, inclusive. Therefore, I will arbitrarily define

Best/Nearly Sorted: less than 0.33

Average/Partially Ordered: between 0.33 and 0.66, inclusive

Worse/Highly Reverse-Ordered: greater than 0.66

While a little primitive, my strategy is to cut the three tiers into 3 *equal chunks.

*The middle chunk is infinitesimally larger. 

### Step 3 Selecting the Sorting Algorithm

My strategy will be to choose insertion sort if it is Best/Nearly Sorted and selection sort if it is Average or Worse.


Here is my code in its entirety:

```cpp
#include <iostream>
#include <string>
#include <cstdlib>
using namespace std;

const int N = 50;

double disorderRatio(int arr[]) {
    int descents = 0;
    for (int i = 0; i < N - 1; i++) {
        if (arr[i] > arr[i + 1]) {
            descents++;
        }
    }
    return (double) descents / (N - 1);
}

// 0 = best, 1 = average, 2 = worst
int classify(double ratio) {
    if (ratio < 0.33) {
        return 0;
    } else if (ratio <= 0.66) {
        return 1;
    } else {
        return 2;
    }
}

string className(int c) {
    if (c == 0) return "Best/Nearly Sorted";
    if (c == 1) return "Average/Partially Ordered";
    return "Worst/Highly Reverse-Ordered";
}

void selectionSort(int arr[]) {
    for (int i = 0; i < N - 1; i++) {
        int minIndex = i;
        for (int j = i + 1; j < N; j++) {
            if (arr[j] < arr[minIndex]) {
                minIndex = j;
            }
        }
        int temp = arr[i];
        arr[i] = arr[minIndex];
        arr[minIndex] = temp;
    }
}

void insertionSort(int arr[]) {
    for (int i = 1; i < N; i++) {
        int key = arr[i];
        int j = i - 1;
        while (j >= 0 && arr[j] > key) {
            arr[j + 1] = arr[j];
            j--;
        }
        arr[j + 1] = key;
    }
}

void printArray(int arr[]) {
    for (int i = 0; i < N; i++) {
        cout << arr[i] << " ";
    }
    cout << endl;
}

void runTest(string label, int arr[]) {
    double ratio = disorderRatio(arr);
    int c = classify(ratio);

    cout << "=== " << label << " ===" << endl;
    cout << "Disorder ratio: " << ratio << endl;
    cout << "Classification: " << className(c) << endl;

    cout << "Before: ";
    printArray(arr);

    if (c == 0) {
        cout << "Algorithm: Insertion Sort" << endl;
        insertionSort(arr);
    } else {
        cout << "Algorithm: Selection Sort" << endl;
        selectionSort(arr);
    }

    cout << "After:  ";
    printArray(arr);
    cout << endl;
}

int main() {
    int nearlySorted[N];
    int randomArr[N];
    int reversed[N];

    srand(42);
    for (int i = 0; i < N; i++) {
        nearlySorted[i] = i + 1;
        reversed[i] = N - i;
        randomArr[i] = rand() % 100 + 1;
    }

    // mess up the nearly sorted one a little (swap two pairs)
    nearlySorted[10] = 12;
    nearlySorted[11] = 11;
    nearlySorted[30] = 32;
    nearlySorted[31] = 31;

    runTest("Nearly sorted", nearlySorted);
    runTest("Random", randomArr);
    runTest("Reversed", reversed);

    return 0;
}
```
## Part B

Here is a snippet of my code that runs this:

```cpp
double disorderRatio(int arr[]) {
    int descents = 0;
    for (int i = 0; i < N - 1; i++) {
        if (arr[i] > arr[i + 1]) {
            descents++;
        }
    }
    return (double) descents / (N - 1);
}

// 0 = best, 1 = average, 2 = worst
int classify(double ratio) {
    if (ratio < 0.33) {
        return 0;
    } else if (ratio <= 0.66) {
        return 1;
    } else {
        return 2;
    }
}

string className(int c) {
    if (c == 0) return "Best/Nearly Sorted";
    if (c == 1) return "Average/Partially Ordered";
    return "Worst/Highly Reverse-Ordered";
}

void runTest(string label, int arr[]) {
    double ratio = disorderRatio(arr);
    int c = classify(ratio);

    cout << "=== " << label << " ===" << endl;
    cout << "Disorder ratio: " << ratio << endl;
    cout << "Classification: " << className(c) << endl;
}

int main() {
    int arr[N];
    cout << "Enter " << N << " integers: ";
    for (int i = 0; i < N; i++) {
        cin >> arr[i];
    }
    runTest("User input", arr);
    return 0;
}
```


I used 3 test runs to show each one working as intended:

<img width="386" height="70" alt="image" src="https://github.com/user-attachments/assets/323e9acf-c6b9-4ac8-89b8-aa47e0ee24c2" />

<img width="398" height="66" alt="image" src="https://github.com/user-attachments/assets/cc427711-0c81-49de-b3d7-13e42a8942a8" />

<img width="420" height="67" alt="image" src="https://github.com/user-attachments/assets/e82deb33-945d-4b55-82d9-d784bd18c0d2" />

## Part C

My algorithm examines every adjacent pair once, so N-1 pairs (49 when N = 50), one comparison each.

The number of operations scales linearly with N: 49 comparisons at N = 50, 99 at N = 100, 999 at N = 1000. 
Doubling N roughly doubles the work.

Thus, it is O(n) time.

No, it does not change the Big-O of the whole process. The total is the examination plus the sort:

- Insertion sort chosen: O(n) + O(n) = O(n)
- Selection sort chosen: O(n) + O(n^2) = O(n^2), the n^2 term dominates so the examination is effectively ignored.

So it only adds a constant factor.

## Part D

Similar to above,

Best/Nearly Sorted: ratio < 0.33

Average/Partially Ordered: 0.33 <= ratio <= 0.66

Worst/Highly Reverse-Ordered: ratio > 0.66

It simply categorizes them by comparing the value. This is what my classify() method is for. 
I split [0, 1] into three roughly equal chunks. It's simple, easy to explain, and applied the same way 
in Parts A and B. Sorted data has a ratio of 0, reversed data has 1, and random data lands around 0.5 
(my random test gave 0.53), so the tiers line up with what I'd expect from those inputs.

The limitation is what I noted in Step 1. Insertion sort's big cost is the number of inversions (out of order pairs, 
not just adjacent ones). Descents are only a cheap proxy, so a low ratio doesn't guarantee a cheap 
insertion sort. [26...50, 1...25] has one descent but 625 inversions.

I chose insertion for more sorted stuff because it doesn't need to shift as much around and, under ideal conditions,
is ~O(n).

I chose selection for more disordered to minimize swaps. On reversed data, insertion sort shifts every element all the way
to the front, which is about n^2/2 writes. Selection sort still does about n^2/2 comparisons, but at most n-1 swaps.
For average data it's a closer call (insertion does about n^2/4 shifts), so there I picked selection for its predictable cost.

### Time Complexity

Selection sort is O(n^2) regardless of order. It always scans the whole unsorted part to find the minimum, with no early exit. That's n(n-1)/2 comparisons whether the array is sorted, reversed, or random.

Insertion sort approaches O(n) on sorted or nearly sorted data. If it's already sorted, the while loop fails immediately for every element, so it's n-1 comparisons and no shifts. Total shifts equal the number 
of inversions, so few inversions means about n work.

Insertion sort is O(n^2) on average and worst. Random data has about n(n-1)/4 inversions and reversed data has n(n-1)/2. Each inversion is one shift, so the work grows with n^2.

Same Big-O can still behave differently. Big-O drops constants and lower order terms. On random data insertion sort does about half the comparisons of selection sort but far more writes, while selection sort 
does at most n-1 swaps but can never finish early.

## Analysis and Reflection

You can determine the size, largest, smallest, average, standard deviation, etc. Some may require extra calculations but 
none of these require a sorted algorithm.

It adds extra up-front computational cost which could hurt the best-case bound. In the time that you examine the input,
a linear search could have pulled the target in the first spot.

It is not always preferable, especially in situations like just mentioned. However, given you know some patterns in data,
choosing an adaptive strategy can significantly shave off time.

As the size increases and/or the data becomes more disordered, it will impact how you select your algorithm. Finding that your input is disordered,
for example, could rule out binary search. The information you gain about your input will help you pick the right tool for the job.

Big-O notation alone may not always be sufficient when comparing algorithms because it removes information. Two
algorithms may have the same Big-O time but take 3N operations vs 10N operations. When n is not too large, one algorithm will perform
much better relatively. Also, it may not factor in the necessary conditions that it assumes of the data. Two algorithms
may have the same Big-O time but one might be better fit for more datasets.

What I observed in testing: the nearly sorted array (two swapped pairs) had a ratio of 0.04 and went to insertion sort,
the random array had 0.53 and went to selection sort, and the reversed array had 1.0 and went to selection sort. The classification
matched what I expected for these inputs, but my [26...50, 1...25] example shows a case where it would pick wrong.
