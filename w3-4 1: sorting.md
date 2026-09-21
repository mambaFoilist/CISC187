# W3-4 1/3 Sorting

## Task 1 Linear Complexity

In Big-O notation, this is O(n) time. If the data were to grow very large, we only care about how the N term grows.
Similar to taking the limit of the quotient of two functions, it mainly matters on how the fastest-growing function grows (n! vs. n^2 vs. log n etc.)
The constants and slower functions, at greater and greater inputs are insignificant. Thus, the constant multiplier (4) and constant term (16) make a minimal difference.

## Task 2 Quadratic Complexity

This is O(n^2) time. If you were to increase N, the number of operations is proportional to N^2. So, for example,
tripling N would take nine times longer.

## Task 3 Analyzing Multiple Sequential Loops

The first loop executes N many times because it must go through every term (N many).

The second loop executes N many times because it must go through every term (N many).

The total work is around 2N (other steps are minimal).

The total time in Big-O notation would, therefore, be O(n).

They are both linear. Adding them together is still linear, just multiplied by a constant which does not change
the time complexity in Big-O notation.

## Task 4 Multiple Constant-Time Operations

This loop executes N many times.

For each execution of the loop, it executes three operations.

Because each operation is taking place in a sequential loop, each will fire N times, meaning that their
time complexity is multiplied by N. 

Because each of the three are constant, it is 3N which is still O(n). This makes sense 
because doing 3 things n times is the same as doing n things 3 times.

## Task 5 Analyzing Nested Iteration

The outer loop will execute N times.

The condition index.even? will be true approximately n/2 times.

Thus, the inner loop will execute n times every time it is reached.

This culminates in n operations being fired around n/2 times, leading to n^2 / 2 inner loop operations. 

This would be O(n^2) time.

It hardly matters if you process ever other element, or every third element, or every element, etc. This is simply a
multiplier being tagged onto the total amount of operations which ultimately doesn't matter in Big-O notation.

## Analysis and Reflection

Constants are normally ignored in Big-O notation because as n grows to infinity, constants are not very consequential.
(Imagine being told to run your house 3 times and then to run across the United States. The initial loops around the house are tiny in comparison.)

O(n) and O(n^2) are linear vs quadratic growth. If you double n, O(n) will double in total operations whereas
O(n^2) will quadruple in total operations. This makes O(n^2) much worse with larger and larger datasets. For N = 1 million,
the linear takes 1 million operations, the quadratic takes a trillion operations. 

Sequential loops and nested loops can result in different time complexities because sequential loops add while 
nested loops multiply. This results in roughly linear behavior versus non-linear behavior (n^2, n^3, etc.). These grow
much faster.

Understanding time complexity is crucial because being efficient with time (and also memory) is critical to making
efficient programs that work in a useful manner and cutting down on hardware requirements, electricity consumption, etc.
