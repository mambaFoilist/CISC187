# Week 8 Assignment/Project 1 Binary Search Trees

## Part 1 Construct a BST

Tree A: It is a valid BST because, for every key, the subtree to the right of it is larger than that value
and vice versa for the subtree to the left.

Tree B: It is an invalid BST because the right subtree of 30 is larger than 30 but also larger than the parent of 
the 30 node.

Tree C: It is a valid BST. The values follow the pattern

It is insufficient to only check a node and its immediate children for validating because those children may have children that validate the 
ordering pattern or the node itself could be violating the pattern as the child of another node. Those children of a node could
also be larger or smaller than the parent of a parent when they are not allowed to be.

## Part 2 Construct a BST

<img width="507" height="370" alt="image" src="https://github.com/user-attachments/assets/8078579b-74d1-4734-a1e6-b31083613015" />

Root	45

Leaf nodes	15, 30, 40, 60, 80

Parent of 30	35

Children of 25	15 (left), 35 (right)

Left subtree of 45	{25, 15, 35, 30, 40}, rooted at 25

Right subtree of 45	{70, 60, 90, 80}, rooted at 70

Depth of 40	3 (45 → 25 → 35 → 40)

Depth of 80	3 (45 → 70 → 90 → 80)

Height of tree	3

## Part 3 Trace BST Search

Search 40

45: 40 < 45, go left

25: 40 > 25, go right

35: 40 > 35, go right

40: 40 = 40, found


Search 75

45: 75 > 45, go right

70: 75 > 70, go right

90: 75 < 90, go left

80: 75 < 80, go left

null: not found

Failure point: at 80 the search goes left, but 80 has no left child. Reaching nullptr means 75 isn’t in the tree.

### Analysis

Every value in the left subtree is smaller than the node, and every value in the right subtree is larger. So one comparison tells you which side the target must be on, and the other subtree can be skipped entirely. Search follows a single path, making it O(h).

## Part 4 Implementing a BST Class

Here is my entire implementation:

```cpp
#include <iostream>

using namespace std;

struct Node {
    int data;
    Node* left;
    Node* right;

    Node(int value) {
        data = value;
        left = nullptr;
        right = nullptr;
    }
};

class BinarySearchTree {
private:
    Node* root;

    Node* insert(Node* node, int value);
    bool search(Node* node, int value) const;

    void inorder(Node* node) const;
    void preorder(Node* node) const;
    void postorder(Node* node) const;

    Node* findMin(Node* node) const;
    Node* findMax(Node* node) const;

    Node* remove(Node* node, int value);
    void deleteTree(Node* node);

public:
    BinarySearchTree();
    ~BinarySearchTree();

    bool empty() const;

    void insert(int value);
    bool search(int value) const;
    bool remove(int value);

    void inorder() const;
    void preorder() const;
    void postorder() const;

    int minimum() const;
    int maximum() const;
};



BinarySearchTree::BinarySearchTree() : root(nullptr) {}

BinarySearchTree::~BinarySearchTree() {
    deleteTree(root);
}

bool BinarySearchTree::empty() const {
    return root == nullptr;
}



Node* BinarySearchTree::insert(Node* node, int value) {
    if (node == nullptr)
        return new Node(value);

    if (value < node->data)
        node->left = insert(node->left, value);
    else if (value > node->data)
        node->right = insert(node->right, value);
    //duplicates are ignored

    return node;
}

void BinarySearchTree::insert(int value) {
    root = insert(root, value);
}



bool BinarySearchTree::search(Node* node, int value) const {
    if (node == nullptr)
        return false;
    if (value == node->data)
        return true;
    if (value < node->data)
        return search(node->left, value);
    return search(node->right, value);
}

bool BinarySearchTree::search(int value) const {
    return search(root, value);
}


void BinarySearchTree::inorder(Node* node) const {
    if (node == nullptr) return;
    inorder(node->left);
    cout << node->data << " ";
    inorder(node->right);
}

void BinarySearchTree::preorder(Node* node) const {
    if (node == nullptr) return;
    cout << node->data << " ";
    preorder(node->left);
    preorder(node->right);
}

void BinarySearchTree::postorder(Node* node) const {
    if (node == nullptr) return;
    postorder(node->left);
    postorder(node->right);
    cout << node->data << " ";
}

void BinarySearchTree::inorder() const {
    inorder(root);
    cout << endl;
}

void BinarySearchTree::preorder() const {
    preorder(root);
    cout << endl;
}

void BinarySearchTree::postorder() const {
    postorder(root);
    cout << endl;
}



Node* BinarySearchTree::findMin(Node* node) const {
    if (node == nullptr) return nullptr;
    while (node->left != nullptr)
        node = node->left;
    return node;
}

Node* BinarySearchTree::findMax(Node* node) const {
    if (node == nullptr) return nullptr;
    while (node->right != nullptr)
        node = node->right;
    return node;
}

int BinarySearchTree::minimum() const {
    if (empty())
        throw runtime_error("minimum() called on empty tree");
    return findMin(root)->data;
}

int BinarySearchTree::maximum() const {
    if (empty())
        throw runtime_error("maximum() called on empty tree");
    return findMax(root)->data;
}


Node* BinarySearchTree::remove(Node* node, int value) {
    if (node == nullptr)
        return nullptr;

    if (value < node->data) {
        node->left = remove(node->left, value);
    } else if (value > node->data) {
        node->right = remove(node->right, value);
    } else {
        
        if (node->left == nullptr) {
            Node* temp = node->right;
            delete node;
            return temp;
        }
        if (node->right == nullptr) {
            Node* temp = node->left;
            delete node;
            return temp;
        }

        
        Node* successor = findMin(node->right);
        node->data = successor->data;
        node->right = remove(node->right, successor->data);
    }

    return node;
}

bool BinarySearchTree::remove(int value) {
    if (!search(root, value))
        return false;
    root = remove(root, value);
    return true;
}


void BinarySearchTree::deleteTree(Node* node) {
    if (node == nullptr) return;
    deleteTree(node->left);
    deleteTree(node->right);
    delete node;
}
```

## Part 5 BST Insertion

```cpp
Node* BinarySearchTree::insert(Node* node, int value) {
    if (node == nullptr)
        return new Node(value);

    if (value < node->data)
        node->left = insert(node->left, value);
    else if (value > node->data)
        node->right = insert(node->right, value);
    //duplicates are ignored

    return node;
}

void BinarySearchTree::insert(int value) {
    root = insert(root, value);
}
```

### Analysis

Each comparison sends the value either left or right based on the BST property. The new value can only belong on one side, so the other subtree is never visited.

It returns the root of the updated subtree so the parent can reattach it with node->left = insert(...). That’s how the new node gets linked to its parent. 
For every other node it returns the same pointer, so nothing changes.

That empty spot is where the value belongs. A new node is created there and returned, and its parent links to it.

Neither < nor > is true, so nothing happens. The node is returned unchanged and the duplicate is ignored.

## Part 6 Searching

```cpp
bool BinarySearchTree::search(Node* node, int value) const {
    if (node == nullptr)
        return false;
    if (value == node->data)
        return true;
    if (value < node->data)
        return search(node->left, value);
    return search(node->right, value);
}

bool BinarySearchTree::search(int value) const {
    return search(root, value);
}
```

Search follows a single root-to-leaf path and does one comparison per level, so the most work it can do is about H + 1 comparisons. 
The cost depends on the tree’s height, not the number of nodes, which is why O(h) is the most accurate way to express it.

In a reasonably balanced tree, each level roughly doubles the number of nodes, so N nodes fit in about log2(N) levels. That makes H ≈ log N, and search is O(log N).

In a highly skewed tree, like one built by inserting already sorted values, every node has only one child and the tree becomes a linked list. Then H = N - 1, and search is O(N).

## Part 7 Traversal

```cpp
void BinarySearchTree::inorder(Node* node) const {
    if (node == nullptr) return;
    inorder(node->left);
    cout << node->data << " ";
    inorder(node->right);
}

void BinarySearchTree::preorder(Node* node) const {
    if (node == nullptr) return;
    cout << node->data << " ";
    preorder(node->left);
    preorder(node->right);
}

void BinarySearchTree::postorder(Node* node) const {
    if (node == nullptr) return;
    postorder(node->left);
    postorder(node->right);
    cout << node->data << " ";
}

void BinarySearchTree::inorder() const {
    inorder(root);
    cout << endl;
}

void BinarySearchTree::preorder() const {
    preorder(root);
    cout << endl;
}

void BinarySearchTree::postorder() const {
    postorder(root);
    cout << endl;
}
```

| Traversal | Predicted Output | Actual Output |
|---|---|---|
| Inorder | 20 30 40 50 60 70 80 | 20 30 40 50 60 70 80 |
| Preorder | 50 30 20 40 70 60 80 | 50 30 20 40 70 60 80 |
| Postorder | 20 40 30 60 80 70 50 | 20 40 30 60 80 70 50 |

Inorder visits the left subtree, then the node, then the right subtree. In a BST, everything on the left is smaller than the node and everything on the right is larger, 
so each node prints after all smaller values and before all larger ones. Since this holds recursively at every node, the whole tree comes out in sorted order. Recursion is beautiful :)

## Part 8 Minimum and Maximum

```cpp
Node* BinarySearchTree::findMin(Node* node) const {
    if (node == nullptr) return nullptr;
    while (node->left != nullptr)
        node = node->left;
    return node;
}

Node* BinarySearchTree::findMax(Node* node) const {
    if (node == nullptr) return nullptr;
    while (node->right != nullptr)
        node = node->right;
    return node;
}

int BinarySearchTree::minimum() const {
    if (empty())
        throw runtime_error("minimum() called on empty tree");
    return findMin(root)->data;
}

int BinarySearchTree::maximum() const {
    if (empty())
        throw runtime_error("maximum() called on empty tree");
    return findMax(root)->data;
}
```

Every left child is smaller than its parent, so each step left reaches a smaller value. The leftmost node has no left child, which means nothing in the tree is smaller than it, so it’s the minimum.

Every right child is larger than its parent, so each step right reaches a larger value. The rightmost node has no right child, which means nothing in the tree is larger than it, so it’s the maximum.

Throw an exception or report an error. An empty tree has no minimum or maximum, and returning a placeholder like -1 would be ambiguous, since -1 could be a real value in the tree.

They follow a single path down one side of the tree, taking at most one step per level. The number of steps is bounded by the height, so the cost is O(H).

## Part 9 BST Deletion

```cpp
Node* BinarySearchTree::remove(Node* node, int value) {
    if (node == nullptr)
        return nullptr;

    if (value < node->data) {
        node->left = remove(node->left, value);
    } else if (value > node->data) {
        node->right = remove(node->right, value);
    } else {
        
        if (node->left == nullptr) {
            Node* temp = node->right;
            delete node;
            return temp;
        }
        if (node->right == nullptr) {
            Node* temp = node->left;
            delete node;
            return temp;
        }

        
        Node* successor = findMin(node->right);
        node->data = successor->data;
        node->right = remove(node->right, successor->data);
    }

    return node;
}

bool BinarySearchTree::remove(int value) {
    if (!search(root, value))
        return false;
    root = remove(root, value);
    return true;
}


void BinarySearchTree::deleteTree(Node* node) {
    if (node == nullptr) return;
    deleteTree(node->left);
    deleteTree(node->right);
    delete node;
}
```

Case 1:

<img width="1167" height="788" alt="image" src="https://github.com/user-attachments/assets/0b6c6e2f-22de-4a38-a92b-60f678d39bd9" />

Case 2:

<img width="1150" height="778" alt="image" src="https://github.com/user-attachments/assets/65e28cf2-79ed-4378-bc2b-7de719165d5d" />

Case 3:

<img width="1593" height="858" alt="image" src="https://github.com/user-attachments/assets/62376062-a11e-4590-920e-976fa1054521" />

A leaf has no children, so deleting it just means setting the parent’s pointer to null. A node with two children has two subtrees that both need a place to go, 
but its parent only has one pointer to give. So you have to find a replacement value that keeps the BST property, which is the inorder successor. Then you copy it 
in and run a second deletion to remove the successor from its original spot. That means an extra search down the right subtree plus another delete.

If you delete a node without reconnecting its subtree, the parent’s pointer either still points to freed memory, which is a dangling pointer and undefined behavior, 
or gets set to null. Setting it to null cuts off every node below the deleted one. Those nodes become unreachable, so searches can’t find them and their memory leaks.

## Part 10 Verify Deletion

Starting Tree

<img width="860" height="726" alt="image" src="https://github.com/user-attachments/assets/78b03f07-628c-45c9-8cde-23bee89252bf" />

<img width="805" height="632" alt="image" src="https://github.com/user-attachments/assets/b4ffb22e-a6b5-4366-bc79-21c0571513a8" />

<img width="751" height="631" alt="image" src="https://github.com/user-attachments/assets/37d70bb2-9d78-4a5d-a302-759ef577922c" />

<img width="865" height="687" alt="image" src="https://github.com/user-attachments/assets/cd2be739-6996-4882-8ce1-8ecc7f1d0303" />

Proof from my terminal:

<img width="857" height="633" alt="image" src="https://github.com/user-attachments/assets/b68ab9f6-3e7d-4996-8298-be7541c6b599" />

### Analysis

Inorder traversal of a valid BST is always sorted. If a deletion reattaches a subtree in the wrong place, the ordering breaks and the output stops being sorted, so the mistake shows up right away. The traversal also lists every value still in the tree. Comparing it to the expected list shows that the deleted value is gone and nothing else was lost.

No, sorted output alone isn’t enough. It proves the tree is still a valid BST with the right contents, but it doesn’t check everything. It can’t detect memory problems like leaks, dangling pointers, or double frees, which can make the output look correct by luck. It doesn’t confirm the tree has the expected shape, since a different valid replacement, like the predecessor instead of the successor, also gives sorted output. And it doesn’t test the other code paths, like search, min, max, or remove’s return value, which need their own tests.

## Part 11 Skewed Tress

Tree A:

<img width="1047" height="645" alt="image" src="https://github.com/user-attachments/assets/bf1557f2-c960-4003-b58c-5a3e768c45d2" />

Tree B (Basically a linked-list):

<img width="921" height="928" alt="image" src="https://github.com/user-attachments/assets/3f578cfb-bcd7-4c49-9091-a9201b3d445c" />

### Analysis

A BST doesn’t rebalance itself, so its shape is set entirely by insertion order. Each new value is placed by comparing it against the nodes already in the tree, 
so the first value becomes the root and early values decide how the tree branches.

If values arrive in a mixed order, like 40, 20, 60, 10, 30, 50, 70, they spread out on both sides and the tree stays balanced, with height around log N. If the same 
values arrive sorted, like 10, 20, 30, 40, 50, 60, 70, each one is larger than everything before it, so it always goes right. The tree becomes a straight chain with height N - 1.

Since search, insert, and delete are all O(H), the same data can give O(log N) or O(N) performance depending only on the order it was inserted.

## Part 12 Complexity Analysis

| Operation | Balanced BST | Highly Skewed BST | Why? |
|---|---|---|---|
| Search | O(log N) | O(N) | Follows one root-to-leaf path, so cost is O(H) |
| Insert | O(log N) | O(N) | Searches down one path to the empty spot where the value belongs, O(H) |
| Delete | O(log N) | O(N) | Finds the node, plus possibly the inorder successor, both along one path, O(H) |
| Minimum | O(log N) | O(N) | Follows left pointers down one side, O(H) |
| Maximum | O(log N) | O(N) | Follows right pointers down one side, O(H) |
| Full inorder traversal | O(N) | O(N) | Visits every node exactly once regardless of shape |

Saying “BST search is O(log N)” assumes the tree is balanced, but a plain BST doesn’t guarantee that. Search actually costs O(H), where H is the height of the tree. 
H is about log N only when the tree is balanced. If values are inserted in sorted or nearly sorted order, the tree becomes a chain with H = N - 1, and search becomes 
O(N). So O(log N) is the best case, not a guarantee. The accurate statement is O(H), which ranges from O(log N) to O(N) depending on the tree’s shape. Only self-balancing 
trees like AVL or red-black trees guarantee O(log N).

## Part 13 Ordered Operations

The values to return are 40, 50, 60, and 70.

Two branches can be ignored. The first is the left subtree of 30, which holds 20. Since 30 is already below 35, everything to its left is 
smaller still and can’t be in range. The second is the right subtree of 70, which holds 80. Since 70 is the upper bound, everything to its right is larger than 70 and out of range.

At each node, the comparison with the range bounds tells you which subtrees could still hold valid values. If the node is less than or equal 
to the lower bound, its whole left subtree is even smaller, so it can be skipped. If the node is greater than or equal to the upper bound, its 
whole right subtree is even larger, so it can be skipped. Only subtrees that could overlap the range are explored. Here, that means 20 and 80 are never visited.

## Part 14 Memory Management

```cpp
void BinarySearchTree::deleteTree(Node* node) {
    if (node == nullptr) return;
    deleteTree(node->left);
    deleteTree(node->right);
    delete node;
}

BinarySearchTree::~BinarySearchTree() {
    deleteTree(root);
}
```

### Analysis

The parent holds the only pointers to its children. If you delete the parent first, you can no longer reach its children, so they and everything below them would be lost and never freed. Deleting the children first means the parent is only freed after it’s no longer needed.

Postorder traversal, which goes left, right, then the node. It fully processes both subtrees before handling the node itself, which is exactly the order deletion needs.

It would cause a memory leak. The memory stays allocated after the tree is destroyed, but nothing points to it anymore, so it can’t be reused. In a long-running program, or one that creates many trees, leaked memory keeps piling up until the program is using far more than it needs or runs out.

Each recursive call goes one level deeper, so the maximum number of active calls on the stack is about H + 1. In a balanced tree that’s around log N, which is small. In a skewed tree H = N - 1, so recursion depth grows linearly with the number of nodes, and a large enough skewed tree can cause a stack overflow. An iterative version using an explicit stack or queue avoids that.

## Part 15 BST Test

Time to put it to the test :P

```cpp
BinarySearchTree bst;

    // 1. Empty tree
    cout << "=== 1. Empty tree ===" << endl;
    cout << "  empty(): " << (bst.empty() ? "true" : "false") << endl;
    try {
        bst.minimum();
    } catch (const runtime_error& e) {
        cout << "  minimum() threw: " << e.what() << endl;
    }

    // 2. Multiple insertions
    cout << "\n=== 2. Insert 50 30 70 20 40 60 80 10 ===" << endl;
    for (int v : {50, 30, 70, 20, 40, 60, 80, 10})
        bst.insert(v);
    cout << "  empty(): " << (bst.empty() ? "true" : "false") << endl;
    /*
              50
            /    \
          30      70
         /  \    /  \
       20   40  60   80
      /
    10
    */

    // 3. Duplicate insertion
    cout << "\n=== 3. Duplicate insert (40 again) ===" << endl;
    bst.insert(40);
    cout << "  Inorder:   "; bst.inorder();
    cout << "  Expected:  10 20 30 40 50 60 70 80 (40 appears once)" << endl;

    // 4 & 5. Search
    cout << "\n=== 4 & 5. Search ===" << endl;
    printSearch(bst, 60);   // found
    printSearch(bst, 75);   // not found

    // 6, 7, 8. Traversals
    cout << "\n=== 6-8. Traversals ===" << endl;
    printAll(bst,
             "10 20 30 40 50 60 70 80",
             "50 30 20 10 40 70 60 80",
             "10 20 40 30 60 80 70 50");

    // 9 & 10. Min and max
    cout << "\n=== 9 & 10. Min / Max ===" << endl;
    cout << "  minimum(): " << bst.minimum() << "  (expected 10)" << endl;
    cout << "  maximum(): " << bst.maximum() << "  (expected 80)" << endl;

    // 11. Leaf deletion
    cout << "\n=== 11. Remove 80 (leaf) ===" << endl;
    cout << "  remove(80): " << (bst.remove(80) ? "true" : "false") << endl;
    printAll(bst,
             "10 20 30 40 50 60 70",
             "50 30 20 10 40 70 60",
             "10 20 40 30 60 70 50");
    printSearch(bst, 80);

    // 12. One-child deletion
    cout << "\n=== 12. Remove 20 (one child: 10) ===" << endl;
    cout << "  remove(20): " << (bst.remove(20) ? "true" : "false") << endl;
    printAll(bst,
             "10 30 40 50 60 70",
             "50 30 10 40 70 60",
             "10 40 30 60 70 50");
    printSearch(bst, 20);
    printSearch(bst, 10);   

    
    cout << "\n=== 13. Remove 50 (two children, successor = 60) ===" << endl;
    cout << "  remove(50): " << (bst.remove(50) ? "true" : "false") << endl;
    printAll(bst,
             "10 30 40 60 70",
             "60 30 10 40 70",
             "10 40 30 70 60");
    printSearch(bst, 50);
    /*
            60
           /  \
         30    70
        /  \
      10    40
    */

    //1. Behavior after deletions
    cout << "\n=== 14. After deletions ===" << endl;
    cout << "  remove(999): " << (bst.remove(999) ? "true" : "false") << "  (expected false)" << endl;
    cout << "  minimum(): " << bst.minimum() << "  (expected 10)" << endl;
    cout << "  maximum(): " << bst.maximum() << "  (expected 70)" << endl;

    cout << "  All remaining values:" << endl;
    for (int v : {10, 30, 40, 60, 70})
        printSearch(bst, v);

    cout << "  Insert 65 and 35 into the modified tree:" << endl;
    bst.insert(65);
    bst.insert(35);
    cout << "  Inorder:   "; bst.inorder();
    cout << "  Expected:  10 30 35 40 60 65 70" << endl;
```
Proof:

<img width="1540" height="725" alt="image" src="https://github.com/user-attachments/assets/f55bcf15-0156-4a7b-8e41-ab50e5ec4d42" />

<img width="1597" height="1082" alt="image" src="https://github.com/user-attachments/assets/a81b3f5c-900c-4f75-9bb9-7b90e130e503" />


## Analysis and Reflection

The BST invariant applies to entire subtrees because every value passed through the same comparisons on the way down. In my insert(), 
40 went left at 50 and right at 30, so it’s smaller than 50 even though it isn’t 50’s direct child. Every node in 50’s left subtree took 
that same left turn, so the whole subtree is smaller than 50, not just the immediate child.

Insertion order affects tree shape because the tree never rebalances. The first value becomes the root, and each later value is placed by 
comparing it against the nodes already there. Inserting 50, 30, 70... gave a balanced tree, but inserting the same values in sorted order would have sent every one to the right.

Search follows only one path because each comparison rules out a whole subtree. In my trace for 75, going right at 45 meant 25, 15, 35, 30, and 
40 were never checked. The search only visited 4 of 10 nodes.

Inorder traversal produces sorted output because it prints the left subtree, then the node, then the right subtree. By the invariant, that’s 
smaller values, then the node, then larger values, applied recursively. My tests relied on this: after every deletion, sorted inorder output showed the ordering survived.

Deletion is more complicated than insertion because insertion always adds a new leaf, while deletion can remove a node from the middle of the tree. 
Leaf and one-child deletions just relink the parent, but removing 50, which had two children, required finding the successor 60, copying it up, and running a second delete to remove the original 60.

BST performance depends on height because search, insert, delete, min, and max each follow a single path, taking one step per level. My table showed 
all of them are O(H). Only full traversals, which visit every node, are O(N) no matter the shape.

A skewed BST can behave like a linked list because sorted insertion gives every node only one child. The tree becomes a single chain, so search walks node 
by node like a list, and both H and the cost become N - 1. The recursive deleteTree also reaches depth N, which risks a stack overflow.

Self-balancing trees like AVL and red-black trees are useful because they restructure themselves with rotations during insert and delete, keeping H at O(log N) 
regardless of insertion order. That turns my implementation’s best case into a guaranteed case, so insertion order can never degrade the tree into a list.
