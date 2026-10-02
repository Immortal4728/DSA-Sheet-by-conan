# Pattern 02: Grid & 2D DP

> Grid DP models movement on a 2D matrix where cell `dp[i][j]` represents the optimal cost, path count, or structural size for the subgrid ending at row $i$ and column $j$.

---

## Why This Pattern Exists

When traversing a 2D grid moving only **Down** $(i+1, j)$ and **Right** $(i, j+1)$:
- Cell $(i, j)$ can ONLY be reached from **Top** $(i-1, j)$ or **Left** $(i, j-1)$.
- This strict directional movement guarantees acyclic dependencies, making 2D tabulation ideal.

```text
                  (i - 1, j) [Top Neighbor]
                      │
                      ▼
 (i, j - 1) ────────► (i, j) [Current Cell]
 [Left Neighbor]
```

### Recurrence Templates for Grid DP

1. **Path Counting (Unique Paths):**
   $$\text{dp}[i][j] = \text{dp}[i-1][j] + \text{dp}[i][j-1]$$
2. **Min Path Cost (Minimum Path Sum):**
   $$\text{dp}[i][j] = \text{grid}[i][j] + \min(\text{dp}[i-1][j], \text{dp}[i][j-1])$$
3. **Square Sub-matrix Expansion (Maximal Square):**
   $$\text{dp}[i][j] = 1 + \min(\text{dp}[i-1][j], \text{dp}[i][j-1], \text{dp}[i-1][j-1])$$

---

## ☕ Standard Java Templates

### 2D Grid Tabulation with $O(N)$ Space Optimization
```java
public int minPathSum(int[][] grid) {
    int rows = grid.length;
    int cols = grid[0].length;
    int[] dp = new int[cols];
    
    dp[0] = grid[0][0];
    // Initialize first row
    for (int j = 1; j < cols; j++) dp[j] = dp[j - 1] + grid[0][j];
    
    for (int i = 1; i < rows; i++) {
        dp[0] += grid[i][0]; // Update first column of row i
        for (int j = 1; j < cols; j++) {
            dp[j] = grid[i][j] + Math.min(dp[j], dp[j - 1]); // dp[j] is top, dp[j-1] is left
        }
    }
    return dp[cols - 1];
}
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Word Search (LC 79)** | Allows moving in all 4 directions with backtracking — belongs to **Recursion & Backtracking (Module 09)**. |
| **Longest Common Subsequence** | 2D state over two string sequences — belongs to **String DP (Pattern 05)**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Medium** | 4 |
| **Total Canonical Questions** | **4** |

---

## 🎯 Question Set (4 Canonical Questions)

### Q1. Unique Paths
<a href="https://leetcode.com/problems/unique-paths/" target="_blank">LeetCode 62</a> — **Medium**

**Target Skill:** Basic 2D grid path counting.

**Core Reasoning:**
- Grid of size $M \times N$. Move from $(0,0)$ to $(M-1, N-1)$.
- Number of paths to cell $(i, j)$ = `dp[i-1][j] + dp[i][j-1]`.
- Base cases: First row and first column have only 1 path (`dp = 1`).

**Why it belongs here:** Canonical entry problem for 2D grid DP counting.

**Complexity:** Time: $O(M \cdot N)$, Space: $O(N)$ space-optimized / $O(M \cdot N)$ table.

---

### Q2. Unique Paths II
<a href="https://leetcode.com/problems/unique-paths-ii/" target="_blank">LeetCode 63</a> — **Medium**

**Target Skill:** Grid obstacle handling in 2D state transitions.

**Core Reasoning:**
- Grid contains obstacles (`obstacleGrid[i][j] == 1`).
- If cell $(i, j)$ is an obstacle, no paths can pass through it $\to$ set `dp[i][j] = 0`.
- Otherwise: `dp[i][j] = dp[i-1][j] + dp[i][j-1]`.

**Why it belongs here:** Teaches zeroing out invalid states due to grid obstacles.

**Complexity:** Time: $O(M \cdot N)$, Space: $O(N)$.

---

### Q3. Minimum Path Sum
<a href="https://leetcode.com/problems/minimum-path-sum/" target="_blank">LeetCode 64</a> — **Medium**

**Target Skill:** Minimization optimization over 2D matrix cells.

**Core Reasoning:**
- Find path from top-left to bottom-right minimizing sum of numbers along path.
- `dp[i][j] = grid[i][j] + min(dp[i-1][j], dp[i][j-1])`.

**Why it belongs here:** Teaches min-cost state accumulation on grids.

**Complexity:** Time: $O(M \cdot N)$, Space: $O(N)$.

---

### Q4. Maximal Square
<a href="https://leetcode.com/problems/maximal-square/" target="_blank">LeetCode 221</a> — **Medium**

**Target Skill:** Richer 2D structural state (`top`, `left`, `top-left` minimum extension).

**Core Reasoning:**
- Find largest square containing only `1`s in a 2D binary matrix.
- `dp[i][j]` represents side length of maximum square whose bottom-right corner is at $(i, j)$.
- If `matrix[i][j] == '1'`:
  $$\text{dp}[i][j] = 1 + \min(\text{dp}[i-1][j], \text{dp}[i][j-1], \text{dp}[i-1][j-1])$$
- Max area = $(\max(\text{dp}[i][j]))^2$.

**Why it belongs here:** Advanced 2D grid DP checking 3 surrounding sub-problems simultaneously.

**Complexity:** Time: $O(M \cdot N)$, Space: $O(M \cdot N)$ or $O(N)$.

---

## ⚡ Mastery Checklist

- [ ] Can you write `Unique Paths` using a 1D `dp[]` array of size $N$ to achieve $O(N)$ space?
- [ ] How do you handle obstacles in `Unique Paths II` during first-row and first-column initialization?
- [ ] Why does `Maximal Square` require taking the minimum of THREE neighbors (`top`, `left`, `top-left`)?
- [ ] Can you explain why moving in 4 directions requires Backtracking (Module 09), while moving in 2 directions uses DP?
