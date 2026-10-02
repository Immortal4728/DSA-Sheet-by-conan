# Pattern 08 — Union-Find & Dynamic Connectivity

Disjoint Set Union (DSU) / Union-Find maintains a partition of a set into disjoint connected components. With Path Compression and Union by Rank/Size, operation complexity is nearly $O(1)$ amortized ($O(\alpha(N))$).

Beyond standard component counting, DSU excels at dynamic connectivity over:
1. **Shared Coordinates**: Connecting nodes sharing row/column properties.
2. **Network Connectivity**: Redundant edge tracking.
3. **Equality & Inequality Constraints**: Validating system equivalence relations.

> [!NOTE]
> **Technique Reference**: <a href="https://leetcode.com/problems/redundant-connection/" target="_blank">LeetCode 684 (Redundant Connection)</a> is a baseline reference for detecting the exact edge that creates a cycle in an undirected graph via DSU (`find(u) == find(v)`).

---

## Pattern Questions (3 Canonical)

### 1. Most Stones Removed with Same Row or Column — <a href="https://leetcode.com/problems/most-stones-removed-with-same-row-or-column/" target="_blank">LeetCode 947</a>

#### Problem Statement
On a 2D plane, we place `n` stones at integer coordinates `stones[i] = [r_i, c_i]`. A stone can be removed if it shares either the same row or the same column as another stone that has not been removed. Return the maximum possible number of stones that can be removed.

#### Shared-Coordinate DSU Mechanics
- Two stones sharing a row or column belong to the same connected component.
- In any connected component of size $S$, we can sequentially remove $S - 1$ stones (leaving 1 root stone behind).
$$\text{Max Stones Removed} = \text{Total Stones } N - \text{Number of Connected Components}$$

#### Fast Coordinate Offset Technique
Union row index `r` with column index `c`. To prevent coordinate collisions between row 0 and column 0, offset columns: `c + 10001`.

#### Java Implementation
```java
import java.util.HashSet;
import java.util.Set;

public class MostStonesRemoved {
    public int removeStones(int[][] stones) {
        DisjointSet ds = new DisjointSet();

        for (int[] stone : stones) {
            // Offset column coordinate by 10001 to distinguish from row index
            ds.union(stone[0], stone[1] + 10001);
        }

        Set<Integer> uniqueComponents = new HashSet<>();
        for (int[] stone : stones) {
            uniqueComponents.add(ds.find(stone[0]));
        }

        return stones.length - uniqueComponents.size();
    }

    private static class DisjointSet {
        private int[] parent = new int[20005];

        public DisjointSet() {
            for (int i = 0; i < parent.length; i++) parent[i] = i;
        }

        public int find(int i) {
            if (parent[i] == i) return i;
            return parent[i] = find(parent[i]);
        }

        public void union(int i, int j) {
            int rootI = find(i);
            int rootJ = find(j);
            if (rootI != rootJ) {
                parent[rootI] = rootJ;
            }
        }
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(N \cdot \alpha(N))$ — Operations take near-constant time.
- **Space Complexity:** $O(\text{Max Coordinates})$ — Array space for parent mapping.

---

### 2. Number of Operations to Make Network Connected — <a href="https://leetcode.com/problems/number-of-operations-to-make-network-connected/" target="_blank">LeetCode 1319</a>

#### Problem Statement
There are `n` computers numbered `0` to `n - 1` and connections `connections[i] = [a, b]`. You can extract redundant cables and reconnect them. Return minimum operations to make all computers connected. If impossible, return `-1`.

#### Key Insight & Redundant Cable Counting
- To connect $C$ components into 1 single network, we need at least $C - 1$ extra cables.
- Total cables must be $\ge n - 1$.
- Each time `union(u, v)` returns `false` (meaning `u` and `v` are already in the same component), that edge is a **redundant cable**.

#### Java Implementation
```java
public class MakeNetworkConnected {
    public int makeConnected(int n, int[][] connections) {
        if (connections.length < n - 1) return -1; // Insufficient cables

        DisjointSet ds = new DisjointSet(n);
        int components = n;

        for (int[] conn : connections) {
            if (ds.union(conn[0], conn[1])) {
                components--;
            }
        }

        return components - 1; // Need (components - 1) extra cables
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
- **Time Complexity:** $O(V + E \cdot \alpha(V))$ — DSU operations.
- **Space Complexity:** $O(V)$ — Parent array.

---

### 3. Satisfiability of Equality Equations — <a href="https://leetcode.com/problems/satisfiability-of-equality-equations/" target="_blank">LeetCode 990</a>

#### Problem Statement
Given an array of strings `equations` representing relationships like `"a==b"` or `"b!=c"`. Return `true` if it is possible to assign values to variables to satisfy all equations.

#### Two-Pass DSU Equivalence Modeling
1. **Pass 1 (Equality Union)**: Process all `"=="` equations. Union variables `var1` and `var2` into the same DSU equivalence set.
2. **Pass 2 (Inequality Verification)**: Process all `"!="` equations. If `find(var1) == find(var2)`, an impossible contradiction occurs $\Rightarrow$ return `false`.

#### Java Implementation
```java
public class SatisfiabilityOfEquations {
    public boolean equationsPossible(String[] equations) {
        DisjointSet ds = new DisjointSet(26);

        // Pass 1: Union equality sets
        for (String eq : equations) {
            if (eq.charAt(1) == '=') {
                int u = eq.charAt(0) - 'a';
                int v = eq.charAt(3) - 'a';
                ds.union(u, v);
            }
        }

        // Pass 2: Check inequality contradictions
        for (String eq : equations) {
            if (eq.charAt(1) == '!') {
                int u = eq.charAt(0) - 'a';
                int v = eq.charAt(3) - 'a';
                if (ds.find(u) == ds.find(v)) {
                    return false; // Contradiction: u and v are equal but asserted !=
                }
            }
        }

        return true;
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
- **Time Complexity:** $O(N \cdot \alpha(26)) = O(N)$ — Linear pass over equations array.
- **Space Complexity:** $O(1)$ — Fixed size array of size 26 for alphabet.

---

## Pattern Comparison Matrix

| Problem | Domain Modeling | Union Logic | Contradiction / Output Rule |
| :--- | :--- | :--- | :--- |
| **LC 947 (Stones)** | 2D Coordinates | Union `r` with `c + offset` | `stones.length - components` |
| **LC 1319 (Network)**| Graph Cable Topology | Union nodes; track components | Require `components - 1` cables |
| **LC 990 (Equations)**| Transitive Equivalence | 2-Pass: `==` union $\to$ `!=` check | Fail if `find(u) == find(v)` for `!=` |
