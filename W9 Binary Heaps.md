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

The last node is stored in the last element of the array.

A newly inserted value is initially placed at the very end and then "sifted up."
Essentially, you compare the new value with the parent and if the new value is larger, you swap the values.
Repeat until the parent is larger.

IDK im struggling

I also have no clue, what even is a linked node implementation
