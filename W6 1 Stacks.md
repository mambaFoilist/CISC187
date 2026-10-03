# Week 6 Stacks

## Part 1 Trace Stack Operations

For the following, I decided to represent the stack as a list with the left most element being
the highest element in the stack. (Just so the table would not look very ugly).
| Operation | Value Returned | Stack After Operation | Top Element | Stack Size|
|---|---|---|---|---|
|push(10)| void | (10) | 10 | 1 |
|push(20)| void | (20, 10) | 20 | 2 |
|push(30)| void | (30, 20, 10) | 30 | 3 |
|pop()| 30 | (20, 10) | 20 | 2 |
|push(40)| void | (40, 20, 10) | 40 | 3 |
|push(50)| void | (50, 40, 20, 10) | 50 | 4 |
|pop()| 50 | (40, 20, 10) | 40 | 3 |
|push(60)| void | (60, 40, 20, 10) | 60 | 4 |

The final top element is 60. The final stack size is 4.
The remaining elements will be removed in the following order: 
1st: 60, 2nd: 40, 3rd: 20, 4th: 10.

This result demonstrates how the last placed in element is the first to be removed. 60 was added last and it was
removed first. For every possible set, the "newest" element will always be popped off first.

## Part 2 Implement an Array-Based Stack

```cpp
Stack::Stack() : topIndex(-1) {}

bool Stack::empty() const {
    return topIndex == -1;
}

bool Stack::full() const {
    return topIndex == CAPACITY - 1;
}

int Stack::size() const {
    return topIndex + 1;
}

void Stack::push(int value){
    if(full()) throw overflow_error("Stack overflow");
    topIndex++;
    data[topIndex] = value;
}

int Stack::pop(){
    if (empty()) throw underflow_error("Stack underflow");
    return data[topIndex--];
}

int Stack::top() const{
    if(empty()) throw underflow_error("Stack is empty");
    return data[topIndex];
}
```

## Part 3 Maintaining topIndex

Similar as to my above implementation:

```cpp
Stack::Stack() : topIndex(-1) {}

bool Stack::empty() const {
    return topIndex == -1;
}

bool Stack::full() const {
    return topIndex == CAPACITY - 1;
}

int Stack::size() const {
    return topIndex + 1;
}
```

topIndex should be initialized as -1 instead of 0 because given you had an empty array, initializing it to 0
would imply that there is something in the stack. Setting -1 as the floor allows you to discern when it is empty.

The first item is at index 0. Therefore, the topIndex is simply the size - 1. To get back the size you 
would return topIndex + 1.

Capacity - 1 indicates that the stack is full because it, again, the stack is zero-indexed, meaning that we are offset
by 1.

## Part 4 Implement push()

```cpp
void Stack::push(int value){
    if(full()) throw overflow_error("Stack overflow");
    topIndex++;
    data[topIndex] = value;
}
```

In my testing I implemented:

```cpp
int main(void){
    try{
        Stack s;
        
        for (int i = 0; i < 10; i++){
            s.push(i);
        }
        s.push(67);
        
        return 0;

    } catch(exception& e) {
        cout <<e.what();
    }
}
```

It properly handled the overflow condition and spit out the correct exception in my terminal! :D

Writing beyond data[CAPACITY - 1] would be wrong because it is essentially going outside of the designated memory
for the stack. You could be writing over important stuff without even knowing it.

## Part 5 Implement pop()

```cpp
int Stack::pop(){
    if (empty()) throw underflow_error("Stack underflow");
    return data[topIndex--];
}
```

I implemented this:

```cpp
try{
        Stack s; 
        std::cout << s.pop() << std::endl;
    } catch(exception& e){
        std::cout << e.what();
    }
```

My program correctly caught the exception and displayed the correct message.

Accessing data[topIndex] when topIndex = -1 is invalid because it is not in the scope of the array.
It is like opening the box that comes before the first box. It is not valid.

## Part 6 Implement top()

```cpp
int Stack::top() const{
    if(empty()) throw underflow_error("Stack is empty");
    return data[topIndex];
}
```

Top will return the value of the highest element and leave the stack the same. Pop will return the 
highest element and then "pop" it off of the stack.

## Part 7 Test the Complete Stack

```cpp
int main(void){
    //Part 4
    //testPush()
    //Part 5
    //testPop()

    Stack s;
    std::cout<< s.empty() << std::endl;

    for (int i = 0; i < 5; i++){
        s.push(i);
    }

    std::cout << s.size() << "\t" << s.top() << std::endl;

    s.pop();
    s.pop();

    std::cout << s.size() << "\t" << s.top() << std::endl;

    try{
        for (int i = 0; i < 10; i++){
            s.pop();
        }

        std::cout << "You are not supposed to be reading this lol :P";
    } catch (exception& e){
        std::cout << e.what() << std::endl;
    }

    std::cout << "Should be zero --> " << s.size() <<std::endl;

    try {
        for (int i = 0; i < 20; i ++){
            s.push(i);
        }
        std::cout<< "You shouldn't be reading this as well lol :P";
    } catch (exception& e){
        std::cout << e.what() << std::endl;
    }

    std::cout << "Should be 10 --> " << s.size() << std::endl;
    
}
```

## Part 8 Complexity Analysis

push() only looks at the first element and consists of constant time operations. Thus, it is O(1).

pop(), similarly, only looks at the first element and consists of constant time operations. Thus, it is also O(1).

top(), only looks at the highest element and consists of constant time operations. Thus, it is O(1).

empty() only needs to check one boolean based on some simple arithmetic so it is O(1).

full(), in the same sense, only checks one boolean based on a simple arithmetic and is also O(1).

size() just returns a stored value so it is also O(1).

Examining the 1 millionth element in a stack does not require looking at anything else except that element. 
It is why we only maintain topIndex. If we were to have a linked list, we would need to go through every
element to find the last/"highest" element. In a stack, the highest element is the first one we pull.

## Stack Correctness

It will return first 20, then 15, then 10, and then 5. 

The values will be returned in the following order: (xN), (xN-1), ... , (x2), (x1).

If your stack does not exhibit this behavior (LIFO), then it can be concluded that your stack is not implemented properly.

## Part 10 Balanced Delimiters

The implementation is as follows in part 11.

## Part 11 Implement the Balanced-Delimiter Algorithm

Here is the method + helpers for my algorithm:

```cpp
//helpers for balanced
bool isOpening(char c) {
    return c == '(' || c == '[' || c == '{';
}

bool isClosing(char c) {
    return c == ')' || c == ']' || c == '}';
}

bool matches(char open, char close) {
    return (open == '(' && close == ')') ||
           (open == '[' && close == ']') ||
           (open == '{' && close == '}');
}

//i chose to use str instead of expression js to avoid confusion because
//i am using vscode to write this
bool balanced(const string& str){
    Stack s;
    for (int i = 0; i < str.length(); i++){
        char c = str[i];
        if (isOpening(c)) {
            s.push(c);
        } else if (isClosing(c)){
            if (!s.empty() && matches(s.top(), c)){
                s.pop();
            } else {
                return false; // found a closing with no corresponding opener
            }
        }
        //anything else we don't care
    }

    return s.empty();
}
```

And this is what I used to test it:

```cpp
string tests[7] = {
        "{(a+b)*[c-d]}", "{(a+b]*c}", "((a+b))", "((a+b)",
        "[a+b]", "{[()]}", "{[(])}"
    };

    for (int i = 0; i < 7; i++) {
        cout << tests[i] << " -> "
             << (balanced(tests[i]) ? "Balanced" : "Not balanced") << endl;
    }
```
This is my result:

<img width="260" height="163" alt="image" src="https://github.com/user-attachments/assets/c839fccd-5b1e-46ff-9a85-a528c91fc6de" />

## Part 12 Analyze Delimiter Matching

Each character is examined once before the algorithm decides whether or not it should push, pop, return false and exit, or keep going.
Each delimiter is pushed or popped at most once because an opening/closing delimiter cannot count for more than one other delimiter, that being its respective compliment.
Because it goes through N many items once and performs constant time operations for each, it is O(n). 

The worse-case space complexity is that the string is all openers like "(((((((((". That would require the program to recreate N many items in the stack.

## Part 13 Stack Applications

Scenario A - Appropriate

Following the most recent operation is LIFO behavior.

Scenario B - Appropriate

Similarly, moving along the call chain means that you return the value of the last called function first. This 
is LIFO behavior. 

Scenario C - Appropriate

This is LIFO behavior as you must go back to the most recent webpage.

Scenario D - Appropriate

You must finish that last discovered path until returning back to the previous node. This is LIFO behavior.

Scenario E - Inappropriate

You must serve customers in the order they call so you can serve the person
who has been waiting the longest. This is FIFO behavior. Using a stack will get you some unhappy
customers.

## Analysis and Reflection

A stack is considered an Abstract Data Type because it is defined by its operations and behavior. It isn't as
simple as just contiguous blocks of memory allocated for elements.

Access is intentionally restricted to the top so you can pull data with LIFO behavior. 

Maintaining topIndex allows push(), pop(), and top() to be O(1) operations as it simply needs to reference 
an index instead of searching for a value.

Stack overflow is when you try to push more than what the stack can handle. Stack underflow is when you try to operate 
on an empty stack, whether that be popping or accessing.

Array-based stacks have a fixed capacity because arrays in C++ are fixed. Unless you use dynamic storage that can grow with your data needs, 
you need to abide by the capacity of the stack.

Stacks naturally support operations such as undo, recursion, backtracking, and delimiter matching because they require
a LIFO approach to walking through the problem. This is exactly what stacks give. 

Stacks cannot access the bottom element unless they pop everything on top. This is wildly inefficient and you will discard all the other elements.
This would require a FIFO approach instead.
