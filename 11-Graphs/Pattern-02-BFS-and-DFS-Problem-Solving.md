# Pattern 02 — BFS & DFS Problem-Solving

While Pattern 01 focused on raw graph traversal mechanics, Pattern 02 transitions into using graph exploration to solve domain problems (component exploration, area calculation, region mutation, and flood filling).

---

## Pattern Distinction: Pattern 02 vs Pattern 03

```text
Pattern 02: Component Exploration & Mutation
  • Goal: Explore connected cells, count components, mutate colors, or mark regions.
  • Primary tool: DFS / BFS to sink land or flood fill.

Pattern 03: Grid Distance & Wave Propagation
  • Goal: Find shortest path, distance to nearest target, or time to spread.
  • Primary tool: Multi-source or Single-source Level-by-Level BFS.
```

---

## Pattern Questions (4 Canonical)

### 1. Number of Islands — <a href="https://leetcode.com/problems/number-of-islands/" target="_blank">LeetCode 200</a>

#### Problem Statement
Given an `m x n` 2D binary grid `grid` which represents a map of `'1'`s (land) and `'0'`s (water), return the number of islands. An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically.

#### Key Insight & In-Place Mutation
Iterate over every cell `(r, c)`. When a `'1'` is found, increment island count and initiate a DFS/BFS traversal to sink all connected land cells by mutating `'1'` to `'0'` (or using a `visited` set).

#### Java Implementation
```java
public class NumberOfIslands {
    public int numIslands(char[][] grid) {
        if (grid == null || grid.length == 0) return 0;
        int rows = grid.length;
        int cols = grid[0].length;
        int count = 0;

        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) {
                if (grid[r][c] == '1') {
                    count++;
                    dfs(grid, r, c);
                }
            }
        }
        return count;
    }

    private void dfs(char[][] grid, int r, int c) {
        if (r < 0 || c < 0 || r >= grid.length || c >= grid[0].length || grid[r][c] != '1') {
            return;
        }

        grid[r][c] = '0'; // Sink island element (mark visited)

        dfs(grid, r + 1, c);
        dfs(grid, r - 1, c);
        dfs(grid, r, c + 1);
        dfs(grid, r, c - 1);
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(M \times N)$ — Every cell is visited at most a constant number of times.
- **Space Complexity:** $O(M \times N)$ — In worst-case (all land), recursion depth is $M \times N$.

---

### 2. Max Area of Island — <a href="https://leetcode.com/problems/max-area-of-island/" target="_blank">LeetCode 695</a>

#### Problem Statement
Given an `m x n` binary matrix `grid`. An island is a group of `1`s connected 4-directionally. Return the maximum area of an island in `grid`. If there is no island, return `0`.

#### Key Insight & Accumulative Recursive Counting
Instead of merely marking components, DFS returns the sum of cells in the component:
$$\text{area}(r, c) = 1 + \text{area}(r+1, c) + \text{area}(r-1, c) + \text{area}(r, c+1) + \text{area}(r, c-1)$$

#### Java Implementation
```java
public class MaxAreaOfIsland {
    public int maxAreaOfIsland(int[][] grid) {
        int maxArea = 0;
        int rows = grid.length;
        int cols = grid[0].length;

        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) {
                if (grid[r][c] == 1) {
                    maxArea = Math.max(maxArea, dfs(grid, r, c));
                }
            }
        }
        return maxArea;
    }

    private int dfs(int[][] grid, int r, int c) {
        if (r < 0 || c < 0 || r >= grid.length || c >= grid[0].length || grid[r][c] != 1) {
            return 0;
        }

        grid[r][c] = 0; // Mark visited

        return 1 + dfs(grid, r + 1, c) 
                 + dfs(grid, r - 1, c) 
                 + dfs(grid, r, c + 1) 
                 + dfs(grid, r, c - 1);
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(M \times N)$ — Linear traversal of grid cells.
- **Space Complexity:** $O(M \times N)$ — Recursion call stack.

---

### 3. Flood Fill — <a href="https://leetcode.com/problems/flood-fill/" target="_blank">LeetCode 733</a>

#### Problem Statement
Given an `m x n` image represented by integer matrix `image`, a starting pixel `(sr, sc)`, and a `color`. Perform a flood fill on the image starting from `(sr, sc)` by changing all connected pixels of the original color to `color`.

#### Key Insight & Early Exit Edge Case
LC 733 serves as the classic bridge into graph component recoloring.
- **Edge Case**: If `image[sr][sc] == color`, return early immediately; otherwise DFS will enter an infinite recursion loop without a separate `visited` matrix!

#### Java Implementation
```java
public class FloodFill {
    public int[][] floodFill(int[][] image, int sr, int sc, int color) {
        int origColor = image[sr][sc];
        if (origColor != color) {
            dfs(image, sr, sc, origColor, color);
        }
        return image;
    }

    private void dfs(int[][] image, int r, int c, int origColor, int newColor) {
        if (r < 0 || c < 0 || r >= image.length || c >= image[0].length || image[r][c] != origColor) {
            return;
        }

        image[r][c] = newColor;

        dfs(image, r + 1, c, origColor, newColor);
        dfs(image, r - 1, c, origColor, newColor);
        dfs(image, r, c + 1, origColor, newColor);
        dfs(image, r, c - 1, origColor, newColor);
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(M \times N)$ — Reaches all pixels in the connected component.
- **Space Complexity:** $O(M \times N)$ — Recursion stack depth.

---

### 4. Surrounded Regions — <a href="https://leetcode.com/problems/surrounded-regions/" target="_blank">LeetCode 130</a>

#### Problem Statement
Given an `m x n` matrix `board` containing `'X'` and `'O'`, capture all regions that are 4-directionally surrounded by `'X'`. A region is captured by flipping all `'O'`s into `'X'`s in that surrounded region. Any `'O'` connected to the boundary cannot be captured.

#### Reverse Exploration Technique (Boundary First)
Directly checking if an internal `'O'` is surrounded requires complex multi-directional reachability checks.
**Reverse Thinking**:
1. Any `'O'` on the boundary and any `'O'` connected to it **cannot** be captured.
2. Run DFS/BFS starting from all boundary `'O'`s and temporarily mark them as `'E'` (Escaped/Safe).
3. Scan the grid: flip remaining `'O'`s $\to$ `'X'` (surrounded), and restore `'E'`s $\to$ `'O'` (safe).

```text
Initial Board           Phase 1: Mark Boundary 'O's           Phase 2: Final Flip
X  X  X  X              X  X  X  X                           X  X  X  X
X  O  O  X    ───────►  X  O  O  X                ───────►   X  X  X  X
X  X  O  X              X  X  O  X                           X  X  X  X
X  O  X  X              X  E  X  X                           X  O  X  X
```

#### Java Implementation
```java
public class SurroundedRegions {
    public void solve(char[][] board) {
        if (board == null || board.length == 0) return;
        int rows = board.length;
        int cols = board[0].length;

        // 1. DFS from boundary 'O's
        for (int r = 0; r < rows; r++) {
            if (board[r][0] == 'O') dfs(board, r, 0);
            if (board[r][cols - 1] == 'O') dfs(board, r, cols - 1);
        }
        for (int c = 0; c < cols; c++) {
            if (board[0][c] == 'O') dfs(board, 0, c);
            if (board[rows - 1][c] == 'O') dfs(board, rows - 1, c);
        }

        // 2. Post-process matrix: 'O' -> 'X', 'E' -> 'O'
        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) {
                if (board[r][c] == 'O') board[r][c] = 'X';
                else if (board[r][c] == 'E') board[r][c] = 'O';
            }
        }
    }

    private void dfs(char[][] board, int r, int c) {
        if (r < 0 || c < 0 || r >= board.length || c >= board[0].length || board[r][c] != 'O') {
            return;
        }

        board[r][c] = 'E'; // Mark escaped boundary component

        dfs(board, r + 1, c);
        dfs(board, r - 1, c);
        dfs(board, r, c + 1);
        dfs(board, r, c - 1);
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(M \times N)$ — Linear scans and single-pass boundary exploration.
- **Space Complexity:** $O(M \times N)$ — Recursion call stack.

---

## Pattern Summary

| Problem | Primary Traversal Purpose | Key Trick / Mechanism |
| :--- | :--- | :--- |
| **LC 200 (Islands)** | Count disconnected land components | Sink land `'1' -> '0'` during DFS |
| **LC 695 (Max Area)** | Return sum size of component | $1 + \text{sum}(\text{neighbors})$ |
| **LC 733 (Flood Fill)** | Color replacement in component | Early exit if `origColor == newColor` |
| **LC 130 (Surrounded)** | Capture enclosed internal regions | Boundary-first DFS $\to$ mark safe cells `'E'` |
