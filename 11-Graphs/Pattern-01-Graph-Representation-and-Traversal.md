# Pattern 01 — Graph Representation & Traversal

Graph traversal is the fundamental operation of visiting all vertices (nodes) and edges in a graph systematic manner. Before tackling advanced graph algorithms, one must master converting raw inputs into graph representations (Adjacency List vs Adjacency Matrix) and traversing them cleanly using Depth-First Search (DFS) or Breadth-First Search (BFS).

---

## Core Concept & Representation

### Adjacency List (Standard Choice)
An array or map of lists representing edges emanating from each node.
- **Space Complexity:** $O(V + E)$
- **Lookup Time for Neighbors of node $u$:** $O(\text{degree}(u))$

```java
// Adjacency List Representation in Java (0-indexed nodes 0..V-1)
List<List<Integer>> adj = new ArrayList<>();
for (int i = 0; i < V; i++) adj.add(new ArrayList<>());
for (int[] edge : edges) {
    adj.get(edge[0]).add(edge[1]);
    adj.get(edge[1]).add(edge[0]); // For undirected graph
}
```

### Traversal Paradigms: DFS vs BFS
- **Depth-First Search (DFS)**: Uses a call stack (recursion) or explicit stack. Explores as deep as possible along each branch before backtracking. Best for path enumeration, cycle detection, and component exploration.
- **Breadth-First Search (BFS)**: Uses a queue (`LinkedList` or `ArrayDeque`). Explores nodes level-by-level (radiating outward). Best for unweighted shortest paths and level-order traversal.

---

## Pattern Questions (4 Canonical)

### 1. Find if Path Exists in Graph — <a href="https://leetcode.com/problems/find-if-path-exists-in-graph/" target="_blank">LeetCode 1971</a>

#### Problem Statement
Given an undirected graph with `n` vertices, an array `edges` where `edges[i] = [u_i, v_i]`, a `source` node, and a `destination` node, return `true` if there is a valid path from `source` to `destination`.

#### Key Insight & Traversal Mechanics
This is a baseline reachability problem. Build an adjacency list and perform either BFS or DFS starting from `source`. Track visited nodes in a boolean array to prevent infinite loops.

#### Java Implementation (BFS Approach)
```java
import java.util.*;

public class PathExistsInGraph {
    public boolean validPath(int n, int[][] edges, int source, int destination) {
        if (source == destination) return true;

        List<List<Integer>> adj = new ArrayList<>();
        for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
        for (int[] edge : edges) {
            adj.get(edge[0]).add(edge[1]);
            adj.get(edge[1]).add(edge[0]);
        }

        boolean[] visited = new boolean[n];
        Queue<Integer> queue = new ArrayDeque<>();

        queue.offer(source);
        visited[source] = true;

        while (!queue.isEmpty()) {
            int curr = queue.poll();
            if (curr == destination) return true;

            for (int neighbor : adj.get(curr)) {
                if (!visited[neighbor]) {
                    visited[neighbor] = true;
                    queue.offer(neighbor);
                }
            }
        }
        return false;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(V + E)$ — Visits each vertex and explores each edge once.
- **Space Complexity:** $O(V + E)$ — For adjacency list, queue, and visited array.

---

### 2. Keys and Rooms — <a href="https://leetcode.com/problems/keys-and-rooms/" target="_blank">LeetCode 841</a>

#### Problem Statement
There are `n` rooms labeled `0` to `n - 1`. Room `0` is unlocked. Each room `i` contains a list of keys `rooms[i]`. A key `v` unlocks room `v`. Return `true` if you can unlock and visit all rooms.

#### Key Insight & Implicit Graph Representation
The input `rooms` is already an adjacency list where directed edge `i -> v` means room `i` contains a key to room `v`. Start at node `0`, traverse all reachable nodes using DFS/BFS, and check if total visited count equals `n`.

#### Java Implementation (DFS Approach)
```java
import java.util.List;

public class KeysAndRooms {
    public boolean canVisitAllRooms(List<List<Integer>> rooms) {
        int n = rooms.size();
        boolean[] visited = new boolean[n];

        dfs(0, rooms, visited);

        for (boolean v : visited) {
            if (!v) return false;
        }
        return true;
    }

    private void dfs(int room, List<List<Integer>> rooms, boolean[] visited) {
        visited[room] = true;
        for (int key : rooms.get(room)) {
            if (!visited[key]) {
                dfs(key, rooms, visited);
            }
        }
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(V + E)$ — $V$ is number of rooms, $E$ is total number of keys.
- **Space Complexity:** $O(V)$ — Visited array and recursive call stack.

---

### 3. Clone Graph — <a href="https://leetcode.com/problems/clone-graph/" target="_blank">LeetCode 133</a>

#### Problem Statement
Given a reference node of a connected undirected graph, return a **deep copy** (clone) of the graph. Each node contains an `val` (int) and a `neighbors` (`List<Node>`).

#### Key Insight & Map-Based State Tracking
Graph cloning requires creating a 1-to-1 mapping between original nodes and cloned nodes (`Map<Node, Node> clonedMap`). During traversal (DFS or BFS):
1. If an unvisited node is encountered, instantiate its clone and register it in `clonedMap`.
2. Recursively/iteratively populate the `neighbors` list of the cloned node.

#### Java Implementation (DFS Approach)
```java
import java.util.*;

class Node {
    public int val;
    public List<Node> neighbors;
    public Node() { val = 0; neighbors = new ArrayList<>(); }
    public Node(int val) { this.val = val; neighbors = new ArrayList<>(); }
    public Node(int val, ArrayList<Node> neighbors) { this.val = val; this.neighbors = neighbors; }
}

public class CloneGraph {
    private Map<Node, Node> visited = new HashMap<>();

    public Node cloneGraph(Node node) {
        if (node == null) return null;

        // If node already cloned, return the cloned instance
        if (visited.containsKey(node)) {
            return visited.get(node);
        }

        // Instantiate clone
        Node clone = new Node(node.val);
        visited.put(node, clone);

        // Copy all neighbors recursively
        for (Node neighbor : node.neighbors) {
            clone.neighbors.add(cloneGraph(neighbor));
        }

        return clone;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(V + E)$ — Visits every node and edge once.
- **Space Complexity:** $O(V)$ — HashMap storage + recursion call stack depth.

---

### 4. All Paths From Source to Target — <a href="https://leetcode.com/problems/all-paths-from-source-to-target/" target="_blank">LeetCode 797</a>

#### Problem Statement
Given a Directed Acyclic Graph (DAG) of `n` nodes labeled `0` to `n - 1`, find all possible paths from node `0` to node `n - 1` and return them in any order.

#### Path Enumeration vs Mere Reachability
Unlike reachability problems which check *if* a path exists, LC 797 specifically requires **enumerating all valid paths**.
- Because the graph is a DAG (no cycles), we do not need a global `visited` array to prevent infinite loops.
- Use Backtracking DFS: add the current node to the temporary path, recurse for neighbors, and remove the node upon backtracking.

#### Java Implementation
```java
import java.util.ArrayList;
import java.util.List;

public class AllPathsFromSourceToTarget {
    public List<List<Integer>> allPathsSourceTarget(int[][] graph) {
        List<List<Integer>> result = new ArrayList<>();
        List<Integer> currentPath = new ArrayList<>();
        currentPath.add(0);

        dfs(0, graph, graph.length - 1, currentPath, result);
        return result;
    }

    private void dfs(int node, int[][] graph, int target, List<Integer> currentPath, List<List<Integer>> result) {
        if (node == target) {
            result.add(new ArrayList<>(currentPath)); // Copy valid path
            return;
        }

        for (int neighbor : graph[node]) {
            currentPath.add(neighbor); // Choose
            dfs(neighbor, graph, target, currentPath, result); // Explore
            currentPath.remove(currentPath.size() - 1); // Backtrack
        }
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(2^V \times V)$ — Worst-case number of paths in a DAG with $V$ nodes is $2^{V-2}$, each taking $O(V)$ to copy.
- **Space Complexity:** $O(V)$ — Path recursion stack depth.

---

## Pattern Summary & Traversal Selector

| Question | Type | Representation | Key Feature |
| :--- | :--- | :--- | :--- |
| **LC 1971 (Path Exists)** | Reachability | Edges Array $\to$ Adj List | Baseline BFS/DFS + boolean `visited[]` |
| **LC 841 (Keys & Rooms)** | Reachability | Implicit Directed Adj List | Traversal coverage validation |
| **LC 133 (Clone Graph)** | Graph Copy | Object Pointers | `HashMap<Node, Node>` duplicate mapping |
| **LC 797 (All Paths)** | Enumeration | Adjacency Matrix / List | Backtracking DFS (Path collection) |
