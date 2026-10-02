# Pattern 06 — Interval & Partition DP

Interval DP operates on subsegments (substrings, subarrays, or subtrees) defined by a starting index `i` and an ending index `j`. Instead of growing a sequence from left to right (like 1D or Sequence DP), Interval DP shrinks or expands intervals by making a split or choosing a final operation within `[i, j]`.

---

## Core Concept & Mental Model

In standard 1D DP, `dp[i]` depends on `dp[i-1]` or previous elements. In Interval DP, `dp[i][j]` represents the answer for the subproblem on the range `nums[i...j]`.

To compute `dp[i][j]`, we iterate over all possible partition/split points `k` where `i <= k < j` (or `i < k < j` depending on problem boundary definitions):

```text
choose split / final operation (k)
        ↓
left interval [i, k]
        +
right interval [k + 1, j]
        +
current operation cost
```

### Evaluation Order
Because `dp[i][j]` depends on smaller sub-intervals `[i, k]` and `[k+1, j]`, the DP table must be filled in order of **increasing interval length**:
1. Base cases: Intervals of length 1 or length 2 (`j - i = 0` or `j - i = 1`).
2. Outer loop: `len` from 2 to `N`.
3. Inner loop: Start index `i` from `0` to `N - len`. End index `j = i + len - 1`.
4. Partition loop: `k` from `i` to `j - 1`.

---

## Pattern Questions (3 Canonical)

### 1. Matrix Chain Multiplication — Classic Interview Problem

#### Problem Statement
Given a sequence of matrices, find the most efficient way to multiply these matrices together. The problem is not actually to perform the multiplications, but merely to decide in which order to perform the multiplications to minimize scalar operations.

Given dimensions array `p` where matrix `i` has dimensions `p[i-1] x p[i]`.

#### Key Insight & State Definition
- Multiplications are associative: `(A * B) * C = A * (B * C)`.
- Any matrix multiplication sequence splits the chain `[i...j]` into two sub-chains `[i...k]` and `[k+1...j]` as the final operation.
- **State**: `dp[i][j]` = minimum scalar multiplications needed to compute matrix product `A[i]...A[j]`.
- **Transition**: For `k` from `i` to `j-1`:
  $$\text{cost} = \text{dp}[i][k] + \text{dp}[k+1][j] + p[i-1] \times p[k] \times p[j]$$
  $$\text{dp}[i][j] = \min_{i \le k < j} (\text{cost})$$

#### Java Implementation
```java
public class MatrixChainMultiplication {
    public static int matrixMultiplication(int[] p) {
        int n = p.length;
        // dp[i][j] represents min cost for matrices i through j (1-indexed)
        int[][] dp = new int[n][n];

        // Length of chain
        for (int len = 2; len < n; len++) {
            for (int i = 1; i < n - len + 1; i++) {
                int j = i + len - 1;
                dp[i][j] = Integer.MAX_VALUE;
                for (int k = i; k < j; k++) {
                    int cost = dp[i][k] + dp[k + 1][j] + p[i - 1] * p[k] * p[j];
                    dp[i][j] = Math.min(dp[i][j], cost);
                }
            }
        }
        return dp[1][n - 1];
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(N^3)$ — 3 nested loops (length, start index, partition point $k$).
- **Space Complexity:** $O(N^2)$ — 2D DP table.

---

### 2. Minimum Cost to Cut a Stick — <a href="https://leetcode.com/problems/minimum-cost-to-cut-a-stick/" target="_blank">LeetCode 1547</a>

#### Problem Statement
Given a wooden stick of length `n` and an array `cuts` where `cuts[i]` represents a position you need to perform a cut at. The cost of a cut is the length of the stick segment being cut. Return the minimum total cost of all cuts.

#### Key Insight & State Definition
- Sorting `cuts` allows treating boundaries as discrete cut points. Add `0` at the start and `n` at the end of `cuts`.
- **Reverse Thinking**: Instead of choosing which cut to make first, consider which cut is performed **last** within interval `[i, j]`.
- Performing cut `k` last splits the interval into `[i, k]` and `[k, j]`, costing `cuts[j] - cuts[i]`.
- **State**: `dp[i][j]` = minimum cost to perform all cuts strictly inside index interval `cuts[i]` to `cuts[j]`.
- **Transition**:
  $$\text{dp}[i][j] = \min_{i < k < j} \left( \text{dp}[i][k] + \text{dp}[k][j] + (\text{cuts}[j] - \text{cuts}[i]) \right)$$

#### Java Implementation
```java
import java.util.Arrays;

public class MinimumCostToCutStick {
    public int minCost(int n, int[] cuts) {
        int m = cuts.length;
        int[] sortedCuts = new int[m + 2];
        System.arraycopy(cuts, 0, sortedCuts, 1, m);
        sortedCuts[0] = 0;
        sortedCuts[m + 1] = n;
        Arrays.sort(sortedCuts);

        int sz = sortedCuts.length;
        int[][] dp = new int[sz][sz];

        for (int len = 2; len < sz; len++) {
            for (int i = 0; i < sz - len; i++) {
                int j = i + len;
                if (len == 2) {
                    dp[i][j] = 0; // No cuts inside (i, j)
                    continue;
                }
                dp[i][j] = Integer.MAX_VALUE;
                for (int k = i + 1; k < j; k++) {
                    int cost = dp[i][k] + dp[k][j] + (sortedCuts[j] - sortedCuts[i]);
                    dp[i][j] = Math.min(dp[i][j], cost);
                }
            }
        }
        return dp[0][sz - 1];
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(M^3)$ where $M$ is the number of cuts.
- **Space Complexity:** $O(M^2)$ for DP storage.

---

### 3. Burst Balloons — <a href="https://leetcode.com/problems/burst-balloons/" target="_blank">LeetCode 312</a>

#### Problem Statement
Given `n` balloons indexed `0` to `n-1`, each with a number of coins represented by `nums[i]`. If you burst balloon `i`, you get `nums[i-1] * nums[i] * nums[i+1]` coins. If `i-1` or `i+1` goes out of bounds, treat it as a balloon with 1 coin. Return the maximum coins you can collect.

#### Key Insight & Reverse-State Mechanics
- **Why Top-Down / Forward popping fails**: Popping balloon `k` first changes the adjacent balloons for `k-1` and `k+1`, causing subproblems to depend on future choices (lacks optimal substructure).
- **Reverse Thinking**: Think about which balloon `k` is burst **LAST** in the range `(i, j)`.
- If balloon `k` is burst last in `(i, j)`, then balloon `i` and balloon `j` are still present when `k` is burst!
- Coins earned by bursting `k` last: `nums[i] * nums[k] * nums[j]`.
- Left subproblem: burst all balloons between `i` and `k`.
- Right subproblem: burst all balloons between `k` and `j`.
- **State**: `dp[i][j]` = max coins from bursting all balloons strictly between index `i` and `j`.

#### Java Implementation
```java
public class BurstBalloons {
    public int maxCoins(int[] nums) {
        int n = nums.length;
        // Pad array with boundary 1s
        int[] val = new int[n + 2];
        val[0] = 1;
        val[n + 1] = 1;
        System.arraycopy(nums, 0, val, 1, n);

        int sz = val.length;
        int[][] dp = new int[sz][sz];

        // Length between boundary i and j (from 2 up to sz - 1)
        for (int len = 2; len < sz; len++) {
            for (int i = 0; i < sz - len; i++) {
                int j = i + len;
                for (int k = i + 1; k < j; k++) {
                    int coins = val[i] * val[k] * val[j] + dp[i][k] + dp[k][j];
                    dp[i][j] = Math.max(dp[i][j], coins);
                }
            }
        }
        return dp[0][sz - 1];
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(N^3)$ — 3 nested loops over array boundaries and last-burst selection point $k$.
- **Space Complexity:** $O(N^2)$ — 2D DP matrix.

---

## Pattern Summary & Implementation Template

| Problem | Range `[i, j]` Definition | Split Variable `k` Meaning | Transition Formula |
| :--- | :--- | :--- | :--- |
| **MCM** | Matrices $i$ through $j$ | Final multiplication split | $\text{dp}[i][k] + \text{dp}[k+1][j] + p_{i-1}p_k p_j$ |
| **Cut Stick** | Cut boundaries `cuts[i]` to `cuts[j]` | Cut executed last in range | $\text{dp}[i][k] + \text{dp}[k][j] + (\text{cuts}[j] - \text{cuts}[i])$ |
| **Burst Balloons**| Balloons strictly between $i$ and $j$ | Balloon burst last in range | $\text{val}[i]\text{val}[k]\text{val}[j] + \text{dp}[i][k] + \text{dp}[k][j]$ |
