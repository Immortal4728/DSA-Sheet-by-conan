# Section 11 — Graphs

Graphs represent relationships between objects. A graph $G = (V, E)$ consists of a set of vertices (nodes) $V$ and a set of edges (connections) $E$. Graph algorithms form one of the most vital foundations of modern software engineering, powering search engines, social networks, routing protocols, and dependency management systems.

---

## Final Curriculum Architecture

This module contains **34 canonical Graph questions**, organized across 10 structured pattern files:

```text
11-Graphs/
├── Pattern-01-Graph-Representation-and-Traversal.md
├── Pattern-02-BFS-and-DFS-Problem-Solving.md
├── Pattern-03-Grid-as-Graph.md
├── Pattern-04-Connected-Components-and-Connectivity.md
├── Pattern-05-Cycle-Detection.md
├── Pattern-06-Topological-Sort-and-DAG.md
├── Pattern-07-Shortest-Path.md
├── Pattern-08-Union-Find-and-Dynamic-Connectivity.md
├── Pattern-09-MST-and-Weighted-Connectivity.md
├── Pattern-10-Hidden-Graph-Recognition.md
└── README.md
```

---

## 1. Core Terminology & Representation

### 1. Graph Terminology and Modeling
- **Vertex (Node)**: An individual entity or state.
- **Edge**: A connection between two nodes.
- **Degree**: Number of edges connected to a node (In-degree vs Out-degree in directed graphs).
- **Path**: A sequence of edges connecting a sequence of vertices.

### 2. Adjacency List vs Adjacency Matrix
- **Adjacency List (`List<List<Integer>>` / `Map<U, List<V>>`)**:
  - Preferred for sparse graphs ($E \ll V^2$).
  - Space: $O(V + E)$. Neighbor traversal: $O(\text{degree}(u))$.
- **Adjacency Matrix (`int[][] adj`)**:
  - Preferred for dense graphs ($E \approx V^2$) or fast edge lookup.
  - Space: $O(V^2)$. Edge existence lookup: $O(1)$.

### 3. Directed vs Undirected Graphs
- **Undirected Graph**: Edges are bidirectional ($u \leftrightarrow v$). Edges are added to both adjacency lists.
- **Directed Graph (Digraph)**: Edges have orientation ($u \to v$). Edge added only to $u$'s list.

### 4. Weighted vs Unweighted Graphs
- **Unweighted Graph**: All edges have uniform cost (implicitly 1).
- **Weighted Graph**: Edges have non-negative or negative costs/distances.

---

## 2. Traversal & Grid Modeling

### 5. BFS and DFS
- **Breadth-First Search (BFS)**: Uses a Queue (`LinkedList` / `ArrayDeque`). Explores level-by-level (radiating outward). Guarantees shortest path in unweighted graphs.
- **Depth-First Search (DFS)**: Uses a Call Stack (recursion) or explicit Stack. Explores as deep as possible along each branch before backtracking.

### 6. Visited-State Management
Prevents infinite recursion loops in cyclic graphs:
- `boolean[] visited` or `Set<Integer> visited`.
- **In-place marking**: Mutating grid cells directly (e.g., `'1' -> '0'`) to save space.

### 7. Grid as an Implicit Graph
A 2D matrix of size $M \times N$ is an implicit graph where cell `(r, c)` is a node connected to up to 4 or 8 adjacent valid neighbor cells.

---

## 3. Structural Graph Properties

### 8. Connected Components
Maximal connected subgraphs where every vertex is reachable from any other vertex in the same component. Computed via DFS, BFS, or Disjoint Set Union (DSU).

### 9. Cycle Detection
- **Undirected Graphs**: Cycle exists if DFS encounters a visited node that is **not** the immediate parent (`visited && neighbor != parent`).
- **Directed Graphs**: Cycle exists if DFS encounters a node that is currently in the **active recursion stack** (3-Color State array: 0=Unvisited, 1=Visiting, 2=Visited).

---

## 4. Ordering & Shortest Paths

### 10. Topological Sorting
A linear ordering of vertices in a DAG such that for every edge $u \to v$, $u$ appears before $v$.

### 11. DAG Reasoning & Kahn's Algorithm
- **Kahn's Algorithm**: BFS approach using an `indegree` array. Nodes with indegree 0 are added to the queue. Decrement indegrees of neighbors as nodes are processed.
- If processed count $< V$, graph contains a cycle.

### 12. Shortest Paths
- **Unweighted Graph**: Level-by-level BFS ($O(V + E)$).
- **Positive Weighted Graph**: Dijkstra's Algorithm ($O(E \log V)$).

### 13. Dijkstra's Algorithm
Uses a Min-Heap Priority Queue ordered by cumulative distance `dist[u]`. Relaxes edges greedily: if `dist[u] + weight < dist[v]`, update `dist[v]` and push to Priority Queue.

### 14. State-Augmented Shortest Paths
When extra constraints exist (e.g., at most $K$ stops or $K$ obstacle eliminations), standard 1D distance arrays fail. Augment the state space to 2D/3D: `dist[node][state]`.

---

## 5. Connectivity & Spanning Trees

### 15. Union-Find (Disjoint Set Union - DSU)
Maintains disjoint partitions over elements. Optimizations:
- **Path Compression**: Flattening tree structure during `find()`.
- **Union by Rank/Size**: Attaching smaller tree under root of larger tree.
- Amortized Operation Time: $O(\alpha(N))$ (Inverse Ackermann function $\approx O(1)$).

### 16. Minimum Spanning Trees (MST)
A subgraph connecting all $V$ nodes with $V - 1$ edges that minimizes total edge weight sum.

### 17. Kruskal's vs Prim's Algorithm
- **Kruskal's Algorithm**: Edge-centric. Sort all edges by weight, add edge if it connects disjoint components via DSU ($O(E \log E)$). Best for sparse graphs.
- **Prim's Algorithm**: Node-centric. Grow tree from an initial vertex using Min-Heap or distance array scan ($O(V^2)$ for dense complete graphs).

---

## 6. Graph Recognition & Decision Boundaries

### 18. Graph Modeling & Hidden Graphs
Any problem with:
```text
States + Transitions + Neighbors + Visited = GRAPH
```
Disguised examples:
- Array jumps $\to$ Reachability Graph
- System equations $\to$ Weighted Directed Graph
- Lock state turns $\to$ Unweighted State Space Graph

### 19. Complexity Analysis Summary

| Algorithm / Pattern | Time Complexity | Space Complexity | Best Use Case |
| :--- | :--- | :--- | :--- |
| **BFS / DFS Traversal** | $O(V + E)$ | $O(V)$ | Reachability, components, level order |
| **Multi-Source BFS** | $O(M \times N)$ | $O(M \times N)$ | Grid distance propagation, rotting timer |
| **Kahn's Topological Sort** | $O(V + E)$ | $O(V + E)$ | Dependency ordering, DAG scheduling |
| **Dijkstra's Algorithm** | $O(E \log V)$ | $O(V + E)$ | Single-source shortest path (positive weights) |
| **Union-Find (DSU)** | $O(E \cdot \alpha(V))$ | $O(V)$ | Dynamic connectivity, component merging |
| **Kruskal's MST** | $O(E \log E)$ | $O(V + E)$ | Spanning tree minimum total cost |
| **Prim's MST** | $O(V^2)$ or $O(E \log V)$ | $O(V)$ | Complete dense graph minimum spanning tree |

---

## 7. Java Graph Implementation Templates

### 20. Java Implementation Patterns

#### Standard Adjacency List Construction
```java
List<List<Integer>> adj = new ArrayList<>();
for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
for (int[] edge : edges) {
    adj.get(edge[0]).add(edge[1]);
    adj.get(edge[1]).add(edge[0]); // Remove for directed graph
}
```

#### Standard Disjoint Set Union (DSU) Template
```java
class DisjointSet {
    int[] parent, rank;
    public DisjointSet(int n) {
        parent = new int[n];
        rank = new int[n];
        for (int i = 0; i < n; i++) parent[i] = i;
    }
    public int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]); // Path compression
    }
    public boolean union(int i, int j) {
        int rootI = find(i), rootJ = find(j);
        if (rootI != rootJ) {
            if (rank[rootI] < rank[rootJ]) parent[rootI] = rootJ;
            else if (rank[rootI] > rank[rootJ]) parent[rootJ] = rootI;
            else { parent[rootJ] = rootI; rank[rootI]++; }
            return true;
        }
        return false;
    }
}
```

---

## 8. Algorithm Selector Guidelines

### 21. When to Use Which Algorithm

```text
Problem Goal                                                Recommended Tool
───────────────────────────────────────────────────────────────────────────────
Check if path exists or count islands                     ──► DFS / BFS
Find shortest path in unweighted graph / grid             ──► Level BFS
Find shortest path in positive weighted graph             ──► Dijkstra
Order tasks with dependency constraints                   ──► Kahn's Topological Sort
Detect cycle in directed graph                            ──► 3-Color DFS
Dynamic connectivity / merging sets                       ──► Union-Find (DSU)
Connect all nodes with minimum total weight               ──► Kruskal / Prim MST
State-constrained shortest path (e.g. max K stops)        ──► Augmented State BFS / Dijkstra
```

### 22. Recognizing Hidden Graphs
- **Array Jumps**: Indices are nodes, jump steps are edges.
- **Equations ($\frac{A}{B} = 2.0$)**: Variables are nodes, division values are directed edge weights.
- **Lock States (`"0000"`)**: Strings are nodes, single wheel rotations are unweighted edges.

---

## Mastery Standard

Upon completing Section 11, the learner will be able to:
- Build adjacency representation fast from raw arrays or edge lists.
- Choose between DFS and BFS appropriately based on path or level requirements.
- Manage visited state cleanly (in-place or via Sets/Arrays).
- Solve component counting and size calculation problems effortlessly.
- Model 2D matrix grids as implicit graphs and apply multi-source BFS.
- Detect cycles in both undirected (parent tracking) and directed (3-color state) graphs.
- Implement Kahn's algorithm for topological sorting and dependency graphs.
- Implement Dijkstra's algorithm for positive weighted shortest paths.
- Augment state definitions when shortest path problems contain extra constraints.
- Implement DSU with path compression and union by rank.
- Implement Kruskal and Prim for Minimum Spanning Trees and distinguish MST from shortest paths.
- Recognize hidden graph problems disguised as arrays, equations, or string state spaces.
- Implement graph algorithms cleanly from a blank editor in Java.
