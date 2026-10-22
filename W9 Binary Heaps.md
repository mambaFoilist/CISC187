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
|4|1|N/A|N/A|
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

My loop checks that left is < n. It is so the node isn't a leaf. You check that right < n which it isn't. Therefore, we don't read that because that is reaching outside the vector. The larger child is simply the left. If the trickling value is less that the left, then we swap. Then, we are at a leaf so the next iteration hits case 2.

**Case 2**

The node is a leaf so we place the value and stop. There are no comparisons to make.

## Part 13 Test the Complete Heap

```cpp
//helper so i dont have to repeat this
void show(const MaxHeap& h, const string& label) {
    cout << "  " << label << " (size " << h.size() << "): ";
    if (h.empty()) cout << "<empty>" << endl;
    else h.display();
}

int main() {
    MaxHeap h;

    cout << "=== Initial state ===" << endl;
    cout << "  empty() = " << boolalpha << h.empty() << endl;
    show(h, "heap");

    cout << "\n=== 1. Insert into empty heap ===" << endl;
    h.insert(10);
    show(h, "after insert(10)");

    cout << "\n=== 2. Multiple insertions ===" << endl;
    h.insert(20);
    show(h, "after insert(20)");
    h.insert(5);
    h.insert(7);
    h.insert(8);
    h.insert(3);
    show(h, "after insert(5,7,8,3)");

    cout << "\n=== 3. Multi-swap trickle-up ===" << endl;
    h.insert(40);
    show(h, "after insert(40)");
    h.insert(50);
    show(h, "after insert(50)");

    cout << "\n=== 4. maximum() ===" << endl;
    cout << "  maximum() = " << h.maximum() << endl;

    cout << "\n=== 5/6. removeMax() with multi-swap trickle-down ===" << endl;
    bool ok = h.removeMax();
    cout << "  removeMax() returned " << ok << endl;
    show(h, "after removeMax");
    cout << "  new maximum() = " << h.maximum() << endl;

    cout << "\n=== Draining to one element ===" << endl;
    while (h.size() > 1) {
        h.removeMax();
        show(h, "after removeMax");
    }

    cout << "\n=== 7. removeMax() on one-element heap ===" << endl;
    ok = h.removeMax();
    cout << "  removeMax() returned " << ok << endl;
    cout << "  empty() = " << h.empty() << endl;
    show(h, "heap");

    cout << "\n=== 8. removeMax() on empty heap ===" << endl;
    ok = h.removeMax();
    cout << "  removeMax() returned " << ok << endl;

    return 0;
}
```

Proof from my terminal:

<img width="806" height="912" alt="image" src="https://github.com/user-attachments/assets/bdae02d2-4670-4b92-9120-857548113b97" />

## Part 14 Complexity Analysis

|Operation|Big-O|Explanation|
|---|---|---|
|Read Max|O(1)|The max is A[0] (i.e. direct access) so it is constant time|
|Insert|O(log n)|It requires you to push_back (constant time), then place the value (constant time) and then sift it up the tree (log n), meaning that it results in O(log n).|
|Remove Max|O(log n)|It requires you swap the last value (direct access) with the root (direct access) and then pop_back (constant time). This then requires sifting down the new root which will average (log n). This results in O(log n).|
|Search for an arbitrary value|O(n)|Unfortunately, it is not sorted like a BST meaning you will have to search every tree. You can prune subtrees which will take off time but you will have to search through on average n-ish elements. That makes it O(n).|
|Access last node in array representation|O(1)|This is just referencing A[n-1]. That is direct access which is O(1).|

## Part 15 Heap vs. Ordered Array

While the ordered array has better deletion, you can't delete something you never inserted. Thus, each deletion is paired with an insertion. To insert something as you are receiving data, the ordered array must shift around ~n elements resulting in O(n). To insert something, the binary heap must sift up an item through around log n tiers. 

Inserting and removing for an ordered queue, therefore, becomes n * O(n) + n * O(1) = O(n^2).

For a binary heap, this becomes n * O(log n) + n * O(log n) = O(n log n). This behaves much more efficiently.

## Part 16 Priority Queue Reasoning

1. Insert into the max-heap

Rule: place the new item in the next open spot (left to right, level by level), then sift up by swapping it with its parent while it is larger than the parent. In array form, the parent of index i is at index floor((i - 1) / 2).

| Step | Insert | Before sift-up | Swaps | Heap after (array) |
|---|---|---|---|---|
| 1 | A (4) | [4] | none (root) | [4] |
| 2 | B (9) | [4, 9] | 9 > 4, swap | [9, 4] |
| 3 | C (2) | [9, 4, 2] | 2 < 9, no swap | [9, 4, 2] |
| 4 | D (7) | [9, 4, 2, 7] | 7 > 4, swap; 7 < 9, stop | [9, 7, 2, 4] |
| 5 | E (10) | [9, 7, 2, 4, 10] | 10 > 7, swap; 10 > 9, swap | [10, 9, 2, 4, 7] |

2. Final heap

```
            10 (E)
          /        \
       9 (B)      2 (C)
      /     \
   4 (A)   7 (D)
```

Array: [10, 9, 2, 4, 7]

Check: every parent is greater than or equal to its children (10 ≥ 9, 2 and 9 ≥ 4, 7).

3. Processed first

Task E (priority 10). The root of a max-heap is always the maximum.

4. Remove the max and restore the heap

Rule: remove the root, move the last element into the root position, then sift down by swapping it with its larger child until it is larger than both children.

1. Remove 10. Move the last element (7) to the root: [7, 9, 2, 4]
2. The children of 7 are 9 and 2. The larger child is 9, and 7 < 9, so swap: [9, 7, 2, 4]
3. The only child of 7 is now 4. Since 7 > 4, stop.

Restored heap:

```
            9 (B)
          /       \
       7 (D)     2 (C)
      /
   4 (A)
```

Array: [9, 7, 2, 4]

5. Processed next

Task B (priority 9).

6. Why a heap is suitable

- Tasks are constantly being added and processed, so both operations matter. A heap performs both insertion and removal in O(log N), so neither becomes a bottleneck. An ordered array would cost O(N) for every new task.
- A heap only guarantees that the maximum is at the root. It does not fully sort the data, which is exactly enough to answer "which task is next?" and is cheaper than keeping everything sorted.
- A heap is stored compactly in a plain array using index arithmetic, with no pointers, which makes it memory-efficient and fast in practice.

## Analysis and Reflection

### Why a heap must be complete
Completeness means there are no gaps, so the heap maps directly onto an array, with the children of index i at 2i + 1 and 2i + 2. It also keeps the height at about log N. In my trace, the five tasks filled the indices [10, 9, 2, 4, 7] with no gaps.

### Why a max-heap is not the same as a BST
A BST orders left < parent < right. A max-heap only requires parent ≥ children, with no left/right rule. In my final heap, 9 sits left of 2 and its children 4 and 7 are both smaller than it, which would be invalid in a BST.

### Why arbitrary search is inefficient in a heap
Since there is no left/right ordering, a search cannot rule out a subtree, so it may have to check every node, which is O(N). For example, finding priority 4 gives no hint whether to go left or right.

### Why the root gives immediate access to the highest priority
Every parent is >= its children, so by transitivity the root is ≥ every node. Reading index 0 is O(1). In my trace, E (10) was at the root.

### Why insertion starts at the end of the array
The next open array slot is the only position that keeps the tree complete. The new item is then sifted up to fix the ordering. When E (10) was inserted, it went to index 4 and then swapped up twice to the root.

### Why deletion replaces the root with the last node
Removing the last node is the only removal that keeps the tree complete. Moving it to the root fills the hole, and sifting down restores the ordering. After removing 10, the last node 7 moved to the root and then swapped down below 9.

### Why trickle-down selects the larger child
The larger child is the only one that can become the parent of the other child and still satisfy parent >= children. When 7 was at the root, swapping with 9 worked. Swapping with 2 would have put 2 above 9, which breaks the heap.

### Why insertion and removal are O(log N)
A complete tree has height floor(log₂ N). Sift-up and sift-down each move at most one level per swap. My traces needed at most two swaps for 5 nodes, and floor(log₂ 5) = 2.

### Why weak ordering is useful for priority queues
A priority queue only ever needs the maximum, not a fully sorted order. Keeping only the parent ≥ children rule is cheap to maintain at O(log N) per operation, while full sorting would cost O(N) per insert, as in the ordered array. The heap does exactly the work the application needs and no more.
