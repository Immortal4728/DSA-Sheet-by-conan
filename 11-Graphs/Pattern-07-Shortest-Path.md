# Pattern 07 — Shortest Path

Finding the shortest path in a graph depends heavily on graph properties (weighted vs unweighted), edge cost functions, and auxiliary state constraints (such as stop limits or obstacle removal quotas).

---

## The Shortest Path Algorithm Selector

```text
                                  Graph Type & Constraints
                                             │
               ┌─────────────────────────────┴─────────────────────────────┐
               ▼                                                           ▼
       Unweighted Graph                                             Weighted Graph
  (Edge weights all equal 1)                                   (Non-negative edge weights)
               │                                                           │
               ▼                                                           ▼
     Standard Level BFS                                           Dijkstra's Algorithm
          O(V + E)                                                  O(E log V)
               │                                                           │
               ▼                                                           ▼
  Examples: LC 127 (Word Ladder)                             Examples: LC 743 (Network Delay)
                                                                       LC 1631 (Min Effort)

               ┌───────────────────────────────────────────────────────────┐
               ▼                                                           ▼
  Bounded / State Constraints                                    Bellman-Ford / BFS State
  (e.g., At most K stops / K obstacles)                         (State: (node, remaining_k))
  Examples: LC 787 (Flights), LC 1293 (Obstacles)
```

> [!CAUTION]
> **Common Fallacy**: Do not assume every weighted graph problem uses vanilla Dijkstra! When state constraints exist (e.g., at most $K$ stops), Dijkstra's greedy priority queue can prune optimal paths prematurely unless the state space is augmented: `dist[node][state]`.

---

## Pattern Questions (5 Canonical)

### 1. Network Delay Time — <a href="https://leetcode.com/problems/network-delay-time/" target="_blank">LeetCode 743</a>

#### Problem Statement
Given `times[i] = [u_i, v_i, w_i]` representing directed weighted edges, send a signal from source node `k`. Return the minimum time for all `n` nodes to receive the signal. If impossible, return `-1`.

#### Key Insight & Standard Dijkstra
This is canonical Single-Source Shortest Path (SSSP) on a positive weighted graph. Use Dijkstra's Algorithm with a Min-Heap (`PriorityQueue`).

#### Java Implementation
```java
import java.util.*;

public class NetworkDelayTime {
    public int networkDelayTime(int[][] times, int n, int k) {
        List<List<int[]>> adj = new ArrayList<>();
        for (int i = 0; i <= n; i++) adj.add(new ArrayList<>());
        for (int[] time : times) {
            adj.get(time[0]).add(new int[]{time[1], time[2]}); // {neighbor, weight}
        }

        int[] dist = new int[n + 1];
        Arrays.fill(dist, Integer.MAX_VALUE);
        dist[k] = 0;

        // Min-Heap ordered by current shortest distance
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> Integer.compare(a[1], b[1]));
        pq.offer(new int[]{k, 0});

        while (!pq.isEmpty()) {
            int[] curr = pq.poll();
            int u = curr[0];
            int d = curr[1];

            if (d > dist[u]) continue; // Stale priority queue entry

            for (int[] edge : adj.get(u)) {
                int v = edge[0];
                int w = edge[1];

                if (dist[u] + w < dist[v]) {
                    dist[v] = dist[u] + w;
                    pq.offer(new int[]{v, dist[v]});
                }
            }
        }

        int maxDelay = 0;
        for (int i = 1; i <= n; i++) {
            if (dist[i] == Integer.MAX_VALUE) return -1;
            maxDelay = Math.max(maxDelay, dist[i]);
        }

        return maxDelay;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(E \log V)$ — Each edge insertion and extraction takes logarithmic time.
- **Space Complexity:** $O(V + E)$ — Adjacency list and distance array.

---

### 2. Cheapest Flights Within K Stops — <a href="https://leetcode.com/problems/cheapest-flights-within-k-stops/" target="_blank">LeetCode 787</a>

#### Problem Statement
Given `n` cities connected by `flights[i] = [from, to, price]`, return the cheapest price from `src` to `dst` with at most `k` stops.

#### State-Constrained BFS / Bellman-Ford Strategy
Standard Dijkstra fails because a cheaper path with 5 stops might overwrite a slightly more expensive path with 1 stop.
**Solution**: Use Bellman-Ford relaxation for exactly $k+1$ iterations (or BFS level-by-level up to step $k+1$).

#### Java Implementation (Bellman-Ford / BFS Level Relaxation)
```java
import java.util.Arrays;

public class CheapestFlights {
    public int findCheapestPrice(int n, int[][] flights, int src, int dst, int k) {
        int[] dist = new int[n];
        Arrays.fill(dist, Integer.MAX_VALUE);
        dist[src] = 0;

        // Relax edges at most k + 1 times
        for (int i = 0; i <= k; i++) {
            int[] tempDist = Arrays.copyOf(dist, n);
            for (int[] flight : flights) {
                int u = flight[0];
                int v = flight[1];
                int price = flight[2];

                if (dist[u] != Integer.MAX_VALUE) {
                    if (dist[u] + price < tempDist[v]) {
                        tempDist[v] = dist[u] + price;
                    }
                }
            }
            dist = tempDist;
        }

        return dist[dst] == Integer.MAX_VALUE ? -1 : dist[dst];
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(K \times E)$ — Relaxes all edges $K+1$ times.
- **Space Complexity:** $O(V)$ — Distance arrays.

---

### 3. Path With Minimum Effort — <a href="https://leetcode.com/problems/path-with-minimum-effort/" target="_blank">LeetCode 1631</a>

#### Problem Statement
A route's effort is the **maximum absolute difference** in height between two consecutive cells of the route. Return the minimum effort required to travel from `(0, 0)` to `(rows-1, cols-1)`.

#### Minimax Dijkstra Formulation
Instead of summing edge weights, the path cost is defined as $\max(\text{effort so far}, |\text{height}_1 - \text{height}_2|)$.
Dijkstra's Min-Heap naturally finds the path minimizing this bottleneck cost.

#### Java Implementation
```java
import java.util.Arrays;
import java.util.PriorityQueue;

public class PathWithMinimumEffort {
    public int minimumEffortPath(int[][] heights) {
        int rows = heights.length;
        int cols = heights[0].length;
        int[][] effort = new int[rows][cols];
        for (int[] row : effort) Arrays.fill(row, Integer.MAX_VALUE);

        effort[0][0] = 0;
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> Integer.compare(a[2], b[2]));
        pq.offer(new int[]{0, 0, 0}); // {row, col, maxEffort}

        int[][] dirs = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};

        while (!pq.isEmpty()) {
            int[] curr = pq.poll();
            int r = curr[0];
            int c = curr[1];
            int e = curr[2];

            if (r == rows - 1 && c == cols - 1) return e;
            if (e > effort[r][c]) continue;

            for (int[] d : dirs) {
                int nr = r + d[0];
                int nc = c + d[1];

                if (nr >= 0 && nr < rows && nc >= 0 && nc < cols) {
                    int nextEffort = Math.max(e, Math.abs(heights[r][c] - heights[nr][nc]));
                    if (nextEffort < effort[nr][nc]) {
                        effort[nr][nc] = nextEffort;
                        pq.offer(new int[]{nr, nc, nextEffort});
                    }
                }
            }
        }
        return 0;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O((M \times N) \log(M \times N))$ — Priority Queue operations over grid cells.
- **Space Complexity:** $O(M \times N)$ — Effort matrix.

---

### 4. Word Ladder — <a href="https://leetcode.com/problems/word-ladder/" target="_blank">LeetCode 127</a>

#### Problem Statement
Given `beginWord`, `endWord`, and `wordList`, return the number of words in the shortest transformation sequence from `beginWord` to `endWord`. Only one letter can be changed at a time.

#### Transformation Implicit Graph & Unweighted BFS
Each word is a node. An unweighted edge exists between two words if they differ by exactly 1 character.
Unweighted Shortest Path $\Rightarrow$ **Level-by-Level BFS**.

#### Java Implementation
```java
import java.util.*;

public class WordLadder {
    public int ladderLength(String beginWord, String endWord, List<String> wordList) {
        Set<String> wordSet = new HashSet<>(wordList);
        if (!wordSet.contains(endWord)) return 0;

        Queue<String> queue = new ArrayDeque<>();
        queue.offer(beginWord);
        int level = 1;

        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                String curr = queue.poll();
                if (curr.equals(endWord)) return level;

                char[] chars = curr.toCharArray();
                for (int j = 0; j < chars.length; j++) {
                    char orig = chars[j];
                    for (char c = 'a'; c <= 'z'; c++) {
                        if (c == orig) continue;
                        chars[j] = c;
                        String nextWord = new String(chars);

                        if (wordSet.contains(nextWord)) {
                            wordSet.remove(nextWord); // Fast visited removal
                            queue.offer(nextWord);
                        }
                    }
                    chars[j] = orig;
                }
            }
            level++;
        }
        return 0;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(N \times L^2)$ where $N$ is word count and $L$ is word length.
- **Space Complexity:** $O(N \times L)$ for word set and queue.

---

### 5. Shortest Path in a Grid with Obstacles Elimination — <a href="https://leetcode.com/problems/shortest-path-in-a-grid-with-obstacles-elimination/" target="_blank">LeetCode 1293</a>

#### Problem Statement
Given an `m x n` grid where `0` is empty and `1` is an obstacle, return the minimum steps to walk from `(0, 0)` to `(m-1, n-1)` given that you can eliminate at most `k` obstacles.

#### State-Augmented BFS
Because we can eliminate obstacles, a cell `(r, c)` can be visited with different remaining obstacle elimination quotas `remK`.
**State**: `visited[r][c][remK]`.

#### Java Implementation
```java
import java.util.ArrayDeque;
import java.util.Queue;

public class ShortestPathObstacles {
    public int shortestPath(int[][] grid, int k) {
        int rows = grid.length;
        int cols = grid[0].length;
        if (rows == 1 && cols == 1) return 0;

        // Optimization: Manhattan distance threshold
        if (k >= rows + cols - 2) return rows + cols - 2;

        // visited[r][c] stores max remaining k used to reach (r, c)
        int[][] visited = new int[rows][cols];
        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) visited[r][c] = -1;
        }

        Queue<int[]> queue = new ArrayDeque<>();
        queue.offer(new int[]{0, 0, k, 0}); // {r, c, remainingK, steps}
        visited[0][0] = k;

        int[][] dirs = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};

        while (!queue.isEmpty()) {
            int[] curr = queue.poll();
            int r = curr[0], c = curr[1], remK = curr[2], steps = curr[3];

            for (int[] d : dirs) {
                int nr = r + d[0];
                int nc = c + d[1];

                if (nr >= 0 && nr < rows && nc >= 0 && nc < cols) {
                    int nextK = remK - grid[nr][nc];

                    if (nextK >= 0 && nextK > visited[nr][nc]) {
                        if (nr == rows - 1 && nc == cols - 1) return steps + 1;
                        visited[nr][nc] = nextK;
                        queue.offer(new int[]{nr, nc, nextK, steps + 1});
                    }
                }
            }
        }
        return -1;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(M \times N \times K)$ — States defined by cell coordinates and obstacle quota $K$.
- **Space Complexity:** $O(M \times N \times K)$ — Visited matrix.

---

## Pattern Summary

| Problem | Algorithm | Graph Weight / Edge Property | State Definition |
| :--- | :--- | :--- | :--- |
| **LC 743 (Network Delay)** | Standard Dijkstra | Positive Edge Weights | `dist[node]` |
| **LC 787 (Cheapest Flights)**| Bellman-Ford / BFS | State bounded by $K$ stops | `dist[k_stops][node]` |
| **LC 1631 (Min Effort)** | Minimax Dijkstra | Edge cost = $\max(\text{cost}, \Delta h)$ | `effort[r][c]` |
| **LC 127 (Word Ladder)** | Unweighted BFS | All transformation edges = 1 | `level` counter |
| **LC 1293 (Obstacles)** | State-Augmented BFS | Unweighted + Max $K$ eliminations | `visited[r][c][remK]` |
