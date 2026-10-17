# Week 8 Assignment/Project 1 Binary Search Trees

## Part 1 Construct a BST

Tree A: It is a valid BST because, for every key, the subtree to the right of it is larger than that value
and vice versa for the subtree to the left.

Tree B: It is an invalid BST because the right subtree of 30 is larger than 30 but also larger than the parent of 
the 30 node.

Tree C: It is a valid BST. The values follow the pattern

It is insufficient to only check a node and its immediate children for validating because those children may have children that validate the 
ordering pattern or the node itself could be violating the pattern as the child of another node. Those children of a node could
also be larger or smaller than the parent of a parent when they are not allowed to be.

## Part 2 Construct a BST

<img width="507" height="370" alt="image" src="https://github.com/user-attachments/assets/8078579b-74d1-4734-a1e6-b31083613015" />

Root: 45
Leaf nodes: 
