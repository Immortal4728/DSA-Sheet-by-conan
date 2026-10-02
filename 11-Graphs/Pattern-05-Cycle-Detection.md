# Pattern 05 — Cycle Detection

Cycle detection algorithms determine whether a graph contains a closed loop. Crucially, **Undirected Graphs** and **Directed Graphs** use fundamentally different cycle detection mechanisms.

---

## Fundamental Mechanics: Undirected vs Directed Cycle Detection

```text
Undirected Graph Cycle Detection:
  • An edge (u, v) creates a cycle IF v is already visited AND v != parent of u.
  • Parent tracking prevents false cycles from traversing backwards along the same edge.

Directed Graph Cycle Detection:
  • A back-edge creates a cycle IF a node is encountered while still in the ACTIVE recursion stack.
  • Requires 3-Coloring / State Array:
      0 = UNVISITED
      1 = VISITING (currently in recursion call stack)
      2 = VISITED (fully explored)
```

---

## Pattern Questions (2 Canonical)

### 1. Graph Valid Tree — Classic Interview Problem (LeetCode 261)

#### Problem Statement
Given `n` nodes labeled `0` to `n - 1` and a list of undirected edges, write a function to check whether these edges form a valid tree.

#### Key Insight & Tree Definition Invariants
By mathematical definition, an undirected graph is a **valid tree** if and only if:
1. It contains **no cycles**.
2. It is **fully connected** (contains exactly 1 connected component).
3. The number of edges is strictly equal to $V - 1$ (`edges.length == n - 1`).

#### Java Implementation (Undirected Cycle Detection with Parent Tracking)
```java
import java.util.ArrayList;
import java.util.List;

public class GraphValidTree {
    public boolean validTree(int n, int[][] edges) {
        // Condition 1: A tree with n nodes must have exactly n - 1 edges
        if (edges.length != n - 1) return false;

        List<List<Integer>> adj = new ArrayList<>();
        for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
        for (int[] edge : edges) {
            adj.get(edge[0]).add(edge[1]);
            adj.get(edge[1]).add(edge[0]);
        }

        boolean[] visited = new boolean[n];

        // Condition 2: Must not contain cycles (check starting from node 0)
        if (hasCycleUndirected(0, -1, adj, visited)) {
            return false;
        }

        // Condition 3: All nodes must be connected
        for (boolean v : visited) {
            if (!v) return false;
        }

        return true;
    }

    private boolean hasCycleUndirected(int curr, int parent, List<List<Integer>> adj, boolean[] visited) {
        visited[curr] = true;

        for (int neighbor : adj.get(curr)) {
            if (!visited[neighbor]) {
                if (hasCycleUndirected(neighbor, curr, adj, visited)) {
                    return true;
                }
            } else if (neighbor != parent) {
                // If neighbor is visited and is NOT parent -> Undirected Cycle detected!
                return true;
            }
        }
        return false;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(V + E)$ — Standard single-pass DFS.
- **Space Complexity:** $O(V + E)$ — Adjacency list and call stack space.

---

### 2. Course Schedule — <a href="https://leetcode.com/problems/course-schedule/" target="_blank">LeetCode 207</a>

#### Problem Statement
There are `numCourses` labeled `0` to `numCourses - 1`. You are given an array `prerequisites` where `prerequisites[i] = [a, b]` indicates that you must take course `b` first if you want to take course `a`. Return `true` if you can finish all courses (i.e., if the prerequisite graph has no directed cycles).

#### Key Insight & Directed 3-State Recursion
A cycle in a directed prerequisite graph makes it impossible to complete courses. We use 3 states:
- `0`: Unvisited node.
- `1`: Visiting (active node on current DFS recursion path).
- `2`: Visited (fully processed and confirmed cycle-free).

If DFS encounters a node with state `1`, a **directed cycle / back-edge** is detected!

#### Java Implementation (3-State Directed Cycle DFS)
```java
import java.util.ArrayList;
import java.util.List;

public class CourseSchedule {
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        List<List<Integer>> adj = new ArrayList<>();
        for (int i = 0; i < numCourses; i++) adj.add(new ArrayList<>());
        for (int[] pre : prerequisites) {
            adj.get(pre[1]).add(pre[0]); // Directed edge: pre[1] -> pre[0]
        }

        int[] state = new int[numCourses]; // 0: unvisited, 1: visiting, 2: visited

        for (int i = 0; i < numCourses; i++) {
            if (state[i] == 0) {
                if (hasCycleDirected(i, adj, state)) {
                    return false; // Directed cycle detected -> cannot finish courses
                }
            }
        }
        return true;
    }

    private boolean hasCycleDirected(int curr, List<List<Integer>> adj, int[] state) {
        state[curr] = 1; // Mark current node as VISITING

        for (int neighbor : adj.get(curr)) {
            if (state[neighbor] == 1) {
                return true; // Cycle found: neighbor is already in active stack!
            }
            if (state[neighbor] == 0) {
                if (hasCycleDirected(neighbor, adj, state)) {
                    return true;
                }
            }
        }

        state[curr] = 2; // Mark current node as VISITED (done)
        return false;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(V + E)$ — Visits every course and prerequisite edge once.
- **Space Complexity:** $O(V + E)$ — Graph representation + recursion call stack.

---

## Pattern Comparison Matrix

| Problem | Graph Type | Cycle Detection Mechanism | Key Invariants |
| :--- | :--- | :--- | :--- |
| **Graph Valid Tree** | Undirected | Parent tracking (`visited && neighbor != parent`) | $E == V - 1$, 1 component, 0 cycles |
| **LC 207 (Course Schedule)**| Directed | 3-State array (`0:Unvisited, 1:Visiting, 2:Visited`) | Cycle in active stack $\Rightarrow$ Invalid DAG |
