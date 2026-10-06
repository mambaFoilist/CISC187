# Week 7 Linked Lists

## Part 1 Understanding Nodes and Pointers

<img width="873" height="595" alt="image" src="https://github.com/user-attachments/assets/b94e9f98-88ce-4fa8-a417-b1a08b3c23bb" />

Head does not actually contain the data. It contains &first. first->next contains the pointer to the second. third-next contains nullptr.
If head were lost, you would lose access to the entire list.

## Part 2 Trace a Linked List

temp->data just pulls the data from each node and temp->next just makes us shift our focus to the next node.
The traversal eventually stops because once it pulls 40, the next node is just a nullptr so it stops.

Traversing a linked-list of n nodes requires going through n-many items. Thus, it is O(n) complexity to traverse the entire list.

## Part 3 Build a LinkedList Class

