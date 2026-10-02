# Pattern 09 — Hidden DP Recognition

In real-world technical interviews, problems are never tagged with `"Dynamic Programming"`. They often look like String Segmentation, Math/Number Theory, or Combinatorial Search problems.

This pattern focuses on **Pattern Recognition First**: training yourself to detect DP signals in unlabeled, ambiguous problem statements.

---

## The Hidden DP Detection Signals

When given an unfamiliar problem, ask these 4 diagnostic questions:

```text
1. Are we asked for Optimal Value (min/max), Feasibility (yes/no), or Count (number of ways)?
                        +
2. Can the problem be solved by making a sequence of choices?
                        +
3. Do subproblems repeat over identical state parameters (Overlapping Subproblems)?
                        +
4. Does an optimal global decision depend ONLY on optimal subproblem decisions (Optimal Substructure)?
```

If all 4 are YES $\Rightarrow$ **It is Dynamic Programming**.

### Contrast with Alternative Paradigms
- **Greedy**: Making a locally optimal choice without looking back always leads to a globally optimal solution. (If local choice can backfire later, Greedy fails $\Rightarrow$ DP).
- **Backtracking / DFS**: Needs to return *all actual full paths or combinations*. (If we only need the *count* or *min/max length*, Backtracking will TLE $\Rightarrow$ DP).

---

## Pattern Questions (3 Canonical)

### 1. Word Break — <a href="https://leetcode.com/problems/word-break/" target="_blank">LeetCode 139</a>

#### Initial Impression (Hidden Surface)
Looks like a String Backtracking / Trie search problem where you split a string into dictionary words.

#### Why Backtracking Fails
Trying all string partitions results in $O(2^N)$ exponential complexity. For example, `s = "aaaaaaa"`, `dict = ["a", "aa", "aaa"]` creates massive duplicate branches for substring feasibility.

#### DP Recognition & State Formulation
- **Signal**: Feasibility question ("Can the string be segmented?").
- **State**: `dp[i]` = boolean flag indicating whether prefix `s[0...i-1]` can be segmented into valid dictionary words.
- **Transition**:
  $$\text{dp}[i] = \text{true if } \exists\, j < i \text{ such that } \text{dp}[j] == \text{true AND } s[j...i-1] \in \text{wordDict}$$

#### Java Implementation
```java
import java.util.List;
import java.util.HashSet;
import java.util.Set;

public class WordBreak {
    public boolean wordBreak(String s, List<String> wordDict) {
        Set<String> dict = new HashSet<>(wordDict);
        int n = s.length();
        boolean[] dp = new boolean[n + 1];
        
        // Base case: empty string is always valid
        dp[0] = true;

        for (int i = 1; i <= n; i++) {
            for (int j = 0; j < i; j++) {
                if (dp[j] && dict.contains(s.substring(j, i))) {
                    dp[i] = true;
                    break; // Found one valid cut for prefix i
                }
            }
        }

        return dp[n];
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(N^2 \times L)$ where $N$ is string length and $L$ is max word length.
- **Space Complexity:** $O(N)$ for DP table + dictionary HashSet storage.

---

### 2. Perfect Squares — <a href="https://leetcode.com/problems/perfect-squares/" target="_blank">LeetCode 279</a>

#### Initial Impression (Hidden Surface)
Looks like a Math / Number Theory problem or Breadth-First Search (BFS) problem on a implicit graph.

#### Why Greedy Fails
Greedy choice ("always pick the largest perfect square $\le n$") fails!
- Example: $n = 12$.
- Greedy choice: $9 + 1 + 1 + 1 = 4$ squares ($9, 1, 1, 1$).
- Optimal DP choice: $4 + 4 + 4 = 3$ squares ($4, 4, 4$).

#### DP Recognition & State Formulation
- **Signal**: Minimization over repeated unbounded choices (equivalent to Unbounded Knapsack / Coin Change).
- **State**: `dp[i]` = minimum number of perfect square numbers that sum to `i`.
- **Transition**:
  $$\text{dp}[i] = \min_{1 \le square \le i} (\text{dp}[i - square] + 1)$$

#### Java Implementation
```java
import java.util.Arrays;

public class PerfectSquares {
    public int numSquares(int n) {
        int[] dp = new int[n + 1];
        Arrays.fill(dp, Integer.MAX_VALUE);
        dp[0] = 0;

        for (int i = 1; i <= n; i++) {
            for (int s = 1; s * s <= i; s++) {
                dp[i] = Math.min(dp[i], dp[i - s * s] + 1);
            }
        }

        return dp[n];
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(N \sqrt{N})$ — Outer loop up to $N$, inner loop up to $\sqrt{N}$.
- **Space Complexity:** $O(N)$ — 1D DP table.

---

### 3. Combination Sum IV — <a href="https://leetcode.com/problems/combination-sum-iv/" target="_blank">LeetCode 377</a>

#### Initial Impression (Hidden Surface)
Named "Combination Sum", heavily tricking learners into using Backtracking / Recursion (like LeetCode 39: Combination Sum).

#### Why Backtracking Fails
LC 39 asks for the *list of combinations*. LC 377 asks for the *number of ordered permutations* summing to `target`. Backtracking to count valid combinations causes $O(2^{\text{target}})$ time complexity.

#### DP Recognition & State Formulation
- **Signal**: Counting problem ("Return the number of possible combinations").
- **State**: `dp[i]` = total number of ordered combinations that sum up to target `i`.
- **Transition**:
  $$\text{dp}[i] = \sum_{num \in nums, num \le i} \text{dp}[i - num]$$

#### Java Implementation
```java
public class CombinationSumIV {
    public int combinationSum4(int[] nums, int target) {
        int[] dp = new int[target + 1];
        dp[0] = 1; // 1 way to form target 0 (empty set)

        for (int i = 1; i <= target; i++) {
            for (int num : nums) {
                if (i >= num) {
                    dp[i] += dp[i - num];
                }
            }
        }

        return dp[target];
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(\text{Target} \times N)$ — Target outer loop, array size inner loop.
- **Space Complexity:** $O(\text{Target})$ — 1D DP array of size `target + 1`.

---

## Summary of Hidden DP Patterns

| Problem | Surface Disguise | Why Naive Paradigm Fails | True DP Equivalent |
| :--- | :--- | :--- | :--- |
| **LC 139 (Word Break)** | String Parsing | Backtracking duplicate branches | 1D String Split Feasibility |
| **LC 279 (Perfect Squares)** | Number Theory | Greedy pick fails (12 = 9+1+1+1 vs 4+4+4) | Unbounded Knapsack (Min Coins) |
| **LC 377 (Comb Sum IV)** | Combination Search | Exponential TLE ($O(2^T)$) | Coin Change II (Ordered Ways) |
