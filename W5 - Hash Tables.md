# W5 Hash Table

## Part 1 Understanding Hash Functions

|Key|Digit Sum|Table Index|
|---|---|---|
|555223|5+5+5+2+2+3 = 22|2|
|555980|5+5+5+9+8+0 = 32|2|
|555000|5+5+5+0+0+0 = 15|5|
|555890|5+5+5+8+9+0 = 32|2|

Yes. The first, second, and fourth key produced the same key. This is called a collision.

% 10 produces an int that is between 0 and 9, precisely what the indices of a 10 slot array are.

No. 555980 and 555890 both have a digit sum of 32, so they collide no matter how big the table is. Also, there are more possible keys than slots, so by the pigeonhole principle some keys must share a slot.

## Part 2 Implement a Hash Function

<img width="701" height="1170" alt="image" src="https://github.com/user-attachments/assets/86c3be6f-dc7d-4b69-959c-0393448c0bcf" />

## Part 3 Build a Hash Table

I used vector<Slot> of size 11. Each slot holds a record and a state label.
No std::map or std::unordered_map is used. In main I call HashTable h(11);

<img width="557" height="547" alt="image" src="https://github.com/user-attachments/assets/d94762b1-3e06-4e22-ac68-19c31eb78a69" />

## Part 4 Linear Probing

Insert first searches for the key. If it exists, the value is updated (no duplicates).
Otherwise, it probes (home + i) % size and places the key in the first slot that is not OCCUPIED.

It avoids an infinite loop by running the probe loop from i = 0 to size - 1. It examines each slot at most once.
If all slots have been checked and none are available, that means the table is full and insert returns false.

<img width="436" height="390" alt="image" src="https://github.com/user-attachments/assets/bf5a6263-6c29-47bb-8bca-7382e5d99ea5" />

## Part 5 Home Position vs Actual Position

<img width="922" height="301" alt="image" src="https://github.com/user-attachments/assets/a484ade9-b697-4eba-a6c1-467445c01d57" />

A key may not be stored at its home position because its key maps to an occupied home position meaning that 
a collision occurs. Thus, we probe to find it a new home.

Linear probing works by checking the spots next to it, sequentially.

A large distance means that every search needs to walk that path to get the desired data.

## Part 6 Searching with Linear Probing

<img width="590" height="265" alt="image" src="https://github.com/user-attachments/assets/4e41f141-044a-4fd1-b8ab-3a2b40991e30" />

| Search Type | Key | Positions Examined | Found? |
|---|---|---|---|
| Home-position key | 555000 | 1 | Yes |
| Displaced key | 555890 | 3 | Yes |
| Missing key | 999999 | 4 | No |

O(1) is the average case (assuming keys are spread out). A displaced key has to probe past every key sitting in its path but it will
typically be O(1) time.

## Part 7 Deletion and Tombstones

Demonstration: 

<img width="546" height="642" alt="image" src="https://github.com/user-attachments/assets/08a46169-0e75-4276-bf88-a66c929ec61a" />

A search for C starts at its home index. If that index were EMPTY, the search would assume the chain ends there and report 
"not found" even though C is just a little further down. The DELETED tells the search to keep going.

## Part 8 Load Factor

Experiment:

<img width="656" height="395" alt="image" src="https://github.com/user-attachments/assets/4f3df3b5-3857-4f27-9c43-d778b984f4d7" />

They go up. With a low load factor, nearly every key will land home. However, with more and more values occupying 
the data table, it is more likely that we will have to probe a bit to keep filling in values. 

1.0 means that it is full. You can't fill it more than 100%. The performance drops near 1.0 because there are very few
spots left, meaning that it is likely you will have to probe a ton to get a free slot.

## Part 9 Hash Function Quality

Given that my function uses the sum of the digits, I will use these two databases.

Dataset A (spread out, digit sums 1 to 8): 100000, 100001, 100002, 100003, 100004, 100005, 100006, 100007

Dataset B (all digit sum 3): 100002, 100011, 100101, 101001, 110001, 200001, 100020, 120000

<img width="851" height="408" alt="image" src="https://github.com/user-attachments/assets/783855d3-0f9a-4371-a6f7-3678c7ddb28f" />

<img width="707" height="77" alt="image" src="https://github.com/user-attachments/assets/4b654dfe-7ac7-449b-a373-965ce60f97d8" />

As expected, dataset A was better. Dataset B produced more collisions as each key mapped to the same exact home position.
Hash-function quality affects performance because a poor function causes more collisions, leading to a longer run time.
A large cluster will form, meaning that every insert and search needs to walk, degrading it to O(n).

## Part 10 Complexity Analysis

Q1: Worse-case scenario, it will need to walk the entire way, searching 1000 elements. 

Q2: It would take around 10. Binary search is O(log n), specifically log base 2 time. log2(1000) is around 9.97.

Q3: It will map every key to roughly its home position, meaning that each search will be O(1).

Q4: It degrades when many keys start to collide. A poor hash function, a load factor near 1.0, or lots of tombstones clog up
the dataset and lead to more probing.

Q5: O(1) is the AVERAGE case under good conditions. In worst case-scenarios, it is O(n).

## Part 11 Hashing vs Encrption

Hashing is meant to be 1 way. A hash maps any input to a fixed-size output and throws information away.
Keys can map to the same index, meaning you can't recover the original input from the hash.

Encryption is meant to be 2 way. That means you can reverse it. 

Hashing example: Storing password hashes. The system hashes the typed password and compares the hashes so it never
stores the real password.

Encryption example: HTTPS or messages apps encrypts. The receiver needs to read the original message but it needs 
to be complete garbarge to anyone trying to intercept it.

## Part 12: Cryptographic vs Non-cryptographic hashing

Deterministic: the same input always produces the same hash.

Pre-image resistance: given a hash output, it's computationally infeasible to find an input that produces it.

Avalanche effect: changing a single bit of the input changes about half of the output bits, so similar inputs give completely different hashes.

Collision resistance: it's computationally infeasible to find two different inputs with the same hash.

A hash table only needs a function that is fast and spreads keys evenly across the slots. It doesn't need 
to resist attackers. Cryptographic hashes like SHA-256 are much slower, and that security is wasted 
when all you need is an array index.

## Part 13

A: Exact lookup by student ID. Good. This is exactly what hashing is built for: one key leads straight to one slot, that gives O(1) average.

B: Range query (IDs 500000 to 600000). Bad. Hashing scatters nearby keys to unrelated slots, so you'd have to scan the whole table. A sorted array or BST is better.

C: Sorted traversal. Bad. A hash table keeps no order, so you'd have to copy everything out and sort it, which is O(nlogn). A BST gives sorted order directly.

D: Minimum key. Bad. Finding the minimum means checking every slot, which is O(N). A min-heap or BST does this much faster.

E: Username lookup. Good. It's an exact match on a key (a string), which hashing handles in O(1) average.

## Analysis and Reflection

Hashing vs linear/binary search: hashing computes the index directly, so a key at home takes 1 probe, compared with about 
1,000 comparisons for linear search and about 10 for binary search on 1,000 elements.

Collisions are unavoidable: there are more possible keys than slots (pigeonhole principle). In Part 1, three of four keys collided.

Linear probing: on a collision, it tries (home + i) % size until it finds a free slot. 555890 went 10 → 0 → 1.

Hash function quality: good spread (Dataset A) gave 0 collisions and 1.0 average probes. Bad spread (Dataset B) gave 7 
collisions and 4.5 average probes.

Load factor: average probes rose from 1.0 to 3.27 as α went from 0.09 to 1.0, because fuller tables have longer clusters.

Deletion: the search stops at EMPTY, so an EMPTY deleted slot would break the chain and hide later keys.

Tombstones: searches probe past them and inserts can reuse them, but too many tombstones lengthen searches.

O(1) average, O(N) worst case: with good spread and a low α, operations take about 1 probe. With heavy collisions or a full 
table, they can scan the whole table.

When not to use a hash table: range queries, sorted order, or min/max, because hashing destroys key order.

# Complete Code:

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

int hashFunction(int key, int tableSize) {
    int digitSum = 0;
    while (key > 0) {
        digitSum += key % 10;
        key /= 10;
    }
    return digitSum % tableSize;
}

struct Record {
    int key;
    string value;
};

enum State { EMPTY, OCCUPIED, DELETED };

struct Slot {
    Record rec;
    State state = EMPTY;
};

class HashTable {
public:
    vector<Slot> table;
    int size;

    HashTable(int n) {
        size = n;
        table.resize(n);   // n empty slots
    }
    //part 4
    bool insert(int key, string value) {
        int probes;
        int found = search(key, probes);
        if (found != -1) {                        // already exists -> update
            table[found].rec.value = value;
            return true;
        }
        int home = hashFunction(key, size);
        for (int i = 0; i < size; i++) {
            int idx = (home + i) % size;
            if (table[idx].state != OCCUPIED) {   // EMPTY or DELETED
                table[idx].rec = {key, value};
                table[idx].state = OCCUPIED;
                return true;
            }
        }
        return false;
    }

    //part 5
    void display() {
        cout << "Index | Key | Value | Home | Actual\n";
        for (int i = 0; i < size; i++) {
            if (table[i].state == OCCUPIED)
                cout << i << " | " << table[i].rec.key << " | " << table[i].rec.value
                    << " | " << hashFunction(table[i].rec.key, size) << " | " << i << "\n";
            else if (table[i].state == DELETED)
                cout << i << " | DELETED\n";
            else
                cout << i << " | EMPTY\n";
        }
        cout << "\n";
    }

    //Part 6
    int search(int key, int &probes) {
        int home = hashFunction(key, size);
        probes = 0;
        
        for (int i = 0; i < size; i++) {
            int idx = (home + i) % size;
            probes++;
            if (table[idx].state == EMPTY) return -1;
            if (table[idx].state == OCCUPIED && table[idx].rec.key == key) return idx;
        }
        return -1;
    }

    //Portion of Part 7
    bool remove(int key) {
        int probes;
        int idx = search(key, probes);
        if (idx == -1) return false;
        table[idx].state = DELETED;   // tombstone
        return true;
    }

    double loadFactor() const {
        int occupied = 0;
        for (int i = 0; i < size; i++)
            if (table[i].state == OCCUPIED) occupied++;
        return (double)occupied / size;
    }
};

//Part 9 helper function :P
void runDataset(string name, vector<int> keys) {
    HashTable t(11);
    int collisions = 0, totalProbes = 0, maxProbes = 0;
    for (int k : keys) {
        t.insert(k, "x");
        int probes;
        int idx = t.search(k, probes);
        totalProbes += probes;
        if (probes > maxProbes) maxProbes = probes;
        if (idx != hashFunction(k, 11)) collisions++;
    }
    cout << name << ": keys = " << keys.size() << ", collisions = " << collisions
         << ", max probes = " << maxProbes
         << ", avg probes = " << (double)totalProbes / keys.size() << "\n";
}

int main(void){


    cout << "=== PART 2 ===\n";
    cout << "555223 -> " << hashFunction(555223, 10) << "\n";
    cout << "555980 -> " << hashFunction(555980, 10) << "\n";
    cout << "555000 -> " << hashFunction(555000, 10) << "\n";
    cout << "555890 -> " << hashFunction(555890, 10) << "\n\n";

    //Part 3
    HashTable h(11);

    //Part 4
    h.insert(555223, "John Smith");
    h.insert(555980, "Sarah Connor");
    h.insert(555000, "Isaac Newton");
    h.insert(555890, "Ada Lovelace");     // collides with 555980 at index 10, wraps to index 1
    h.insert(555980, "Sarah Connor v2");  // existing key -> value updated, no duplicate
    
    //Part 5
    cout << "=== PART 5 ===\n";
    h.display();

    //Part 6
    cout << "=== PART 6 ===\n";
    int probes, r;
    r = h.search(555000, probes);
    cout << "Home-position key 555000: probes = " << probes << ", found = " << (r != -1) << "\n";
    r = h.search(555890, probes);
    cout << "Displaced key 555890: probes = " << probes << ", found = " << (r != -1) << "\n";
    r = h.search(999999, probes);
    cout << "Missing key 999999: probes = " << probes << ", found = " << (r != -1) << "\n\n";

    //Part 7
    cout << "=== PART 7 ===\n";
    HashTable t(11);
    t.insert(100002, "A");   // all 3 have home 3
    t.insert(100011, "B");
    t.insert(100101, "C");  
    t.display();
    t.remove(100002);
    t.display();
    r = t.search(100101, probes);
    cout << "Search 100101: found = " << (r != -1) << ", index = " << r << ", probes = " << probes << "\n\n";
    

    //Part 8
    cout << "=== PART 8 ===\n";
    cout << "Elements | Load Factor | Collisions | Avg Probes\n";
    HashTable lf(11);
    int keys[] = {555223, 555980, 555000, 555890, 123456, 654321,
                111111, 222222, 333333, 444444, 100002};
    int collisions = 0, totalProbes = 0, n = 0;
    for (int k : keys) {
        lf.insert(k, "x");
        int p;
        int idx = lf.search(k, p);                    // probes to reach it = probes used to place it
        n++;
        totalProbes += p;
        if (idx != hashFunction(k, 11)) collisions++; // not at home = collision
        cout << n << " | " << lf.loadFactor() << " | " << collisions
            << " | " << (double)totalProbes / n << "\n";
    }
    cout << "\n";

    //Part 9
    cout << "=== PART 9 ===\n";
    runDataset("Dataset A", {100000, 100001, 100002, 100003, 100004, 100005, 100006, 100007});
    runDataset("Dataset B", {100002, 100011, 100101, 101001, 110001, 200001, 100020, 120000});
    return 0;
}
```
