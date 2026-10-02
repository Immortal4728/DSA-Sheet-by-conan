# Pattern 08 — DP with Optimization & Counting

In standard Dynamic Programming, subproblems usually ask for an **extreme value** (minimum cost, maximum length, feasibility). However, advanced DP problems frequently require **dual-state tracking** (e.g., tracking both the optimal value AND the total count of paths/sequences achieving that optimum) or **generalizing $K$-dimensional state parameters**.

---

## Core Concept & Dual-State Tracking

### Dual-State Mechanics
When a problem asks for the count of optimal solutions:
- `len[i]` = length/cost of the optimal solution ending at index `i`.
- `count[i]` = number of optimal solutions ending at index `i`.

During state transition from `j` to `i`:
1. **If finding a strictly better solution** (`len[j] + 1 > len[i]`):
   - Reset optimal value: `len[i] = len[j] + 1`
   - Inherit count: `count[i] = count[j]`
2. **If finding an equal solution** (`len[j] + 1 == len[i]`):
   - Keep optimal value unchanged
   - Accumulate count: `count[i] += count[j]`

---

## Pattern Questions

### 1. Number of Longest Increasing Subsequence — <a href="https://leetcode.com/problems/number-of-longest-increasing-subsequence/" target="_blank">LeetCode 673</a> [CORE]

#### Problem Statement
Given an integer array `nums`, return the number of longest increasing subsequences.

#### Key Insight & Dual-State Formulation
Standard LIS (<a href="https://leetcode.com/problems/longest-increasing-subsequence/" target="_blank">LeetCode 300</a>) tracks `dp[i]` as the LIS length ending at `i`.
LC 673 requires maintaining two arrays:
- `length[i]`: length of LIS ending at index `i`.
- `count[i]`: number of LIS of length `length[i]` ending at index `i`.

#### Java Implementation
```java
public class NumberOfLIS {
    public int findNumberOfLIS(int[] nums) {
        if (nums == null || nums.length == 0) return 0;
        int n = nums.length;

        int[] length = new int[n];
        int[] count = new int[n];
        
        // Base initialization: every single element is an LIS of length 1, count 1
        for (int i = 0; i < n; i++) {
            length[i] = 1;
            count[i] = 1;
        }

        int maxLength = 1;

        for (int i = 1; i < n; i++) {
            for (int j = 0; j < i; j++) {
                if (nums[i] > nums[j]) {
                    if (length[j] + 1 > length[i]) {
                        length[i] = length[j] + 1;
                        count[i] = count[j]; // Reset count to new optimal precursor
                    } else if (length[j] + 1 == length[i]) {
                        count[i] += count[j]; // Accumulate alternate valid ways
                    }
                }
            }
            maxLength = Math.max(maxLength, length[i]);
        }

        int totalCount = 0;
        for (int i = 0; i < n; i++) {
            if (length[i] == maxLength) {
                totalCount += count[i];
            }
        }

        return totalCount;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(N^2)$ — Nested iteration over all previous indices.
- **Space Complexity:** $O(N)$ — 2 DP arrays (`length` and `count`).

---

### 2. Best Time to Buy and Sell Stock IV — <a href="https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/" target="_blank">LeetCode 188</a> [SELECTIVE / STRETCH]

> [!NOTE]
> **Selective/Stretch Status**: LC 188 is included as a selective stretch problem because <a href="https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/" target="_blank">LeetCode 123</a> already establishes the core 2-transaction state machine. LC 188 generalizes the unrolled state machine to $K$ arbitrary transactions.

#### Problem Statement
Given an integer array `prices` and an integer `k`, return the maximum profit you can achieve with at most `k` transactions.

#### Generalization & Optimization
- If $k \ge N / 2$, transaction limits become non-binding, reducing the problem to unlimited transactions (<a href="https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/" target="_blank">LeetCode 122</a> greedy solution).
- For general $k < N / 2$, build a 2D state matrix:
  - `buy[t]`: max profit on current day after making $t$-th buy ($1 \le t \le k$).
  - `sell[t]`: max profit on current day after completing $t$-th sell ($1 \le t \le k$).

#### Java Implementation
```java
import java.util.Arrays;

public class StockIV {
    public int maxProfit(int k, int[] prices) {
        if (prices == null || prices.length == 0 || k == 0) return 0;
        int n = prices.length;

        // Optimization: unlimited transactions if k >= n / 2
        if (k >= n / 2) {
            int maxProfit = 0;
            for (int i = 1; i < n; i++) {
                if (prices[i] > prices[i - 1]) {
                    maxProfit += prices[i] - prices[i - 1];
                }
            }
            return maxProfit;
        }

        int[] buy = new int[k + 1];
        int[] sell = new int[k + 1];
        Arrays.fill(buy, Integer.MIN_VALUE / 2);

        for (int price : prices) {
            for (int t = 1; t <= k; t++) {
                buy[t] = Math.max(buy[t], sell[t - 1] - price);
                sell[t] = Math.max(sell[t], buy[t] + price);
            }
        }

        return sell[k];
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(N \times K)$ — Outer loop over prices, inner loop over $K$ transaction bounds.
- **Space Complexity:** $O(K)$ — Space optimized using 1D arrays of size $K+1$.

---

## Pattern Comparison Matrix

| Problem | Type | Distinct Requirement | Key State Concept |
| :--- | :--- | :--- | :--- |
| **LC 673 (Num LIS)** | Core | Optimal value + Count of ways | Dual-array update (`length[]` + `count[]`) |
| **LC 188 (Stock IV)**| Selective / Stretch | Generalize 2D state matrix to $K$ | Parameterized transaction state array `sell[t]` |
