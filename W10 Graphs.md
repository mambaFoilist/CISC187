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

   
