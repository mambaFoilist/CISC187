# Week 10 Graphs

## Part 1 Graph Terminology

Vertices: A, B, C, D, E, F

Vertices adjacent to B: A, C, D

Vertex adjacent to E: C, D, F

One valid path from A to F: A->B->D->E->F

This path is length 4

The shortest distance from A to F is A->C->E->F which is 4.

The distance between two vertices is essentially the smallest possible path length between the two vertices. Overall, path length is any valid sequence of nodes with length equal to or less the path distance.

## Part 2 Graphs vs Trees

A **graph** is any set of vertices together with edges connecting pairs of them. A **tree** is a special kind of graph that meets two extra conditions: it is **connected** and it is **acyclic** (has no cycles). Since a tree is still just vertices and edges, every tree is a graph. Most graphs break at least one of the tree conditions, so not every graph is a tree.

- **Cycles:** A tree cannot contain a cycle. That is why there is exactly one simple path between any two vertices in a tree. If a graph has a cycle, such as A-B-C-A in Part 1, some pair of vertices has two different routes between them, so the graph is not a tree.
- **Connectivity:** In a tree, every vertex can be reached from every other vertex. A connected graph on n vertices needs at least n - 1 edges, and a tree has exactly n - 1. With any fewer edges the graph comes apart, and with any more it must contain a cycle.
- **Disconnected vertices:** If even one vertex cannot be reached (an isolated vertex, or a separate component), the graph is not connected, so it is not a tree. An acyclic graph with several disconnected components is called a forest, meaning a collection of trees.

| Structure | Tree? | Reason |
|---|---|---|
| Connected graph with no cycles | **Yes** | Meets both conditions of a tree: connected and acyclic. |
| Connected graph containing a cycle | **No** | The cycle gives two distinct paths between some pair of vertices, which breaks the acyclic condition. |
| Acyclic graph with two disconnected components | **No** | It has no cycles, but it is not connected. It is a **forest** made of two trees. |


## Part 3 Adjacency List Representation
| Vertex | Adjacent Vertices |
|---|---|
| A | B, C |
| B | A, C, D |
| C | A, B, E |
| D | B, E |
| E | C, D, F |
| F | E |

*Check:* the degrees sum to 2 + 3 + 3 + 2 + 3 + 1 = 14 = 2 × 7 edges.

1. An undirected edge {u, v} can be traveled in both directions, so v is a neighbor of u and u is a neighbor of v. The list therefore stores v in u's list and u in v's list. For example, edge A-B shows up as B in A's list and as A in B's list. This is why the total number of list entries is the sum of the degrees, which equals 2E (the Handshaking Lemma).

2. The structure has two parts: one list header (or array slot) for each vertex, which is O(V) even if a vertex has no neighbors and the neighbor entries themselves, which total 2E for an undirected graph (E for a directed graph), and 2E is O(E).

   Adding these gives O(V) + O(E) = O(V + E). Both terms are needed because either one can be the larger: a graph can have many isolated vertices (V much larger than E) or many edges (E much larger than V).

   Essentially: to store a snapshot of who is friends with who in an undirected graph, you need to store who everyone is ad who is friends with each individual person.

4. A sparse graph has far fewer edges than the maximum possible, so E is much smaller than V². An adjacency matrix always uses Θ(v^2) space, whether or not the edges exist, and most of its cells would be 0. An adjacency list stores only the edges that actually exist, so it uses O(V + E) space. Listing all neighbors of a vertex v also takes O(deg(v)) time with a list, compared with O(V) to scan a whole matrix row. Traversals such as BFS and DFS therefore run in O(V + E) time instead of O(V^2). The trade-off is that checking whether one particular edge (u, v) exists takes O(deg(u)) time with a list, compared with O(1) for a matrix.

## Part 4 Adjacency Matrix Representation

|   | A | B | C | D | E | F |
|---|---|---|---|---|---|---|
| A | 0 | 1 | 1 | 0 | 0 | 0 |
| B | 1 | 0 | 1 | 1 | 0 | 0 |
| C | 1 | 1 | 0 | 0 | 1 | 0 |
| D | 0 | 1 | 0 | 0 | 1 | 0 |
| E | 0 | 0 | 1 | 1 | 0 | 1 |
| F | 0 | 0 | 0 | 0 | 1 | 0 |

1. The matrix is symmetric because this is an undirected graph. If u and v are connected, [u][v]  and [v][u] will both be true just in transposed spots.

2. It is constant O(1) time. You just need to directly access/refer to m[u][v] or m[v][u], either works.

3. The matrix requires O(V^2) space because you are storing V many rows and V many columns.

4. This representation may be wasteful for sparse graphs because with far fewer edges than possible, E will be muc-h much smaller than V^2. Therefore, even with a small E this will still require O(V^2) space, even if most of it is empty space.

   
## Part 5 Implement an Adjacency-List Graph

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <stack>
#include <stdexcept>

using namespace std;

class Graph {
private:
    vector<vector<int>> adjacencyList;

public:
    Graph(int vertices);

    void addEdge(int source, int destination);
    void display() const;

    void bfs(int start) const;
    void dfs(int start) const;
};

Graph::Graph(int vertices) : adjacencyList(vertices) {}

void Graph::addEdge(int source, int destination) {
    int n = static_cast<int>(adjacencyList.size());

    if(n == 0 || source < 0 || source >= n || destination < 0 || destination >= n) {
        throw out_of_range("vertex index out of range");
    }
    adjacencyList[source].push_back(destination);
    adjacencyList[destination].push_back(source);
}

void Graph::display() const{
    int n = static_cast<int>(adjacencyList.size());
    if(n == 0){
        std::cout << "Graph is empty" << std::endl;
        return;
    }

    for(int i = 0; i < n; i++){
        std::cout << i << ": ";
        int m = static_cast<int>(adjacencyList[i].size());
        for (int j = 0; j < m; j++){
            std::cout << adjacencyList[i][j] << "\t";
        }
        std::cout << "\n";
    }
}
```

Since the graph is undirected, addEdge pushes the destination into the source's list and the source into the destination's list. That way the edge 0 -- 1 shows up in both lists. display prints each vertex followed by its neighbors.

## Part 6 Breadth-First Search

```cpp
void Graph::bfs(int start) const{
    int n = static_cast<int>(adjacencyList.size());
    if(start < 0 || start >= n) {
        throw out_of_range("start vertex out of range");
    }

    vector<bool> visited(n, false);
    queue<int> visitLater;

    visited[start] = true;
    visitLater.push(start);

    while(!visitLater.empty()){
        int current = visitLater.front();
        visitLater.pop();

        std::cout << current << " ";

        for (int neighbor : adjacencyList[current]) {
            if(!visited[neighbor]){
                visited[neighbor] = true;
                visitLater.push(neighbor);
            }
        }
    }
    cout << "\n";
}
```

Vertices get marked as discovered when they are enqueued, so no vertex can enter the queue twice.

Edges: A-B, A-D, B-C, B-E, D-E, E-F, C-F

Queue is written front -> back.

| Step | Visited Vertex | Queue | Discovered Vertices |
|---|---|---|---|
| 0 | (start) | A | A |
| 1 | A | B, D | A, B, D |
| 2 | B | D, C, E | A, B, D, C, E |
| 3 | D | C, E | A, B, D, C, E |
| 4 | C | E, F | A, B, D, C, E, F |
| 5 | E | F | A, B, D, C, E, F |
| 6 | F | (empty) | A, B, D, C, E, F |

BFS order: A, B, D, C, E, F

| Vertex | Distance from A |
|---|---|
| A | 0 |
| B | 1 |
| D | 1 |
| C | 2 |
| E | 2 |
| F | 3 |

## Part 7 Depth-First Search

```cpp
void Graph::dfs(int start) const{
    int n = static_cast<int>(adjacencyList.size());

    if (start < 0 || start >= n){
        throw out_of_range("start vertex out of range");
    }

    vector<bool> visited(n, false);
    stack<int> visitLater;

    visitLater.push(start);

    while (!visitLater.empty()){
        int current = visitLater.top();
        visitLater.pop();

        if (visited[current]) continue;

        visited[current] = true;
        std::cout << current << " ";

        for (int neighbor : adjacencyList[current]) {
            visitLater.push(neighbor);
        }
    }
    cout << "\n";
}
```

Unlike BFS, DFS marks a vertex visited when it is popped. A vertex can be pushed more than once, so already-visited vertices just get skipped when they come off the stack.

Neighbors are pushed alphabetically, so the alphabetically last one is on top and gets popped first. Stack is written bottom -> top.

| Step | Popped Vertex | Stack | Visited Vertices |
|---|---|---|---|
| 0 | (start) | A | (none) |
| 1 | A | B, D | A |
| 2 | D | B, A, E | A, D |
| 3 | E | B, A, B, D, F | A, D, E |
| 4 | F | B, A, B, D, C, E | A, D, E, F |
| 5 | E (skip) | B, A, B, D, C | A, D, E, F |
| 6 | C | B, A, B, D, B, F | A, D, E, F, C |
| 7 | F (skip) | B, A, B, D, B | A, D, E, F, C |
| 8 | B | B, A, B, D, A, C, E | A, D, E, F, C, B |
| 9-15 | E, C, A, D, B, A, B (all skip) | (empty) | A, D, E, F, C, B |

DFS order: A, D, E, F, C, B

BFS and DFS visit the same vertices but in different orders because of the data structure. BFS uses a queue (FIFO), so it finishes everything at distance 1 before going to distance 2. DFS uses a stack (LIFO), so it always goes to the most recently pushed vertex. That makes it go deep down one path (A->D->E->F->C) before it backtracks to B.

## Part 8 Directed Graphs

1. B and C, since the edges A->B and A->C leave A.

2. No. That would need an edge B->A. The edge A->B only goes one way, and B's only outgoing edge is B->D.

3. A->B->D->E

4. Yes, B->D->C->B.

5. A directed edge u->v only adds v to u's list. An undirected edge adds it to both lists. If you used the undirected addEdge on a directed graph, every edge would become two-way and you'd get paths that don't exist, like B->A.

## Part 9 Weighted Graphs

| Path | Calculation | Total Cost |
|---|---|---|
| A->B->E | 4 + 3 | 7 |
| A->C->B->E | 2 + 1 + 3 | 6 |
| A->C->D->E | 2 + 8 + 2 | 12 |

A->C->B->E has the lowest total weight at 6.

The cost of a path is the sum of its weights, not the number of edges. A->B->E only has 2 edges, but A-B alone costs 4. A->C->B->E has 3 edges, but A-C and C-B together only cost 3, so it ends up cheaper.

## Part 10 Dijkstra's Shortest Path Algorithm

Start: A = 0, everything else = ∞, all predecessors = null. Each step picks the unvisited vertex with the smallest distance. Visited neighbors are skipped since their distances are already final.

| Current Vertex | Adjacent Vertex | Current Distance | Alternative Distance | Update? | Predecessor |
|---|---|---|---|---|---|
| A (0) | B | ∞ | 0 + 4 = 4 | Yes | A |
| A (0) | C | ∞ | 0 + 2 = 2 | Yes | A |
| C (2) | B | 4 | 2 + 1 = 3 | Yes | C |
| C (2) | D | ∞ | 2 + 8 = 10 | Yes | C |
| B (3) | D | 10 | 3 + 5 = 8 | Yes | B |
| B (3) | E | ∞ | 3 + 3 = 6 | Yes | B |
| E (6) | D | 8 | 6 + 2 = 8 | No | B |
| D (8) | (all visited) | - | - | - | - |

1. 

| Vertex | Distance | Predecessor |
|---|---|---|
| A | 0 | null |
| B | 3 | C |
| C | 2 | A |
| D | 8 | B |
| E | 6 | B |

2. Following the predecessors back from E gives E <- B <- C <- A. Reversed, the path is A->C->B->E with cost 6, which matches Part 9.

3. The distance array only stores the total cost, not how you got there. Each predecessor records which vertex gave the better distance, so walking them backward from the destination rebuilds the actual path.

## Part 11 When Should Dijkstra's Algorithm Be Used?

| Graph | Use Dijkstra? | Reason |
|---|---|---|
| Weighted graph with only non-negative edge weights | Yes | This is what it's designed for. Adding non-negative edges can never make a path cheaper, so once a vertex is finalized its distance is correct. |
| Unweighted graph treated as all edges having weight 1 | Yes, but BFS is better | It works, but with every weight equal it behaves the same as BFS. BFS gets the same distances in O(V + E) without a priority queue. |
| Directed graph containing a negative edge weight | No | A negative edge can lower a vertex's distance after it was already finalized, so the results can be wrong. Use Bellman-Ford instead. |

## Part 12 Complexity Analysis

| Operation / Representation | Big-O | Explanation |
|---|---|---|
| Adjacency-list storage | O(V + E) | One list per vertex is O(V). The neighbor entries total 2E (undirected), which is O(E). |
| Adjacency-matrix storage | O(V^2) | It stores a V x V cell for every possible pair of vertices, whether the edge exists or not. |
| Matrix adjacency check | O(1) | Checking an edge is a single lookup, m[u][v]. |
| Dijkstra with list-based unvisited queue | O(V^2) | Each of the V iterations scans the whole unvisited list to find the minimum, which is O(V). That gives O(V^2). Edge relaxations add O(E), and E <= V^2. |
| Dijkstra with binary-heap priority queue | O((V + E) log V) | Each of the V vertices is removed from the heap once for O(log V). Each of up to E relaxations can push into the heap for O(log V). This beats O(V^2) on sparse graphs. |

## Part 13 Complete Program Test

Same graph as Part 6, with 0 = A, 1 = B, 2 = C, 3 = D, 4 = E, 5 = F. B and E each have 3 neighbors, A-B-E-D-A is a cycle, and A to F has multiple paths (A-B-C-F, A-B-E-F, A-D-E-F). The edges are added in alphabetical order so the lists stay sorted.

```cpp
int main() {
    // 0 = A, 1 = B, 2 = C, 3 = D, 4 = E, 5 = F
    Graph g(6);

    g.addEdge(0, 1); // A-B
    g.addEdge(0, 3); // A-D
    g.addEdge(1, 2); // B-C
    g.addEdge(1, 4); // B-E
    g.addEdge(2, 5); // C-F
    g.addEdge(3, 4); // D-E
    g.addEdge(4, 5); // E-F

    cout << "Adjacency list:\n";
    g.display();

    cout << "\nBFS from 0: ";
    g.bfs(0);

    cout << "DFS from 0: ";
    g.dfs(0);

    return 0;
}
```

Output:

```
Adjacency list:
0: 1	3
1: 0	2	4
2: 1	5
3: 0	4
4: 1	3	5
5: 2	4

BFS from 0: 0 1 3 2 4 5
DFS from 0: 0 3 4 5 2 1
```

BFS (A, B, D, C, E, F) goes out by distance: A, then B and D, then C and E, then F. DFS (A, D, E, F, C, B) pops D first because it was pushed last, keeps going deeper, and reaches B last even though B is directly connected to A. Both match the traces in Parts 6 and 7.

## Analysis and Reflection

- Adjacency lists only store edges that exist, so sparse graphs use O(V + E) space instead of O(V^2).
- An adjacency matrix checks adjacency in O(1) because it is just one lookup, m[u][v].
- BFS uses a queue so vertices get processed in the order they were discovered, which visits them layer by layer.
- DFS uses a stack so the most recently discovered vertex goes next, which makes it go deep before backtracking.
- Visited vertices have to be tracked because cycles (like A-B-E-D-A) would cause infinite loops or repeated visits.
- BFS finds shortest distance in unweighted graphs because the first time it reaches a vertex, it got there with the fewest edges.
- BFS doesn't solve weighted shortest paths because it minimizes the number of edges, not total weight (A->B->E has fewer edges but costs more than A->C->B->E).
- Directed edges can't be treated as two-way because A->B does not mean B->A, and doing so creates paths that don't exist.
- Dijkstra stores predecessors because distances only give the total cost, and predecessors let you rebuild the actual path.
- Dijkstra shouldn't be used with negative weights because it assumes a finalized vertex can't get cheaper later, and a negative edge breaks that.
