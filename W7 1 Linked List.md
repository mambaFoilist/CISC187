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

```
