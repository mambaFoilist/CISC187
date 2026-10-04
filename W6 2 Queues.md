# Week 6 Queues

## Part 1 Trace Queue Operations

| Operation | Value Returned | Logical Queue After Operation | Front Size | 
|---|---|---|---|
|enqueue(10)| void | (10) | 0 |
|enqueue(20)| void | (10, 20) | 0 |
|enqueue(30)| void | (10, 20, 30) | 0 |
|dequeue() | 10 | (20, 30) | 1 |
|enqueue(40)| void | (20, 30, 40) | 1 |
|enqueue(50)| void | (20, 30, 40, 50) | 1 |
|dequeue() | 20 | (30, 40, 50) | 2 |
|enqueue(60)| void | (30, 40, 50, 60) | 2 |

The final front element is 30. The final queue size is 4. The
remaining elements would be removed in the following order: 30, 
40, 50, 60.
This is FIFO behavior because the "oldest" element is the first to be taken out.

## Part 2 Why Not Shift the Array?

If the queue contains N elements, you would need to move approximately N-1 elements during one dequeue().
This would be O(N) time complexity. Thus, removing all N elements would take N passes of an operation taking N-1 steps. Thus, that results in a O(n^2) time complexity. Advancing the front index is prefereable to physically moving every remaining element because that becomes a constant time operation (just a single increment).

## Part 3 Implement a Circular Queue

