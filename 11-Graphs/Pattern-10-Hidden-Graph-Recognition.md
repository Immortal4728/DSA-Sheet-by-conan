# Pattern 10 — Hidden Graph Recognition

In technical coding interviews, graph problems are frequently disguised as Array manipulation, Mathematical Equations, or Combination State puzzles. Recognizing that an unlabeled problem is secretly a Graph problem is the single most critical step toward solving it.

---

## The Hidden Graph Recognition Formula

Whenever an unfamiliar problem statement contains these 4 elements, it can be modeled as a Graph:

```text
1. States       ──►   Variables, Array Indices, Numbers, or String Combinations (Nodes V)
2. Transitions  ──►   Valid Operations, Jump Steps, Multiplications, or Turns (Edges E)
3. Neighbors    ──►   All Next-States reachable in 1 valid move
4. Visited      ──►   Set/Array preventing infinite loops across identical states

  States + Transitions + Neighbors + Visited  =  GRAPH
```

---

## Disguise Categories & Canonical Questions (3 Canonical)

### 1. Jump Game III — <a href="https://leetcode.com/problems/jump-game-iii/" target="_blank">LeetCode 1306</a>

#### Initial Surface Impression
Appears to be an Array recursion or Greedy jump problem.

#### Why Greedy Fails
From index `i`, you can jump to `i + arr[i]` or `i - arr[i]`. Choosing one direction greedily might land in a dead end or infinite cycle.

#### Graph Recognition & Formulation
- **Nodes**: Array indices `0` through `arr.length - 1`.
- **Edges**: Directed edges from index `i` to `i + arr[i]` and `i - arr[i]`.
- **Goal**: Find if any node with `arr[node] == 0` is reachable from `start`. (Standard BFS/DFS Reachability).

#### Java Implementation
```java
import java.util.ArrayDeque;
import java.util.Queue;

public class JumpGameIII {
    public boolean canReach(int[] arr, int start) {
        int n = arr.length;
        Queue<Integer> queue = new ArrayDeque<>();
        boolean[] visited = new boolean[n];

        queue.offer(start);
        visited[start] = true;

        while (!queue.isEmpty()) {
            int curr = queue.poll();
            if (arr[curr] == 0) return true;

            // Generate neighbor transitions
            int[] nextIndices = {curr + arr[curr], curr - arr[curr]};
            for (int next : nextIndices) {
                if (next >= 0 && next < n && !visited[next]) {
                    visited[next] = true;
                    queue.offer(next);
                }
            }
        }

        return false;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(N)$ — Each index is visited at most once.
- **Space Complexity:** $O(N)$ — BFS queue and visited array.

---

### 2. Evaluate Division — <a href="https://leetcode.com/problems/evaluate-division/" target="_blank">LeetCode 399</a>

#### Initial Surface Impression
Appears to be an Algebraic Equation solver or String HashMap lookup problem.

#### Graph Recognition & Formulation
Given equations $\frac{A}{B} = 2.0$:
- **Nodes**: Variable strings (`"A"`, `"B"`).
- **Directed Weighted Edges**:
  - Edge `"A" -> "B"` with weight $2.0$.
  - Edge `"B" -> "A"` with reciprocal weight $\frac{1}{2.0} = 0.5$.
- **Goal**: Evaluating query $\frac{X}{Y}$ is equivalent to finding a path from $X$ to $Y$ and computing the product of edge weights along the path!

#### Java Implementation (Weighted DFS Path Product)
```java
import java.util.*;

public class EvaluateDivision {
    public double[] calcEquation(List<List<String>> equations, double[] values, List<List<String>> queries) {
        Map<String, Map<String, Double>> graph = new HashMap<>();

        // 1. Build directed weighted graph
        for (int i = 0; i < equations.size(); i++) {
            String u = equations.get(i).get(0);
            String v = equations.get(i).get(1);
            double val = values[i];

            graph.computeIfAbsent(u, k -> new HashMap<>()).put(v, val);
            graph.computeIfAbsent(v, k -> new HashMap<>()).put(u, 1.0 / val);
        }

        // 2. Process queries using DFS
        double[] results = new double[queries.size()];
        for (int i = 0; i < queries.size(); i++) {
            String src = queries.get(i).get(0);
            String dst = queries.get(i).get(1);

            if (!graph.containsKey(src) || !graph.containsKey(dst)) {
                results[i] = -1.0;
            } else if (src.equals(dst)) {
                results[i] = 1.0;
            } else {
                Set<String> visited = new HashSet<>();
                results[i] = dfs(src, dst, 1.0, graph, visited);
            }
        }

        return results;
    }

    private double dfs(String curr, String target, double product, Map<String, Map<String, Double>> graph, Set<String> visited) {
        visited.add(curr);
        if (curr.equals(target)) return product;

        Map<String, Double> neighbors = graph.get(curr);
        for (Map.Entry<String, Double> entry : neighbors.entrySet()) {
            String neighbor = entry.getKey();
            double weight = entry.getValue();

            if (!visited.contains(neighbor)) {
                double result = dfs(neighbor, target, product * weight, graph, visited);
                if (result != -1.0) return result;
            }
        }

        return -1.0;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(Q \times (V + E))$ — Runs DFS traversal per query.
- **Space Complexity:** $O(V + E)$ — HashMap representation of variable graph.

---

### 3. Open the Lock — <a href="https://leetcode.com/problems/open-the-lock/" target="_blank">LeetCode 752</a>

#### Initial Surface Impression
Appears to be a Combination lock String puzzle or Backtracking state problem.

#### Graph Recognition & Formulation
- **Nodes**: 4-digit lock strings (10,000 total states from `"0000"` to `"9999"`).
- **Edges**: Unweighted edge between two lock states if they differ by 1 wheel rotation (+1 or -1). (8 neighbors per state).
- **Deadends**: Invalid nodes that cannot be traversed.
- **Goal**: Minimum wheel turns to reach `target` starting from `"0000"` $\Rightarrow$ **Unweighted Single-Source BFS**.

#### Java Implementation
```java
import java.util.*;

public class OpenTheLock {
    public int openLock(String[] deadends, String target) {
        Set<String> dead = new HashSet<>(Arrays.asList(deadends));
        if (dead.contains("0000")) return -1;
        if ("0000".equals(target)) return 0;

        Queue<String> queue = new ArrayDeque<>();
        Set<String> visited = new HashSet<>();

        queue.offer("0000");
        visited.add("0000");
        int turns = 0;

        while (!queue.isEmpty()) {
            turns++;
            int size = queue.size();

            for (int i = 0; i < size; i++) {
                String curr = queue.poll();

                for (String neighbor : getNeighbors(curr)) {
                    if (neighbor.equals(target)) return turns;

                    if (!dead.contains(neighbor) && !visited.contains(neighbor)) {
                        visited.add(neighbor);
                        queue.offer(neighbor);
                    }
                }
            }
        }

        return -1;
    }

    private List<String> getNeighbors(String code) {
        List<String> neighbors = new ArrayList<>();
        char[] chars = code.toCharArray();

        for (int i = 0; i < 4; i++) {
            char orig = chars[i];

            // Turn wheel forward (+1)
            chars[i] = orig == '9' ? '0' : (char)(orig + 1);
            neighbors.add(new String(chars));

            // Turn wheel backward (-1)
            chars[i] = orig == '0' ? '9' : (char)(orig - 1);
            neighbors.add(new String(chars));

            chars[i] = orig; // Restore
        }
        return neighbors;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(N \times 10^D)$ where $D=4$ digits ($10^4 = 10,000$ total states), max 8 neighbors per state.
- **Space Complexity:** $O(10^D) = O(10,000)$ — Queue and HashSet size bounded by 10,000 states.

---

## Hidden Graph Disguise Decoder

| Problem | Surface Disguise | Node Definition | Edge Definition | Algorithm |
| :--- | :--- | :--- | :--- | :--- |
| **LC 1306 (Jump Game III)**| Array Jumps | Array Index `i` | $i \to i \pm \text{arr}[i]$ | Simple BFS / DFS Reachability |
| **LC 399 (Eval Division)**| Algebraic Equations| Variable Strings | Directed Edge with Weight $W = a/b$ | Path Product DFS |
| **LC 752 (Open Lock)** | Combination Lock | 4-Digit String | 1 Wheel Turn ($\pm 1$) | Unweighted Level BFS |
