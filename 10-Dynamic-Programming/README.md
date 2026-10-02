# Section 10 — Dynamic Programming

Dynamic Programming (DP) is an optimization paradigm used to solve complex problems by breaking them down into simpler overlapping subproblems, solving each subproblem once, and storing their solutions to eliminate redundant computation.

---

## Final Curriculum Architecture

This module contains **30 canonical core questions + 1 selective/stretch question**, organized into 9 structured pattern files:

```text
10-Dynamic-Programming/
├── Pattern-01-DP-Fundamentals-and-1D-State.md
├── Pattern-02-Grid-and-2D-DP.md
├── Pattern-03-Knapsack-and-Subset-DP.md
├── Pattern-04-Sequence-DP.md
├── Pattern-05-String-DP.md
├── Pattern-06-Interval-and-Partition-DP.md
├── Pattern-07-State-Transition-DP.md
├── Pattern-08-DP-with-Optimization-and-Counting.md
├── Pattern-09-Hidden-DP-Recognition.md
└── README.md
```

---

## 1. What Dynamic Programming Is

Dynamic Programming is **memoized recursion or bottom-up state progression over DAGs (Directed Acyclic Graphs)**. It is applicable when a problem exhibits two foundational properties:
1. **Overlapping Subproblems**: The same subproblems are solved repeatedly in a naive recursive evaluation tree.
2. **Optimal Substructure**: An optimal solution to the problem contains within it optimal solutions to its subproblems.

---

## 2. How Recursion Leads Naturally into DP

Consider computing Fibonacci numbers recursively:
```java
// Naive Recursion: O(2^N) time due to identical branches
int fib(int n) {
    if (n <= 1) return n;
    return fib(n - 1) + fib(n - 2);
}
```
The call `fib(5)` invokes `fib(3)` twice, `fib(2)` three times, and `fib(1)` five times. 

By adding a lookup cache (Memoization), we cut redundant branches:
$$\text{Recursion } + \text{ Memoization} = \text{Top-Down DP } O(N)$$

By reversing execution to compute from smallest subproblems upward (Tabulation), we eliminate stack overhead:
$$\text{Iterative Table Filling} = \text{Bottom-Up DP } O(N)$$

---

## 3. Core Principles & Terminology

### 3. Overlapping Subproblems
Subproblems recur frequently. Storing subproblem outputs in an array or map avoids re-computation.

### 4. Optimal Substructure
The overall optimal solution can be constructed directly from the optimal solutions of subproblems. For example, in Shortest Path, if the shortest path from $A$ to $C$ passes through $B$, the segment $A \to B$ must be the shortest path from $A$ to $B$.

### 5. State Definition
A precise mathematical definition of what `dp[...]` represents.
- *Example*: `dp[i]` = min cost to reach step `i`.

### 6. Transition Definition (Recurrence Relation)
The equation linking state `dp[...]` to previous states.
- *Example*: `dp[i] = min(dp[i-1] + cost[i-1], dp[i-2] + cost[i-2])`.

### 7. Base Cases
The initial values of smallest subproblems that do not depend on any previous states.
- *Example*: `dp[0] = 0`, `dp[1] = 0`.

---

## 4. Execution Frameworks

### 8. Memoization (Top-Down DP)
- Begins at target state `N` and recursively breaks it down.
- Stores calculated results in `memo` array/hash table.
- **Pros**: Only computes reachable states; easy conversion from naive DFS.
- **Cons**: Function call stack overhead; potential `StackOverflowError`.

```java
int solveMemo(int n, int[] memo) {
    if (n <= 1) return n;
    if (memo[n] != -1) return memo[n];
    return memo[n] = solveMemo(n - 1, memo) + solveMemo(n - 2, memo);
}
```

### 9. Tabulation (Bottom-Up DP)
- Begins at base cases (e.g., `0, 1`) and iteratively fills the table up to `N`.
- **Pros**: No recursion overhead; easy to apply space optimizations.
- **Cons**: Computes all states in table even if some are unneeded.

```java
int solveTab(int n) {
    int[] dp = new int[n + 1];
    dp[0] = 0; dp[1] = 1;
    for (int i = 2; i <= n; i++) dp[i] = dp[i-1] + dp[i-2];
    return dp[n];
}
```

### 10. Space Optimization
If `dp[i]` only depends on a fixed window of previous states (e.g., `dp[i-1]` and `dp[i-2]`), we can replace the full $O(N)$ or $O(N^2)$ array with scalar variables or 1D arrays, reducing space to $O(1)$ or $O(N)$.

---

## 5. Overview of DP Families

### 11. 1D DP ([Pattern 01](file:///d:/DSA/10-Dynamic-Programming/Pattern-01-DP-Fundamentals-and-1D-State.md))
- State depends on single variable `i` (index, step, amount).
- Transition depends on immediate previous elements (`i-1`, `i-2`).
- *Canonical Questions*: <a href="https://leetcode.com/problems/climbing-stairs/" target="_blank">LeetCode 70</a>, <a href="https://leetcode.com/problems/min-cost-climbing-stairs/" target="_blank">LeetCode 746</a>, <a href="https://leetcode.com/problems/house-robber/" target="_blank">LeetCode 198</a>, <a href="https://leetcode.com/problems/house-robber-ii/" target="_blank">LeetCode 213</a>, <a href="https://leetcode.com/problems/decode-ways/" target="_blank">LeetCode 91</a>.

### 12. 2D & Grid DP ([Pattern 02](file:///d:/DSA/10-Dynamic-Programming/Pattern-02-Grid-and-2D-DP.md))
- State represented on grid matrix `dp[i][j]`.
- Moves allowed only top-to-bottom or left-to-right (`dp[i-1][j]` and `dp[i][j-1]`).
- *Canonical Questions*: <a href="https://leetcode.com/problems/unique-paths/" target="_blank">LeetCode 62</a>, <a href="https://leetcode.com/problems/unique-paths-ii/" target="_blank">LeetCode 63</a>, <a href="https://leetcode.com/problems/minimum-path-sum/" target="_blank">LeetCode 64</a>, <a href="https://leetcode.com/problems/maximal-square/" target="_blank">LeetCode 221</a>.

### 13. Knapsack & Subset DP ([Pattern 03](file:///d:/DSA/10-Dynamic-Programming/Pattern-03-Knapsack-and-Subset-DP.md))
- Choices between taking an item or skipping it under a capacity constraint.
- **0/1 Choice**: Traverse capacity loop backwards (`W` to `w`).
- **Unbounded Choice**: Traverse capacity loop forwards (`w` to `W`).
- *Canonical Questions*: <a href="https://leetcode.com/problems/partition-equal-subset-sum/" target="_blank">LeetCode 416</a>, <a href="https://leetcode.com/problems/target-sum/" target="_blank">LeetCode 494</a>, <a href="https://leetcode.com/problems/coin-change/" target="_blank">LeetCode 322</a>, <a href="https://leetcode.com/problems/coin-change-ii/" target="_blank">LeetCode 518</a>, 0/1 Knapsack Classic.

### 14. Sequence DP ([Pattern 04](file:///d:/DSA/10-Dynamic-Programming/Pattern-04-Sequence-DP.md))
- Operating on arrays where optimal subproblem ending at `i` checks all compatible prior indices `j < i`.
- *Canonical Questions*: <a href="https://leetcode.com/problems/longest-increasing-subsequence/" target="_blank">LeetCode 300</a>, <a href="https://leetcode.com/problems/longest-palindromic-subsequence/" target="_blank">LeetCode 516</a>, <a href="https://leetcode.com/problems/wiggle-subsequence/" target="_blank">LeetCode 376</a>.

### 15. String DP ([Pattern 05](file:///d:/DSA/10-Dynamic-Programming/Pattern-05-String-DP.md))
- Subproblems operate on 2 strings `text1[0...i]` and `text2[0...j]`.
- Match (`text1[i] == text2[j]`) vs Non-match transitions.
- *Canonical Questions*: <a href="https://leetcode.com/problems/longest-common-subsequence/" target="_blank">LeetCode 1143</a>, <a href="https://leetcode.com/problems/edit-distance/" target="_blank">LeetCode 72</a>, <a href="https://leetcode.com/problems/distinct-subsequences/" target="_blank">LeetCode 115</a>, <a href="https://leetcode.com/problems/interleaving-string/" target="_blank">LeetCode 97</a>.

### 16. Interval & Partition DP ([Pattern 06](file:///d:/DSA/10-Dynamic-Programming/Pattern-06-Interval-and-Partition-DP.md))
- Subproblems operate on range `[i, j]`.
- Loop by increasing interval length; choose partition point `k`.
- *Canonical Questions*: Matrix Chain Multiplication Classic, <a href="https://leetcode.com/problems/minimum-cost-to-cut-a-stick/" target="_blank">LeetCode 1547</a>, <a href="https://leetcode.com/problems/burst-balloons/" target="_blank">LeetCode 312</a>.

### 17. State Transition DP ([Pattern 07](file:///d:/DSA/10-Dynamic-Programming/Pattern-07-State-Transition-DP.md))
- Finite state machine transitions (`hold`, `sold`, `rest`, `buy1`, `sell1`).
- *Canonical Questions*: <a href="https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/" target="_blank">LeetCode 309</a>, <a href="https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/" target="_blank">LeetCode 714</a>, <a href="https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/" target="_blank">LeetCode 123</a>.

### 18. Optimization & Counting DP ([Pattern 08](file:///d:/DSA/10-Dynamic-Programming/Pattern-08-DP-with-Optimization-and-Counting.md))
- Simultaneous tracking of extreme value and total ways to achieve optimum, or parameterizing state dimensions to $K$.
- *Canonical Questions*: <a href="https://leetcode.com/problems/number-of-longest-increasing-subsequence/" target="_blank">LeetCode 673</a> (Core), <a href="https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/" target="_blank">LeetCode 188</a> (Selective/Stretch).

### 19. Hidden DP Recognition ([Pattern 09](file:///d:/DSA/10-Dynamic-Programming/Pattern-09-Hidden-DP-Recognition.md))
- Unlabeled problem recognition: detect overlapping subproblems, choices, and recurrence in disguised problems.
- *Canonical Questions*: <a href="https://leetcode.com/problems/word-break/" target="_blank">LeetCode 139</a>, <a href="https://leetcode.com/problems/perfect-squares/" target="_blank">LeetCode 279</a>, <a href="https://leetcode.com/problems/combination-sum-iv/" target="_blank">LeetCode 377</a>.

---

## 6. Paradigm Comparison & Decision Boundaries

### 20. When DP Should NOT Be Used
- **No Overlapping Subproblems**: Divide & Conquer (e.g., Merge Sort) has non-overlapping subproblems.
- **Unconstrained Tree Search / Exact Path Generation**: If you need to print every permutation or subset, use Backtracking.
- **Greedy Choice Property Holds**: If a local optimal choice guarantees global optimality without backtracking, use Greedy.

### 21. Relationship Between DP and Greedy
- **DP**: Evaluates **all** valid candidate choices at step `i` and picks min/max. Always correct if state definition is valid.
- **Greedy**: Evaluates a single **locally optimal choice** at step `i` without looking back. Fast ($O(N)$ or $O(N \log N)$), but fails if local choices corrupt future options.

### 22. Relationship Between DP and Recursion / Backtracking
- **Backtracking**: Traverses full decision tree ($O(2^N)$ or $O(N!)$). Computes full state paths.
- **DP**: Collapses identical nodes in decision tree into single state nodes, reducing execution from exponential to polynomial ($O(N^2)$ or $O(N \times W)$).

---

## 7. Java Implementation Patterns & Complexity Table

### 23. Java Implementation Templates

#### Standard 1D Bottom-Up Template
```java
public int solve1D(int[] nums) {
    int n = nums.length;
    int[] dp = new int[n + 1];
    dp[0] = baseValue;

    for (int i = 1; i <= n; i++) {
        dp[i] = transition(dp[i - 1], nums[i - 1]);
    }
    return dp[n];
}
```

#### Standard 0/1 Knapsack Template (Space Optimized)
```java
public int knapsack(int[] weights, int[] values, int W) {
    int[] dp = new int[W + 1];
    for (int i = 0; i < weights.length; i++) {
        for (int w = W; w >= weights[i]; w--) { // Reverse loop for 0/1
            dp[w] = Math.max(dp[w], dp[w - weights[i]] + values[i]);
        }
    }
    return dp[W];
}
```

### 24. Time & Space Complexity Summary

| Pattern | General Time Complexity | Standard Space | Optimized Space |
| :--- | :--- | :--- | :--- |
| **1D DP** | $O(N)$ | $O(N)$ | $O(1)$ |
| **2D Grid DP** | $O(M \times N)$ | $O(M \times N)$ | $O(N)$ |
| **0/1 Knapsack** | $O(N \times W)$ | $O(N \times W)$ | $O(W)$ |
| **Sequence DP** | $O(N^2)$ or $O(N \log N)$ | $O(N)$ | $O(N)$ |
| **String DP** | $O(M \times N)$ | $O(M \times N)$ | $O(N)$ |
| **Interval DP** | $O(N^3)$ | $O(N^2)$ | $O(N^2)$ |
| **State Transition** | $O(N)$ | $O(N)$ | $O(1)$ |

---

## Mastery Standard

Upon completing Section 10, the learner will be able to:
- Identify overlapping subproblems and optimal substructure in unfamiliar questions.
- Formulate precise mathematical DP state definitions before writing code.
- Derive exact recurrence transitions and establish correct base cases.
- Systematically transform recursive memoization solutions into bottom-up tabulation.
- Apply space optimization techniques to contract table dimensions.
- Distinguish 1D, 2D, 0/1 vs unbounded knapsack, sequence, string, and interval states.
- Model state-machine transitions and extend DP state with extra parameters.
- Recognize hidden DP problems disguised as strings, math, or combinations.
- Clearly articulate why a problem requires DP instead of Greedy or Backtracking.
- Implement solutions from a blank editor in Java and analyze time/space complexity.
