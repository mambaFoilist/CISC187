# Week 6 Queues

## Part 1 Trace Queue Operations

| Operation | Value Returned | Logical Queue After Operation | Front | Size | 
|---|---|---|---|---|
|enqueue(10)| void | (10) | 10 | 1 |
|enqueue(20)| void | (10, 20) | 10 | 2 |
|enqueue(30)| void | (10, 20, 30) | 10 | 3 |
|dequeue() | 10 | (20, 30) | 20 | 2 |
|enqueue(40)| void | (20, 30, 40) | 20 | 3 |
|enqueue(50)| void | (20, 30, 40, 50) | 20 | 4 |
|dequeue() | 20 | (30, 40, 50) | 30 | 3 |
|enqueue(60)| void | (30, 40, 50, 60) | 30 | 4 |

The final front element is 30. The final queue size is 4. The
remaining elements would be removed in the following order: 30, 
40, 50, 60.
This is FIFO behavior because the "oldest" element is the first to be taken out.

## Part 2 Why Not Shift the Array?

If the queue contains N elements, you would need to move approximately N-1 elements during one dequeue().
This would be O(N) time complexity. Thus, removing all N elements would take N passes of an operation taking N-1 steps. Thus, that results in a O(n^2) time complexity. Advancing the front index is prefereable to physically moving every remaining element because that becomes a constant time operation (just a single increment).

## Part 3 Implement a Circular Queue

Here are all of my implementations.

```cpp
Queue::Queue() : frontIndex(0), rearIndex(0), count(0) {};

bool Queue::empty() const{
    return count == 0;
}

bool Queue::full() const{
    return count == CAPACITY;
}

int Queue::size() const{
    return count;
}

void Queue::enqueue(int value){
    if (full()) throw overflow_error ("Queue overflow");
    data[rearIndex] = value;
    rearIndex = (++rearIndex) % CAPACITY;
    count++;
}

int Queue::dequeue(){
    if (empty()) throw underflow_error ("Queue underflow");
    int temp = frontIndex;
    frontIndex = (++frontIndex) % CAPACITY;
    count--;
    return data[temp];
}

int Queue::front() const{
    if (empty()) throw underflow_error ("Queue underflow");
    return data[frontIndex];
}
```

## Part 4 Queue State and Invariants

```cpp
bool Queue::empty() const{
    return count == 0;
}

bool Queue::full() const{
    return count == CAPACITY;
}

int Queue::size() const{
    return count;
}
```
Count represents the amount of items in the queue. Thus, if count is 0 there is 0 items in the queue.
Similarly, if count is at CAPAPCITY, then the queue contains CAPACITY many items and is full.
frontIndex == rearIndex could mean that there is nothing in the queue and that the indices are at the same starting point or it could mean that the rearindex has wrapped around far enough to get back to the frontIndex, meaning the queue is full. Count removes this ambiguity by just storing the amount of items in its own, easy to interpret, int.

## Part 5 Implement enqueue()

```cpp
void Queue::enqueue(int value){
    if (full()) throw overflow_error ("Queue overflow");
    data[rearIndex] = value;
    rearIndex = (++rearIndex) % CAPACITY;
    count++;
}
```

I tested my program with the following:

```cpp
Queue q;

for (int i = 1; i <= 10; ++i)
    q.enqueue(i);
cout << "Queue filled. Size: " << q.size() << endl;

try {
    q.enqueue(11);
    cout << "ERROR: overflow not detected" << endl;
} catch (const overflow_error& e) {
    cout << "Caught: " << e.what() << endl;
}
```

++rearIndex is not sufficient because it needs to wrap around once it gets to the end. 

## Part 6 Implement dequeue()

```cpp
int Queue::dequeue(){
    if (empty()) throw underflow_error ("Queue underflow");
    int temp = frontIndex;
    frontIndex = (++frontIndex) % CAPACITY;
    count--;
    return data[temp];
}
```

To test I used this:

```cpp
Queue q;

q.enqueue(10);
q.enqueue(20);
q.enqueue(30);
cout << "Enqueued 3. Size: " << q.size() << endl;

while (!q.empty())
    cout << "Dequeued: " << q.dequeue() << endl;
cout << "Queue empty. Size: " << q.size() << endl;

try {
    q.dequeue();
    cout << "ERROR: underflow not detected" << endl;
} catch (const underflow_error& e) {
    cout << "Caught: " << e.what() << endl;
}
```

dequeue() should advance frontIndex instead of shifting all of the remaining elements because it is functionally the same thing while only requiring to change one int instead of moving N elements. 

## Part 7 Implement front()

```cpp
int Queue::front() const{
    if (empty()) throw underflow_error ("Queue is empty");
    return data[frontIndex];
}
```

Similar to the difference between top() and pop() in stacks, front() will return the next to be removed element whereas dequeue() will return that same element and then effectively "remove" it. which is really just increasing the frontIndex and treating it now as garbarge data.

## Part 8 Demonstrate Circular Wraparound


For this, I made a helper method (I included a declaration in the class) and tested the circular wraparound with the following code:

```cpp
//helper for documenting state in part 8
void Queue::printState(const string& op) const {
    cout << op << "\tfront=" << frontIndex
         << "  rear=" << rearIndex
         << "  count=" << count << "  queue: ";
    for (int i = 0; i < count; ++i)
        cout << data[(frontIndex + i) % CAPACITY] << ' ';
    cout << '\n';
}

int main() {

    Queue q; 

    q.printState("Initial");

    q.enqueue(10); q.printState("enqueue(10)");
    q.enqueue(20); q.printState("enqueue(20)");
    q.enqueue(30); q.printState("enqueue(30)");
    q.enqueue(40); q.printState("enqueue(40)");
    q.enqueue(50); q.printState("enqueue(50)");

    q.dequeue();   q.printState("dequeue()");
    q.dequeue();   q.printState("dequeue()");

    q.enqueue(60); q.printState("enqueue(60)");
    
    return 0;
}
```


Here is what I got in my terminal: 

<img width="691" height="202" alt="image" src="https://github.com/user-attachments/assets/f3b8aeab-f0c9-4e2e-b188-1c7fd41ca441" />


The wraparound occurred at q.enqueue(60). It reached the final index place and thus has to wraparound to place in 60. 

By modular arithmetic, CAPACITY % CAPACITY = 0. Thus, it will wraparound to the very first spot, that being index 0. Physical array order can differ because all that matters is that it exists in order in the logical queue order, that is, that the ring is not invalidly manipulated.

Modular arithmetic makes this possible by being circular in nature, it will keep wrapping larger and larger values to the same sequence of numbers. 

## Part 9 Logical Position vs. Physical Position

The physical positions for i = 0, 1, 2, and 3 are 6, 7, 0, and 1, respectively. 

This relationship, using modular arithmetic simply wraps the index around once it exceeds the capacity. That way you make one "contiguous" block of memory even though it is not actually all next to each other in memory.

## Part 10 Test the Complete Circular Queue

Here is my main method: 

```cpp
Queue q;

cout << "=== 1. Initially empty queue ===\n";
q.printState("Initial");
cout << "empty(): " << boolalpha << q.empty() << ", size(): " << q.size() << "\n\n";

cout << "=== 2. Multiple enqueues ===\n";
q.enqueue(10); q.printState("enqueue(10)");
q.enqueue(20); q.printState("enqueue(20)");
q.enqueue(30); q.printState("enqueue(30)");

cout << "\n=== 4-5. front() and size() ===\n";
cout << "front(): " << q.front() << "\n";
cout << "size():  " << q.size() << "\n";

cout << "\n=== 3, 6. FIFO order / multiple dequeues ===\n";
cout << "dequeue() -> " << q.dequeue() << "\n"; q.printState("after dequeue");
cout << "dequeue() -> " << q.dequeue() << "\n"; q.printState("after dequeue");

cout << "\n=== 7-8. Wraparound and reuse of vacated slots ===\n";
q.enqueue(40); q.printState("enqueue(40)");
q.enqueue(50); q.printState("enqueue(50)");   // rear wraps 4 -> 0
q.enqueue(60); q.printState("enqueue(60)");   // reuses index 0
q.enqueue(70); q.printState("enqueue(70)");   // reuses index 1
cout << "full(): " << q.full() << ", front(): " << q.front() << "\n";

cout << "\n=== 10. Overflow ===\n";
try {
    q.enqueue(80);
    cout << "ERROR: overflow not detected\n";
} catch (const overflow_error& e) {
    cout << "enqueue(80) -> Caught: " << e.what() << "\n";
}
q.printState("after overflow");

cout << "\n=== Drain queue (FIFO, front index also wraps) ===\n";
while (!q.empty()) {
    int v = q.dequeue();
    q.printState("dequeue() -> " + to_string(v));
}

cout << "\n=== 9. Underflow ===\n";
try {
    q.dequeue();
    cout << "ERROR: underflow not detected\n";
} catch (const underflow_error& e) {
    cout << "dequeue() -> Caught: " << e.what() << "\n";
}
try {
    q.front();
    cout << "ERROR: underflow not detected\n";
} catch (const underflow_error& e) {
    cout << "front()   -> Caught: " << e.what() << "\n";
}
```

This is what I got in my terminal: 

<img width="662" height="933" alt="image" src="https://github.com/user-attachments/assets/02c2de45-b6b7-4bf0-b36c-09b47e9e2d7f" />

## Part 11 Complexity Analysis

enqueue() only involves initiating a value to memory and incrementing values. Thus, it is constant or O(1) time.

dequeue(), simiilarly, only involves incrementing some values so it is O(1).

front() involves only returning the value so it is O(1).

empty() checks a single boolean by integer arithmetic so it is O(1).

full(), same deal, only deals with integer arithmetic to check a boolean so it is O(1).

size() returns a single int so it is O(1).

Implementation A will require operating on N elements to shift everything. Thus, doing this n times in order to remove all n elements would be O(n^2).

Implementation B will just incrementing a value so it is O(1). Doing this n many times would be O(n).

## Part 12 FIFO Correctness

It would return 5, 10, 15, 20, in that order.

It would return x1, x2, ... , xN, in that order. 

If this property is not demonstrated by the implementation, that it can be concluded that it is an incorrect implementation as it does not exhibit proper FIFO behavior. 

## Part 13 Queue Applications

Scenario A - Appropriate

Yes. This is a FIFO problem where each request should be done in the order that they come in.

Scenario B - Appropriate

Yes. This is also FIFO as you don't want a user waiting too long. 

Scenario C - Inappropriate 

This is a LIFO problem. undo's should refer to the last performed operation not the earliest.

Scenario D - Appropriate

This is another case of FIFO behavior. The queue is excellent for this.

Scenario E - Inappropriate

This, similar to undo, is LIFO behavior and you should not use a queue.

## Part 14 - Queue and Breadth-First-Search

A>B>C>D>E>F

Queue:
A

B,C

C,D,E

D,E,F

E,F

F

Done!

FIFO will tackle the "oldest" node, causing it to eliminate possibilities level by level.

In Breadth-First-Search, it will make sure each vertex is enqueued and dequeued at most once. This results in O(V). And when a vertex is dequeued, BFS scans its adjacency list, checking each neighbor once. Over the whole rune, this will take around E operations. This results in O(E). Add them together and get O(V + E).

In the worst space cast, each vertex enters the queue at most once, so the queue can never hold more than V vertices. Thus, it is O(V).

## Analysis and Reflection

A queue is defined by its operations, not by how it's stored. My tests only called those methods, so they'd pass unchanged with a linked list inside. 

FIFO is first-in-first-out while LIFO is last-in-first-out. If I input 10, 20, 30, and 40. LIFO would take out 40, 30, 20, and then 10 while FIFO would take out 10, 20, 30 and then 40.

Circular indexing performs the same function as shifting every element but it is constant time instead of linear time. 

frontIndex, rearIndex, and count all work together to manage where to place, take out, and track the amount of elements. frontIndex tracks where to take elements out, rearIndex tracks where to place the next element, and count tracks the amount of elements.

Modular arithmetic is precisely the function needed to wrap around any int to the desired range (between 0 and CAPACITY - 1).

frontIndex == rearIndex could mean that the queue is either full or empty. Count helps discern from this ambiguity with a single int.

Enqueue and dequeue, when properly implemented, only involve either initializing memory and incrementing an int or incremeneting a value and returning an element, respectively. Thus, both are O(1).

Queues are appropriate for work that needs to be prioritized by arrival order because they process elements in the order they come. 

FIFO ordering supports BFS because it will move from the "oldest" node meaning that it will follow the necessary pattern.
