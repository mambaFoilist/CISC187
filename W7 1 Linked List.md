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

Here are all of my implementations.

```cpp
LinkedList::LinkedList() : head(nullptr) {};

bool LinkedList::empty() const{
    return head == nullptr;
}

void LinkedList::display() const{
    Node* temp = head;
    while (temp != nullptr){
        std::cout << temp->data << std::endl;
        temp = temp -> next;
    }
}

bool LinkedList::search(int value) const{
    Node* temp = head;
    while (temp != nullptr){
        if(temp->data == value){
            return true;
        }
        temp = temp->next;
    }
    return false;
}

void LinkedList::insertFront(int value){
    Node* n = new Node{value, head};
    head = n;
}

void LinkedList::insertBack(int value){
    Node* n = new Node{value, nullptr};

    if (head == nullptr) {
        head = n;
        return;
    }

    Node* temp = head;
    while (temp->next != nullptr){
        temp = temp->next;
    }

    temp->next = n;
}

bool LinkedList::insertAfter(int target, int value){
    Node* temp = head;
    while (temp != nullptr){
        if (temp->data == target) {
            Node* n = new Node{value, temp->next};
            temp->next = n;
            return true;
        }
        temp = temp->next;
    }

    return false;
}

bool LinkedList::remove(int value) {
    Node* prev = nullptr;
    Node* temp = head;

    while (temp != nullptr) {
        if (temp->data == value) {
            if (prev == nullptr) {
                head = temp->next;
            } else {
                prev->next = temp->next;
            }
            delete temp;
            return true;
        }
        prev = temp;
        temp = temp->next;
    }
    return false;
}

LinkedList::~LinkedList() {
    Node* temp = head;
    while (temp != nullptr) {
        Node* nextNode = temp->next;
        delete temp;
        temp = nextNode;
    }
}
```

## Part 4 Initialize the List

Here is my LinkedList() constructor.

```cpp
LinkedList::LinkedList() : head(nullptr) {};
```

I like these kind of constructors :).

Here is my empty() function.

```cpp
bool LinkedList::empty() const{
    return head == nullptr;
}
```

The order of pointer updates matter because if you unlink the chain or change a pointer in the linked list, it is possible to lose the rest of the list (assuming you didn't save it anywhere).

If that were to happen, you would lose the whole thing.

insertFront() does not require to traverse the list because the place you need to put it is the first item. Thus, it is O(1) access. The inserting process is constant time as well because it is just swapping a pointer or two. Thus, it is O(1).

## Part 6 Insert at the End

Here is my insertBack(int value) method.

```cpp
void LinkedList::insertBack(int value){
    Node* n = new Node{value, nullptr};

    if (head == nullptr) {
        head = n;
        return;
    }

    Node* temp = head;
    while (temp->next != nullptr){
        temp = temp->next;
    }

    temp->next = n;
}
```

It reuqires walking through the entire list, n many items, to find where to place the value. Inserting, as mentioned before, is incredibly simple. Thus, it is O(n).

Maintaining a tail pointer, like inserting at the front, makes accessing the point of implementation constant time. Thus, it would make it O(1) overall instead of linear.

## Part 7 Search the List

Here is my search function:

```cpp
bool LinkedList::search(int value) const{
    Node* temp = head;
    while (temp != nullptr){
        if(temp->data == value){
            return true;
        }
        temp = temp->next;
    }
    return false;
}
```

The best case is that it is in the first node. That is accessing the first element and is thus O(1). 

The worst case is that it is either at the very end or not in there at all. That requires going through n many items and is thus O(n).

## Part 8 Insert After a Specific Value

Here is my method:

```cpp
bool LinkedList::insertAfter(int target, int value){
    Node* temp = head;
    while (temp != nullptr){
        if (temp->data == target) {
            Node* n = new Node{value, temp->next};
            temp->next = n;
            return true;
        }
        temp = temp->next;
    }

    return false;
}
```

It would require making the node before point to our new node and making our new node point to the node previously after node.

The pointer insertion is just swapping 2 pointers, and setting values. Those are all constant time operations and thus is O(1). However, to get to that point, you need to move through the elements of list. That is a O(n) process and makes the entire thing O(n).

## Part 9 Delete a Node

Here is my function:

```cpp
bool LinkedList::remove(int value) {
    Node* prev = nullptr;
    Node* temp = head;

    while (temp != nullptr) {
        if (temp->data == value) {
            if (prev == nullptr) {
                head = temp->next;
            } else {
                prev->next = temp->next;
            }
            delete temp;
            return true;
        }
        prev = temp;
        temp = temp->next;
    }
    return false;
}
```

Deleting any other node just links the before node to the node after the current node. However, with the head node, there is no before node and the only reference to that specific node is the head member variable itself. Thus, you must specifically set the next node to be the new head.

When unlinking a node, you typically overwrite the pointer that referred to it. If you don't save that node's address, you would have no way to delete it later on. 

A memory leak is when memory was allocated with new but never released with delete. Now, you have a piece of memory you can no no longer use/access/delete until the process is done.

Accessing a node after delete is unsafe because it is a dangling pointer. Reading or writing through it is undefined behavior and who knows what has happened to that memory since it was deleted. It could have been reused, overwritten, unmapped, etc.

## Part 10 Test the Complete Linked List

Here is my main method. This is also me experimenting with lambdas :P

```cpp
int main(void){
    auto show = [](const LinkedList& list, const string& label) {
        cout << "\n--- " << label << " ---\n";
        if (list.empty()) cout << "(empty)\n";
        else list.display();
    };
    auto found = [](bool b) { return b ? "found" : "not found"; };
    auto ok    = [](bool b) { return b ? "success" : "failed"; };


    LinkedList list;
    show(list, "1. Created list");


    cout << "\n2. empty(): " << (list.empty() ? "true" : "false") << '\n';


    list.insertFront(30);
    list.insertFront(20);
    list.insertFront(10);
    show(list, "3. After insertFront 30, 20, 10 (expect 10 20 30)");

   
    list.insertBack(40);
    list.insertBack(50);
    list.insertBack(60);
    show(list, "4. After insertBack 40, 50, 60 (expect 10 20 30 40 50 60)");


    show(list, "5. Display");
    cout << "empty(): " << (list.empty() ? "true" : "false") << '\n';


    cout << "\n6. Search\n";
    cout << "search(10): " << found(list.search(10)) << '\n';  // head
    cout << "search(40): " << found(list.search(40)) << '\n';  // middle
    cout << "search(60): " << found(list.search(60)) << '\n';  // tail
    cout << "search(99): " << found(list.search(99)) << '\n';  // missing


    cout << "\n7. insertAfter(30, 35): " << ok(list.insertAfter(30, 35)) << '\n';
    show(list, "After insertAfter (expect 10 20 30 35 40 50 60)");

    
    cout << "\n8. insertAfter(99, 77): " << ok(list.insertAfter(99, 77)) << '\n';
}
```

## Part 11 Memory Management

Here is my destructor:

```cpp
LinkedList::~LinkedList() {
    Node* temp = head;
    while (temp != nullptr) {
        Node* nextNode = temp->next;
        delete temp;
        temp = nextNode;
    }
}
```

That allocated memory is never released and could potentially lose access to it. That is a memory leak because now you have no way to release it to be allocated again. Memory management is especially important when implementing linked structures with raw pointers because it is incredibly easy to lose access to those addresses in memory if you do not properly store them.

## Part 12 Complexity Analysis

|Operation|Big-O Explanation|
|---|---|
|Access element at position i|This requires going through around n elements and is thus O(n).|
|Search|This also requires going through and checking element by element. This is thus O(n).|
|Insert at the beginning|This consists of accessing the first element O(1) and then inserting O(1). Thus, it is O(1).|
|Insert after a specified value|This will require going through the elements O(n) and then inserting O(1). Thus, it is O(n).|
|Delete first node|This requires getting to the first node O(1) and then releasing it back to memory O(1). Thus, it is O(1)n.
|Delete specified value|Similar to the pattern emerging, it needs O(n) to get there and then O(1) to delete it. O(n).
|Traverse entire list|Go thorugh n items -> O(n).

## Part 13 Linked List vs. Array

For accessing an element at index N/2, the array can just directly access it, whereas the linked list needs to go through ~N/2 operations. 

For inserting at the beginning, the linked list just needs to change pointers and set the new node as the head. The array needs to shift everything ~N items (assuming it isn't full) and then set the value.

For removing the first element, the linked list can just delete the head node and set the next as the new head. For the array, it can null the first element but depending on how the array is being used, it may be inconvenient to have that little gap at the front.

For searching for a particular value, assuming random assignment in the array, both need to sift through around n items leading to both being linear in complexity.

For memory layout, the array will be one contiguous block of memory that you can perform arithmetic to get to particular indices. The linked list will be blocks of dynamic memory that get allocated at runtime. 

The linked list pretty much only grows dynamically and are only limited by how much memory is in the heap. Arrays have a fixed size meaning that if you fill up your array and need more space, you need to pretty much start over and copy over (like an arraylist).

A linked list requires each element to hold both data and a pointer to the next element whereas each element in an array only needs to worry about holding the data.

### Analysis

Linked lists can perform some insertion and deletion operations efficiently like near the front (or the end if it holds a tail pointer). However, to get to a relatively intermediary element, it can't direct access like an array. It needs to walk through the elements to get there.

## Part 14 Reasoning About a Tail Pointer

1. Every "insert at the end" or "delete the end" becomes significantly faster because you can just point to the very end instead of having to walk the entire path. 

2. This it make direct access for the end elements -> O(1).

3. No, searching still requires sifting through all the elements.

4. No. Y

5. ou still need to set the n-1 node to point to nothing. Doing that in a singly linked list (meaning no way to go back) means going through n-1 elements. That is O(n).

## Part 15 Singly vs Doubly Linked Lists

prev points to where we came from in that linked list.

It can travel in both directions because now you know where the previous one is instead of just the next.

It requires holding another piece of information for each item, increasing memory usage.

It means that you don't need to record a temporary "previous" address. It is right there in the node you are operating on.

## Part 16 Circular Linked List

It would fail because now, no node points to a nullptr. It is like asking to find the end of a circle.

You could check if you return to the head node.

They are good for cyclic processing because it will just wrap back to the beginning. You don't need to check for nullptr and reset to head.

Round robin CP scheduling is a great example because each process gets a time slice and then moves onto the next.

## Analysis and Reflection

Linked lists do not require contiguous memory because going to the next element is just going to an address. That address could be anywhere in the heap, not necessarily right next-door.

The role of the head pointer is to get reference to the first node. Essentially, to "get the train started" so you can start traversing it.

Traversal is O(n) because you must move through n items to get to a later node.

Insertion at the beginning is O(1) because getting to that point is constant time O(1) and inserting, i.e. setting a new pointer as the head and setting the old head to the pointer, is constant time. Thus, O(1) + O(1) = O(1).

Insertion at the end is O(n) without a tail pointer requires moving through the entire thing ~n items and then inserting O(1)n. Thus, O(n).

Deletion requires careful pointer manipulation because it is very easy to lose reference to what you are deleting or setting as the next point. 

They must be eventually deallocated because we do not have infinite memory and must responsibly use the heap. That means cleaning up after our process and general deallocations.

A linked list cannot perform binary search efficiently like an indexed array because binary search requires a specific process to get to a "middle index". In a linked list, that middle index could be anywhere in the heap and it is not contiguous. 

A linked list may be preferable to an array or a vector for dynamic allocation at runtime. This flexibility is a great tool.

An array or vector may be preferable to a linked list if random access is a big necessity
