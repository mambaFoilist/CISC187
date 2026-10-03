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
|dequeue() | 20 | (30, 40, 50) | 3 |
|enqueue(60)| void | (30, 40, 50, 60) | 4 |

The final front element
