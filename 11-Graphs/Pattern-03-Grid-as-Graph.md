# Pattern 03 — Grid as Graph

A 2D matrix or grid can be naturally modeled as an **implicit graph** where:
- Each cell `(r, c)` is a vertex.
- Adjacent valid cells (4-directional or 8-directional) are edges with weight 1.

Breadth-First Search (BFS) is uniquely suited for unweighted grid graphs because BFS guarantees that the first time a cell is reached, it is reached via the **shortest path / minimum time**.

---

## The BFS Wavefront Mechanics

```text
Multi-Source BFS Initialization:
Queue: [(r1, c1), (r2, c2), ...]   <-- All initial sources added at Minute 0

Minute 1 Wavefront:
Push all valid unvisited neighbors of initial sources into Queue.

Minute 2 Wavefront:
Push neighbors of Minute 1 nodes...
```

---

## Pattern Questions (3 Canonical)

### 1. Rotting Oranges — <a href="https://leetcode.com/problems/rotting-oranges/" target="_blank">LeetCode 994</a>

#### Problem Statement
Given an `m x n` grid where:
- `0` is an empty cell,
- `1` is a fresh orange,
- `2` is a rotten orange.

Every minute, any fresh orange that is 4-directionally adjacent to a rotten orange becomes rotten. Return the minimum number of minutes that must elapse until no cell has a fresh orange. If this is impossible, return `-1`.

#### Key Insight & Multi-Source BFS
- All rotten oranges (`2`s) act as simultaneous starting sources.
- Add all initial rotten oranges to the BFS queue at time `0` and count total fresh oranges.
- Process the queue level-by-level (each level = 1 minute). When a fresh orange rots, decrement fresh count and push its coordinates to queue.

#### Java Implementation
```java
import java.util.ArrayDeque;
import java.util.Queue;

public class RottingOranges {
    public int orangesRotting(int[][] grid) {
        int rows = grid.length;
        int cols = grid[0].length;
        Queue<int[]> queue = new ArrayDeque<>();
        int freshCount = 0;

        // 1. Initialize queue with all rotten oranges + count fresh oranges
        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) {
                if (grid[r][c] == 2) {
                    queue.offer(new int[]{r, c});
                } else if (grid[r][c] == 1) {
                    freshCount++;
                }
            }
        }

        if (freshCount == 0) return 0; // No fresh oranges initially

        int minutes = 0;
        int[][] dirs = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};

        // 2. Level-by-level BFS
        while (!queue.isEmpty() && freshCount > 0) {
            minutes++;
            int size = queue.size();

            for (int i = 0; i < size; i++) {
                int[] curr = queue.poll();
                int r = curr[0];
                int c = curr[1];

                for (int[] d : dirs) {
                    int nr = r + d[0];
                    int nc = c + d[1];

                    if (nr >= 0 && nr < rows && nc >= 0 && nc < cols && grid[nr][nc] == 1) {
                        grid[nr][nc] = 2; // Rot the fresh orange
                        freshCount--;
                        queue.offer(new int[]{nr, nc});
                    }
                }
            }
        }

        return freshCount == 0 ? minutes : -1;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(M \times N)$ — Each grid cell is processed at most once.
- **Space Complexity:** $O(M \times N)$ — Queue size in worst case.

---

### 2. 01 Matrix — <a href="https://leetcode.com/problems/01-matrix/" target="_blank">LeetCode 542</a>

#### Problem Statement
Given an `m x n` binary matrix `mat`, return the distance of the nearest `0` for each cell. The distance between two adjacent cells is 1.

#### Multi-Source Distance Propagation
- **Reverse Thinking**: Instead of starting BFS from each `1` (which would take $O((M \times N)^2)$ time), start Multi-Source BFS from **all `0` cells simultaneously** with distance 0!
- Propagate distances outward to neighboring `1` cells.

#### Java Implementation
```java
import java.util.ArrayDeque;
import java.util.Queue;

public class Matrix01 {
    public int[][] updateMatrix(int[][] mat) {
        int rows = mat.length;
        int cols = mat[0].length;
        int[][] dist = new int[rows][cols];
        Queue<int[]> queue = new ArrayDeque<>();

        // Initialize queue with all 0-cells and set 1-cells to infinity
        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) {
                if (mat[r][c] == 0) {
                    dist[r][c] = 0;
                    queue.offer(new int[]{r, c});
                } else {
                    dist[r][c] = Integer.MAX_VALUE;
                }
            }
        }

        int[][] dirs = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};

        while (!queue.isEmpty()) {
            int[] curr = queue.poll();
            int r = curr[0];
            int c = curr[1];

            for (int[] d : dirs) {
                int nr = r + d[0];
                int nc = c + d[1];

                if (nr >= 0 && nr < rows && nc >= 0 && nc < cols) {
                    // If a shorter distance path is found
                    if (dist[nr][nc] > dist[r][c] + 1) {
                        dist[nr][nc] = dist[r][c] + 1;
                        queue.offer(new int[]{nr, nc});
                    }
                }
            }
        }

        return dist;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(M \times N)$ — Single linear pass for initialization and BFS wavefront.
- **Space Complexity:** $O(M \times N)$ — Distance matrix and queue storage.

---

### 3. Shortest Path in Binary Matrix — <a href="https://leetcode.com/problems/shortest-path-in-binary-matrix/" target="_blank">LeetCode 1091</a>

#### Problem Statement
Given an `n x n` binary matrix `grid`, return the length of the shortest clear path from top-left `(0, 0)` to bottom-right `(n-1, n-1)`. All visited cells must be `0`. You may move in **8 directions**.

#### Key Insight & 8-Directional Single-Source BFS
- Single-source shortest path on unweighted graph $\Rightarrow$ BFS.
- **8 Directions**: Include diagonal movements:
  `(±1, 0), (0, ±1), (±1, ±1)`.

#### Java Implementation
```java
import java.util.ArrayDeque;
import java.util.Queue;

public class ShortestPathBinaryMatrix {
    public int shortestPathBinaryMatrix(int[][] grid) {
        int n = grid.length;
        if (grid[0][0] != 0 || grid[n - 1][n - 1] != 0) return -1;
        if (n == 1) return 1;

        int[][] dirs = {
            {-1, -1}, {-1, 0}, {-1, 1},
            { 0, -1},          { 0, 1},
            { 1, -1}, { 1, 0}, { 1, 1}
        };

        Queue<int[]> queue = new ArrayDeque<>();
        queue.offer(new int[]{0, 0, 1}); // {row, col, pathLength}
        grid[0][0] = 1; // Mark visited in-place

        while (!queue.isEmpty()) {
            int[] curr = queue.poll();
            int r = curr[0];
            int c = curr[1];
            int len = curr[2];

            if (r == n - 1 && c == n - 1) return len;

            for (int[] d : dirs) {
                int nr = r + d[0];
                int nc = c + d[1];

                if (nr >= 0 && nr < n && nc >= 0 && nc < n && grid[nr][nc] == 0) {
                    grid[nr][nc] = 1; // Mark visited
                    queue.offer(new int[]{nr, nc, len + 1});
                }
            }
        }

        return -1;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(N^2)$ — Visits each grid cell at most once.
- **Space Complexity:** $O(N^2)$ — BFS queue capacity.

---

## Pattern Summary & BFS Wavefront Progression

| Problem | BFS Source Type | Directionality | Goal / Output |
| :--- | :--- | :--- | :--- |
| **LC 994 (Rotting Oranges)** | Multi-Source (All initial `2`s) | 4-directional | Minimum time to cover all cells |
| **LC 542 (01 Matrix)** | Multi-Source (All initial `0`s) | 4-directional | Distance array from nearest source |
| **LC 1091 (Binary Matrix)**| Single-Source `(0, 0)` | 8-directional | Shortest path to `(N-1, N-1)` |
