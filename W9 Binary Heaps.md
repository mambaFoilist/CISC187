# Week 9 Binary Heaps

## Part 1 Is it a Valid Max-Heap?

Heap A:

Yes. While splayed out it is not necessarily "ordered," the every child is less than or equal to the parent node,
satisfying the condition for a max-heap.

Heap B:

No. All of the children are smaller than the parents. However, these heaps must be filled in from left to right. There
is an empty spot right before 40, invalidating this heap.

Heap C:

No. 95, a child of 90, is greater than. That is not valid for these types of heaps.

## Part 2 Heap vs. Binary Search Tree

This is a valid max-heap tree because every child node is less than its respective parent node. 

This is not necessarily a valid binary search tree because the values in the left subtrees are not smaller than the parent
and the right subtrees are not necessarily larger than the parent node.

No. You must search for each split as there is no way to discern which path it may be down.

In a heap, both children will be smaller than the parent yet we have no relation for the children themselves. 
Thus, we must search every possible path. While pruning the tree can help, it will still require going through roughly n
elements of the heap. That results in O(n).

The peek can be accessed immediately. It is A[0].

## Part 3 Represent a Heap Using an Array

|Index|Value|
|---|---|
|0|90|
|1|70|
|2|80|
|3|20|
|4|40|
|5|60|
|6|50|

|Index|Parent|Left|Right|
|---|---|---|---|
|1|0|3|4|
|2|0|5|6|
|4|1|N/A||N/A|
|5|2|N/A|N/A|

## Part 4 Why Use an Array?

Because the heap contains no pointers like a regular BST, it uses size n to find the last node to place things.

The last node is always at n - 1 because it is zero indexed.

A newly inserted value is first placed at index n.

Removing the final array element preserves the complete-tree structure because removing it does not create any gaps. If you were to remove any element before n - 1, that would ruin the structure and create a gap. The resulting heap will be smaller but it is still a valid structure.

A linked-node implementation would make locating the last node more complicated because instead of referencing it by arithmetic, you would need to travel along the nodes to find the very bottom, potentially having to go down several subtrees.

## Part 5 Implement a MaxHeap Class

```cpp
#include <iostream>
#include <vector>
#include <utility>
#include <stdexcept>

using namespace std;

class MaxHeap {
private:
    vector<int> heap;

    int parentIndex(int index) const; //done
    int leftChildIndex(int index) const; // done
    int rightChildIndex(int index) const; //done

    void trickleUp(int index); //done 
    void trickleDown(int index); //done

public:
    bool empty() const; // done
    int size() const; // done 

    int maximum() const; //done

    void insert(int value); //done
    bool removeMax(); 

    void display() const;
};

// ====== private ======
int MaxHeap::parentIndex(int index) const{
    if(index == 0){
        throw std::out_of_range("parentIndex() called on parentless node");
    }
    return (int) ((index - 1)/ 2);
}

int MaxHeap::leftChildIndex(int index) const{
    return 2 * index + 1;
}

int MaxHeap::rightChildIndex(int index) const{
    return 2 * index + 2;
}

void MaxHeap::trickleUp(int index){
    while (index > 0 && (heap[parentIndex(index)] < heap[index])){
        int p = parentIndex(index);
        std::swap(heap[index], heap[p]);
        index = p;
    }
}


void MaxHeap::trickleDown(int index) {
    int n = size();

    while (leftChildIndex(index) < n) {// gaurd
        int left  = leftChildIndex(index);
        int right = rightChildIndex(index);

        //pick larger + check if right exists
        int larger = left;
        if (right < n && heap[right] > heap[left]) {
            larger = right;
        }

        //means it is larger than both and we are done
        if (heap[index] >= heap[larger]) {
            break;
        }

        std::swap(heap[index], heap[larger]);
        index = larger;
    }
}

// ====== public ======
bool MaxHeap::empty() const {
    return heap.size() == 0;
}

int MaxHeap::size() const{
    return heap.size();
}

int MaxHeap::maximum() const{
    if(empty()){
        throw std::out_of_range("maximum() called on empty heap");
    }
    return heap[0];
}

void MaxHeap::insert(int value){
    heap.push_back(value);
    trickleUp(size() - 1);
}

bool MaxHeap::removeMax(){
    if(empty()){
        return false;
    }
    heap[0] = heap[size() - 1];
    heap.pop_back();
    trickleDown(0);
    return true;
}

void MaxHeap::display() const{
    for(size_t i = 0; i < heap.size(); i++){
        std::cout << heap[i] << " ";
    }
    std::cout << std::endl;
}
```

## Part 6 Implement the Index Functions

```cpp
int MaxHeap::parentIndex(int index) const{
    if(index == 0){
        throw std::out_of_range("parentIndex() called on parentless node");
    }
    return (int) ((index - 1)/ 2);
}

int MaxHeap::leftChildIndex(int index) const{
    return 2 * index + 1;
}

int MaxHeap::rightChildIndex(int index) const{
    return 2 * index + 2;
}

```

The root is the very first value in the array. There are no values to the left of it.

You check whether that child index is within the size of the vector.

The index simply performs a bitwise shift and increments, meaning that the possible children indices grow with the same shape of the tree. It requires it to be complete, otherwise that previously mentioned formula may drop you into nothing (i.e. a gap).

## Part 7 Heap Insertion

```cpp
void MaxHeap::insert(int value){
    heap.push_back(value);
    trickleUp(size() - 1);
}
//AND ITS HELPER TRICKLEUP
void MaxHeap::trickleUp(int index){
    while (index > 0 && (heap[parentIndex(index)] < heap[index])){
        int p = parentIndex(index);
        std::swap(heap[index], heap[p]);
        index = p;
    }
}
```

### Manual Trace

Start with:

[90, 70, 80, 20, 40, 60, 50]

Insert: 85

| Current Index | Parent Index | Current Value | Parent Value | Action |
|---|---|---|---|---|
| 7 | 3 | 85 | 20 | 85 > 20 -> swap |
| 3 | 1 | 85 | 70 | 85 > 70 -> swap |
| 1 | 0 | 85 | 90 | 85 < 90 -> stop |

### Final Array

[90, 85, 80, 70, 40, 60, 50, 20]

### Final Tree

```
          90
        /    \
      85      80
     /  \    /  \
   70   40  60   50
  /
20
```

### Reasoning

Placing it at the end of the array ensures that the shape stays valid. We then use trickleUp() to corerct the order of it, ensuring a valid maxheap.

## Part 8 Accessing the Maximum

```cpp
int MaxHeap::maximum() const{
    if(empty()){
        throw std::out_of_range("maximum() called on empty heap");
    }
    return heap[0];
}
```

### Analysis

Reading the maximum is direct access at index 0. This is a constant time action so it is O(1). Removing it requires ensuring the tree stays valid. To do so, you need to swap the very last spot with the max (constant), kill the max with push_back (~constant), and then trickling the new root to ensure order (log n ). That results in O(log n).

## Part 9 Removing the Root

```cpp
bool MaxHeap::removeMax(){
    if(empty()){
        return false;
    }
    heap[0] = heap[size() - 1];
    heap.pop_back();
    trickleDown(0);
    return true;
}
```

## Part 10 Trickle-Down

```cpp
void MaxHeap::trickleDown(int index) {
    int n = size();

    while (leftChildIndex(index) < n) {// gaurd
        int left  = leftChildIndex(index);
        int right = rightChildIndex(index);

        //pick larger + check if right exists
        int larger = left;
        if (right < n && heap[right] > heap[left]) {
            larger = right;
        }

        //means it is larger than both and we are done
        if (heap[index] >= heap[larger]) {
            break;
        }

        std::swap(heap[index], heap[larger]);
        index = larger;
    }
}
```

### Manual Trace

Begin with the max-heap:

[100, 88, 75, 60, 70, 40, 50, 20, 30]

Remove the maximum: 100

Move the last element (30) to the root and remove the last slot:

[30, 88, 75, 60, 70, 40, 50, 20] (size = 8)

| Current Index | Left Child | Right Child | Larger Child | Action |
|---|---|---|---|---|
| 0 (30) | 1 (88) | 2 (75) | 1 (88) | 30 < 88 -> swap |
| 1 (30) | 3 (60) | 4 (70) | 4 (70) | 30 < 70 -> swap |
| 4 (30) | 9 (none) | 10 (none) | — | 9 ≥ size 8, leaf -> stop |

### Final Array

[88, 70, 75, 60, 30, 40, 50, 20]

### Final Tree

```
          88
        /    \
      70      75
     /  \    /  \
   60   30  40   50
  /
20
```

## Part 11 Why the Larger Child?

1. 90 should be selected.

2. If 70 were to be selected, then it would become the parent. Thus, that would result in 70 parenting 90 which is not valid for a maxHeap.

3. Choosing the larger child helps preserve the max-heap condition because that will imply that once it becomes a parent, it is still larger than the other child that didn't get picked. (Like choosing the worthiest heir to the throne :P).

## Part 12 One-Child Edge Case 

**Case 1**


**Case 2**

