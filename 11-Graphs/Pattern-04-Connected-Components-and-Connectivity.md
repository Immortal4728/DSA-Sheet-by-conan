# Pattern 04 — Connected Components & Connectivity

A **Connected Component** in an undirected graph is a maximal subgraph where any two vertices are reachable from each other. Connected component problems involve counting components, computing component sizes, counting unreachable pairs, or grouping entities based on shared attributes (e.g., merging user accounts).

---

## Technical Approach: DFS vs Disjoint Set Union (DSU)

```text
       Adjacency Matrix / List              Union-Find (DSU)
  ┌────────────────────────────────┐   ┌────────────────────────────────┐
  │ • Natural for static graphs.   │   │ • Superior for dynamic edge    │
  │ • Simple recursive DFS / BFS.  │   │   insertions.                  │
  │ • O(V + E) traversal time.     │   │ • Nearly O(1) union/find       │
  └────────────────────────────────┘   │   amortized operations.        │
                                       └────────────────────────────────┘
```

---

## Pattern Questions (4 Canonical)

### 1. Number of Connected Components in an Undirected Graph — Classic Interview Problem

#### Problem Statement
Given `n` nodes labeled `0` to `n - 1` and an array `edges` where `edges[i] = [a_i, b_i]`, return the total number of connected components.

#### Key Insight & DSU Component Counter
Start with `n` isolated components. Each successful `union(u, v)` between two previously disconnected components decrements total component count by 1.

#### Java Implementation (Union-Find)
```java
public class NumberOfConnectedComponents {
    public int countComponents(int n, int[][] edges) {
        DisjointSet ds = new DisjointSet(n);
        int components = n;

        for (int[] edge : edges) {
            if (ds.union(edge[0], edge[1])) {
                components--;
            }
        }
        return components;
    }

    private static class DisjointSet {
        int[] parent;
        int[] rank;

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
            int rootI = find(i);
            int rootJ = find(j);
            if (rootI != rootJ) {
                if (rank[rootI] < rank[rootJ]) {
                    parent[rootI] = rootJ;
                } else if (rank[rootI] > rank[rootJ]) {
                    parent[rootJ] = rootI;
                } else {
                    parent[rootJ] = rootI;
                    rank[rootI]++;
                }
                return true;
            }
            return false;
        }
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(V + E \cdot \alpha(V))$ where $\alpha$ is the Inverse Ackermann function ($\approx O(1)$).
- **Space Complexity:** $O(V)$ for DSU arrays.

---

### 2. Number of Provinces — <a href="https://leetcode.com/problems/number-of-provinces/" target="_blank">LeetCode 547</a>

#### Problem Statement
There are `n` cities. Some are connected directly, represented by an `n x n` matrix `isConnected` where `isConnected[i][j] = 1` if city `i` and city `j` are directly connected. Return total number of provinces (connected components).

#### Matrix Traversal Formulation
Iterate over `i` from `0` to `n-1`. If city `i` has not been visited, trigger a DFS traversal that visits all reachable cities $j$ where `isConnected[i][j] == 1`, incrementing province count.

#### Java Implementation (DFS Approach)
```java
public class NumberOfProvinces {
    public int findCircleNum(int[][] isConnected) {
        int n = isConnected.length;
        boolean[] visited = new boolean[n];
        int provinces = 0;

        for (int i = 0; i < n; i++) {
            if (!visited[i]) {
                provinces++;
                dfs(i, isConnected, visited);
            }
        }
        return provinces;
    }

    private void dfs(int city, int[][] isConnected, boolean[] visited) {
        visited[city] = true;
        for (int neighbor = 0; neighbor < isConnected.length; neighbor++) {
            if (isConnected[city][neighbor] == 1 && !visited[neighbor]) {
                dfs(neighbor, isConnected, visited);
            }
        }
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(N^2)$ — Visits all entries of the adjacency matrix.
- **Space Complexity:** $O(N)$ — Visited boolean array + recursion depth.

---

### 3. Count Unreachable Pairs of Nodes in an Undirected Graph — <a href="https://leetcode.com/problems/count-unreachable-pairs-of-nodes-in-an-undirected-graph/" target="_blank">LeetCode 2316</a>

#### Problem Statement
Given an integer `n` and 2D array `edges`. Return the total number of pairs of nodes that are **unreachable** from each other.

#### Mathematical Component Combinatorics
Suppose the graph is partitioned into connected components with sizes $S_1, S_2, \dots, S_k$.
Two nodes are unreachable if and only if they belong to different components.
$$\text{Unreachable Pairs} = \sum_{i < j} (S_i \times S_j)$$

#### Fast Linear Calculation
Instead of a double sum, track remaining unvisited nodes `remainingNodes` initialized to `n`:
For each component size $S$:
$$\text{Pairs From Component } S = S \times (\text{remainingNodes} - S)$$
$$\text{remainingNodes} \leftarrow \text{remainingNodes} - S$$

#### Java Implementation
```java
import java.util.ArrayList;
import java.util.List;

public class CountUnreachablePairs {
    public long countPairs(int n, int[][] edges) {
        List<List<Integer>> adj = new ArrayList<>();
        for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
        for (int[] edge : edges) {
            adj.get(edge[0]).add(edge[1]);
            adj.get(edge[1]).add(edge[0]);
        }

        boolean[] visited = new boolean[n];
        long unreachablePairs = 0;
        long remainingNodes = n;

        for (int i = 0; i < n; i++) {
            if (!visited[i]) {
                long componentSize = dfs(i, adj, visited);
                unreachablePairs += componentSize * (remainingNodes - componentSize);
                remainingNodes -= componentSize;
            }
        }

        return unreachablePairs;
    }

    private long dfs(int node, List<List<Integer>> adj, boolean[] visited) {
        visited[node] = true;
        long size = 1;
        for (int neighbor : adj.get(node)) {
            if (!visited[neighbor]) {
                size += dfs(neighbor, adj, visited);
            }
        }
        return size;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(V + E)$ — Single linear graph traversal.
- **Space Complexity:** $O(V + E)$ — Adjacency list and visited storage.

---

### 4. Accounts Merge — <a href="https://leetcode.com/problems/accounts-merge/" target="_blank">LeetCode 721</a>

#### Problem Statement
Given a list of `accounts` where `accounts[i][0]` is a name, and remaining elements are emails. If two accounts share at least one email, the accounts belong to the same person. Merge the accounts and return sorted emails under the name.

#### Graph Modeling & Shared Attribute Union
LC 721 is a classic **real-world relationship modeling** problem:
1. Map each unique email to a unique integer ID or owner name.
2. For each account, union the **first email** with all other emails in that account.
3. Group emails by their root parent, sort them alphabetically, and format output.

#### Java Implementation (Union-Find Modeling)
```java
import java.util.*;

public class AccountsMerge {
    public List<List<String>> accountsMerge(List<List<String>> accounts) {
        int n = accounts.size();
        DisjointSet ds = new DisjointSet(n);
        Map<String, Integer> emailToAccId = new HashMap<>();

        // 1. Build Union-Find connections between accounts sharing an email
        for (int i = 0; i < n; i++) {
            for (int j = 1; j < accounts.get(i).size(); j++) {
                String email = accounts.get(i).get(j);
                if (emailToAccId.containsKey(email)) {
                    ds.union(i, emailToAccId.get(email));
                } else {
                    emailToAccId.put(email, i);
                }
            }
        }

        // 2. Group emails by merged Account Root ID
        Map<Integer, List<String>> mergedAccounts = new HashMap<>();
        for (Map.Entry<String, Integer> entry : emailToAccId.entrySet()) {
            String email = entry.getKey();
            int rootAccId = ds.find(entry.getValue());
            mergedAccounts.computeIfAbsent(rootAccId, k -> new ArrayList<>()).add(email);
        }

        // 3. Format final result with sorted emails
        List<List<String>> result = new ArrayList<>();
        for (Map.Entry<Integer, List<String>> entry : mergedAccounts.entrySet()) {
            List<String> emails = entry.getValue();
            Collections.sort(emails);
            List<String> account = new ArrayList<>();
            account.add(accounts.get(entry.getKey()).get(0)); // Owner name
            account.addAll(emails);
            result.add(account);
        }

        return result;
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
        public void union(int i, int j) {
            int rootI = find(i);
            int rootJ = find(j);
            if (rootI != rootJ) parent[rootI] = rootJ;
        }
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(N \cdot K \log(N \cdot K))$ where $N$ is number of accounts and $K$ is max emails per account (due to sorting emails).
- **Space Complexity:** $O(N \cdot K)$ for HashMaps and DSU.

---

## Pattern Summary

| Problem | Primary Requirement | Math / Modeling Framework |
| :--- | :--- | :--- |
| **Connected Components Classic** | Count isolated subgraphs | Decrement count on valid `union(u, v)` |
| **LC 547 (Provinces)** | Adjacency matrix component count | Standard unvisited DFS iteration |
| **LC 2316 (Unreachable Pairs)**| Pairwise combinations across components | $\sum \text{Size}_i \times (\text{Remaining} - \text{Size}_i)$ |
| **LC 721 (Accounts Merge)** | Attribute-based relationship union | Union accounts sharing common email IDs |
