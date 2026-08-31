# Week 1 Array Assignment

## VIDEO FILE
https://youtu.be/mj95V5wn7uE

## Task 1
There are many ways to initialize an array. When initializing, you need to include the data type, the size, and the name of the array.
It may look something a little like below:

```cpp
//assuming we have #include <array> somewhere above
std::array<int, 100> num;
```
Here I have created an array called num with 100 uninitialized elements that are all ints.

## Task 2
Each element in the array above will be the size of an int (4 bytes). We could show this by writing something like:

```cpp
//assuming with have #include <iostream> somewhere above
std::cout << "Each element is " << sizeof(num[0]) << " bytes!" << std::endl;
```

## Task 3
For the following operations, it takes:

**Reading: 1 step** --> This is just a direct index.

**Searching for a value not contained within the array: 100 steps** --> We must go through the entire array.

**Insertion at the beginning of the array: 101 steps** --> We must shift everything over (100 steps) and then insert (1 step).

**Insertion at the end of the array: 1 step** --> We just add the new element at the end.

**Deletion at the beginning of the array: 99 steps** --> We can just shift everything over to the left (writing over the first element).

**Deletion at the end of the array: 1 step** --> We just remove the last element.

## Task 4
It would take N steps (O(n) time) as we are simply looking through the entire array just once. The cost would increase linearly.

## Task 5
In C (what I've used in the past), the array name itself decays into a pointer automatically:
```c
int arr[100];
int* p1 = arr; //points to first element
p1++;          //moves to the next element
```

In C++, since I'm using std::array, it doesn't decay to a pointer the same way a C-style array does. To get the memory address, you can use .data() or & like so:

```cpp
std::cout << "Address of array: " << num.data() << std::endl;
std::cout << "Address via &: " << &num[0] << std::endl;
```
Both will print the same address, that being the address of the first element.


# VIDEO FILE
