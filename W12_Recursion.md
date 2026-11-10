# Week 12 Recursion
 
## Part 1 Recognizing Recursion
 
1. This is recursive because it calls itself.
2. It does not contain a base case.
3. It will proceed in an infinite recursion of just calling itself, since nothing ever tells it to stop.
4. It just keeps growing, since every call adds a new frame to the stack that never gets removed, until the stack runs out of memory and the program crashes (stack overflow).
## Part 2 From Loop to Recursion
 
A base case is the root case where your recursion may tend toward in order to terminate. For countdown, the base case is n == 0, where it prints 0 and returns.
 
A recursive case is where you may execute something before you continue with the recursion. For countdown, it prints n and then calls countdown(n - 1).
 
It will often pass arguments that, through each execution of the method, will be altered in a way that it tends toward the base case. For countdown, n goes down by 1 every call, so it eventually hits 0.
 
```cpp
void countdown(int n){
    std::cout << n << std::endl;
 
    if(n == 0){
        return;
    }
 
    countdown(n - 1);
}
 
void loopCountdown(int n){
    for(int i = n; i >= 0; i--){
        std::cout << i << std::endl;
    }
    return;
}
```
 
## Part 3 Trace the Countdown
 
|Step|Function Call|Value Printed|Next Action|
|---|---|---|---|
|1|countdown(3)|3|countdown(2)|
|2|countdown(2)|2|countdown(1)|
|3|countdown(1)|1|countdown(0)|
|4|countdown(0)|0|return|
 
It will terminate at 0, thus the argument passed in the function will never be less than zero (i.e. negative).
 
## Part 4 Reading Recursive Code Factorial
 
1. The base case is n == 1, where it returns 1.
2. The recursive case is return n * factorial(n - 1).
3. Each call passes in a value that is 1 smaller than before, so n eventually becomes 1. This only works if n starts at 1 or higher, since something like factorial(0) would skip right past the base case.
4. factorial(1) returns 1.
5. factorial(2) = 2 * factorial(1) = 2 * 1 = 2.
6. factorial(3) = 3 * factorial(2) = 3 * 2 = 6.
7. factorial(4) = 4 * factorial(3) = 4 * 6 = 24.
## Part 5 Expanding Recursive Calls
 
```
factorial(4)
= 4 * factorial(3)
= 4 * 3 * factorial(2)
= 4 * 3 * 2 * factorial(1)
= 4 * 3 * 2 * 1
= 24
```
 
The recursive calling phase stops once factorial(1) is reached, since the base case returns 1 without making another call.
 
## Part 6 The Call Stack
 
|Call Order|Function|
|---|---|
|1|factorial(4)|
|2|factorial(3)|
|3|factorial(2)|
|4|factorial(1)|
 
|Completion Order|Function|
|---|---|
|1|factorial(1)|
|2|factorial(2)|
|3|factorial(3)|
|4|factorial(4)|
 
The completion order is the reverse of the call order because each call is waiting on the result of the call it made. factorial(4) cannot finish until factorial(3) gives it a value, and so on, so the last function pushed onto the stack is the first one to finish and get popped off (LIFO).
 
## Part 7 Passing Values Up the Call Stack
 
|Function|Expression|Returned Value|
|---|---|---|
|factorial(1)|base case|1|
|factorial(2)|2 * factorial(1) = 2 * 1|2|
|factorial(3)|3 * factorial(2) = 3 * 2|6|
|factorial(4)|4 * factorial(3) = 4 * 6|24|
 
When a call finishes, it hands its return value back to the call below it on the stack, which was paused waiting for it. That call then uses the value to finish its own expression and returns its result to the call below it, until the original call finishes with the final answer.
 
## Part 8 Debugging Recursive Functions
 
### Example A
 
1. Yes, it contains a base case, n == 0.
2. No, the base case will never be reached since n starts at 5 and only gets bigger.
3. The recursive step uses n + 1, which moves away from 0 instead of toward it.
4. Change mystery(n + 1) to mystery(n - 1). To be safe for negative inputs, the base case could also be changed to n <= 0.
### Example B
 
There is no base case, so nothing ever stops the calls. n just keeps going down past 0 into the negatives, and every call adds another frame to the call stack. Since the stack has a limited amount of memory, it eventually fills up and the program crashes with a stack overflow.
 
## Part 9 Write a Recursive Function
 
The base case is n == 1, where it returns 1.
 
The recursive case is return n + sumTo(n - 1).
 
Each call passes n - 1, so n goes down by 1 every call until it reaches 1. This assumes n is positive.
 
```cpp
int sumTo(int n){
    if(n == 1){
        return 1;
    }
 
    return n + sumTo(n - 1);
}
```
 
|Call Order|Function Call|Next Action|
|---|---|---|
|1|sumTo(4)|4 + sumTo(3)|
|2|sumTo(3)|3 + sumTo(2)|
|3|sumTo(2)|2 + sumTo(1)|
|4|sumTo(1)|return 1|
 
|Function|Expression|Returned Value|
|---|---|---|
|sumTo(1)|base case|1|
|sumTo(2)|2 + sumTo(1) = 2 + 1|3|
|sumTo(3)|3 + sumTo(2) = 3 + 3|6|
|sumTo(4)|4 + sumTo(3) = 4 + 6|10|
 
## Part 10 Recursion vs. Iteration
 
|Question|Response|
|---|---|
|Which version is easier for you to read?|The loop version, since everything happens in one place and there is no need to trace calls.|
|Which version uses the call stack?|The recursive version, every call adds a new frame to the stack.|
|Is recursion automatically more efficient than iteration?|No, each recursive call has extra overhead from creating a stack frame, so it is usually the same speed or slower and uses more memory.|
|Why might a loop still be preferable for a simple countdown?|A loop uses a constant amount of memory and cannot overflow the stack, even for a really large n.|
 
## Part 11 Recursive Structure Filesystem Traversal
 
1. Recursion is a natural approach because a directory is made up of smaller directories, which are made up of even smaller directories. Visiting a subdirectory is the same problem as visiting the original directory, just smaller.
2. The base case is a directory with no subdirectories, where there is nothing left to go into, so the function just returns.
3. The recursive case would process the current directory, then call the same function on each of its subdirectories.
4. It is difficult to know in advance because the nesting depends entirely on how the files were organized, and any directory can contain more directories. A fixed number of nested loops would only work for a fixed depth, while recursion just keeps going as deep as it needs to.
```cpp
struct Directory{
    std::string name;
    std::vector<Directory> children;
};
 
void traverse(const Directory& dir, int depth){
    std::cout << std::string(depth * 4, ' ') << dir.name << "/" << std::endl;
 
    
    for(const Directory& child : dir.children){
        traverse(child, depth + 1);
    }
}
```
 
## Analysis and Reflection
 
1. Every useful recursive function needs a stopping condition, or else it will call itself forever until the stack overflows and it never actually returns an answer.
2. Having a base case is not enough if the recursive step never reaches it, like in mystery where n + 1 moves away from 0. The base case only works if each call actually moves the argument closer to it.
3. During the recursive calling phase, the function keeps calling itself with smaller arguments, and each call gets pushed onto the stack and paused while it waits for the next call to return.
4. During the unwinding phase, the base case returns first, then each paused call takes the returned value, finishes its work, and gets popped off the stack, until the original call returns.
5. The call stack behaves as a LIFO structure because the most recent call is always the one that has to finish first, since every earlier call is waiting on it.
6. Infinite recursion causes stack overflow because each call takes up memory on the stack, and if the calls never stop, the stack eventually runs out of space.
7. Recursion is not automatically preferable to iteration because it uses more memory, has extra overhead per call, and can overflow the stack. For simple problems like a countdown, a loop does the same thing with less cost.
8. Recursion is useful for problems with an unknown number of nested levels, like directories, because the function just calls itself again for each level and does not need to know the depth ahead of time.
