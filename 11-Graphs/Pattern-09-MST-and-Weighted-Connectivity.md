# Pattern 09 — MST & Weighted Connectivity

A **Minimum Spanning Tree (MST)** of a connected weighted undirected graph is a subset of edges that connects all vertices together without any cycles while **minimizing the total edge weight sum**.

> [!NOTE]
> **Follow-Up Reference**: <a href="https://leetcode.com/problems/connecting-cities-with-minimum-cost/" target="_blank">LeetCode 1135 (Connecting Cities With Minimum Cost)</a> is a direct application of standard Kruskal's algorithm on an explicit edge list. It serves as an additional practice reference.

---

## Kruskal's vs Prim's Algorithm

```text
Kruskal's Algorithm (Edge-Centric):
  1. Sort all edges in non-decreasing order of weight.
  2. Iterate through sorted edges, add edge to MST if it connects 2 different components via DSU.
  3. Complexity: O(E log E)

Prim's Algorithm (Node-Centric):
  1. Start from an arbitrary node. Add all incident edges to a Min-Heap (Priority Queue).
  2. Extract min edge. If destination node unvisited, add to MST and push incident edges.
  3. Complexity: O(E log V)
```

### MST vs Shortest Path Distinction
- **Shortest Path (Dijkstra)**: Minimizes distance from **a specific source** node to destination node(s).
- **MST (Kruskal/Prim)**: Minimizes **total cost to span all nodes** globally.

---

## Pattern Questions (2 Canonical)

### 1. Min Cost to Connect All Points — <a href="https://leetcode.com/problems/min-cost-to-connect-all-points/" target="_blank">LeetCode 1584</a>

#### Problem Statement
Given an array `points` where `points[i] = [x_i, y_i]`, return the minimum cost to make all points connected. The cost of connecting two points `(x1, y1)` and `(x2, y2)` is the Manhattan distance: `|x1 - x2| + |y1 - y2|`.

#### Dense Graph Prim's Formulation
Between $N$ points, there are $O(N^2)$ implicit edges.
For dense graphs where $E \approx V^2$, **Prim's Algorithm without Min-Heap** (using simple distance array scan) runs in $O(V^2)$ time, outperforming Kruskal's $O(V^2 \log V)$.

#### Java Implementation (Prim's Algorithm $O(N^2)$)
```java
import java.util.Arrays;

public class MinCostToConnectPoints {
    public int minCostConnectPoints(int[][] points) {
        int n = points.length;
        int[] minDist = new int[n];
        Arrays.fill(minDist, Integer.MAX_VALUE);
        boolean[] inMST = new boolean[n];

        minDist[0] = 0;
        int totalCost = 0;

        for (int step = 0; step < n; step++) {
            // Find unvisited vertex with minimum edge weight
            int curr = -1;
            for (int i = 0; i < n; i++) {
                if (!inMST[i] && (curr == -1 || minDist[i] < minDist[curr])) {
                    curr = i;
                }
            }

            inMST[curr] = true;
            totalCost += minDist[curr];

            // Update distances to neighboring unvisited points
            for (int next = 0; next < n; next++) {
                if (!inMST[next]) {
                    int dist = Math.abs(points[curr][0] - points[next][0]) 
                             + Math.abs(points[curr][1] - points[next][1]);
                    if (dist < minDist[next]) {
                        minDist[next] = dist;
                    }
                }
            }
        }

        return totalCost;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(N^2)$ — Optimal for dense complete graphs.
- **Space Complexity:** $O(N)$ — Distance and `inMST` array storage.

---

### 2. Optimize Water Distribution in a Village — Classic Interview Problem (LeetCode 1168)

#### Problem Statement
There are `n` houses. You can supply water by building a well inside house `i` at cost `wells[i-1]`, or by piping water from another house `j` at cost `pipes[i] = [house1, house2, cost]`. Return minimum total cost to supply water to all houses.

#### Virtual Node Graph Modeling Transformation
- **The Challenge**: We have two distinct choices per house: build an internal well or connect to an external pipe network.
- **Virtual Node Trick**: Introduce a **Virtual Source Node 0** representing the global water reservoir.
  - Building a well at house `i` with cost $W_i$ is equivalent to adding a weighted edge `0 -> i` with weight $W_i$.
  - Existing pipes remain edges `u -> v` with weight $P_{uv}$.
- Now, the problem transforms into finding a standard Minimum Spanning Tree over $n + 1$ nodes!

```text
      Virtual Well Node (0)
        /    |    \
     w1/   w2|     \w3
      ▼      ▼      ▼
   House 1 ───── House 2 ───── House 3
            p12           p23
```

#### Java Implementation (Virtual Node + Kruskal's MST)
```java
import java.util.*;

public class OptimizeWaterDistribution {
    public int minCostToSupplyWater(int n, int[] wells, int[][] pipes) {
        List<int[]> edges = new ArrayList<>();

        // 1. Add virtual node (0) edges for wells
        for (int i = 0; i < n; i++) {
            edges.add(new int[]{0, i + 1, wells[i]});
        }

        // 2. Add pipe edges
        for (int[] pipe : pipes) {
            edges.add(pipe);
        }

        // 3. Sort edges by weight
        edges.sort((a, b) -> Integer.compare(a[2], b[2]));

        // 4. Kruskal's MST on n + 1 nodes (0 to n)
        DisjointSet ds = new DisjointSet(n + 1);
        int totalCost = 0;
        int edgesCount = 0;

        for (int[] edge : edges) {
            if (ds.union(edge[0], edge[1])) {
                totalCost += edge[2];
                edgesCount++;
                if (edgesCount == n) break; // MST completed for n+1 nodes
            }
        }

        return totalCost;
    }

    private static class DisjointSet {
        int[] parent;
        public DisjointSet(int n) {
            parent = new int[n];
            for (int i = 0; i < n; i++) parent[i] = i;
        }
        public int find(int i) {
            if (parent[i] == i) return i;
            return parent[i] = find(parent[i]);
        }
        public boolean union(int i, int j) {
            int rootI = find(i);
            int rootJ = find(j);
            if (rootI != rootJ) {
                parent[rootI] = rootJ;
                return true;
            }
            return false;
        }
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O((E + V) \log(E + V))$ — Edge sorting dominates time complexity.
- **Space Complexity:** $O(E + V)$ — Edge list and Union-Find array.

---

## Pattern Comparison Matrix

| Problem | Graph Topology | Algorithm Applied | Key Trick / Modeling Concept |
| :--- | :--- | :--- | :--- |
| **LC 1584 (Min Cost Points)** | Dense Implicit Complete Graph | Prim's $O(N^2)$ | Array-scan Prim's avoids priority queue overhead |
| **LC 1168 (Water Supply)** | Explicit Pipes + Node Costs | Kruskal's MST | Virtual Node `0` unifies node costs into edge costs |
