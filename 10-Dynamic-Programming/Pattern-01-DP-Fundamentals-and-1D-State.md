# Pattern 01: DP Fundamentals & 1D State

> Dynamic Programming (DP) optimizes recursive algorithms that exhibit **Overlapping Subproblems** and **Optimal Substructure** by storing intermediate subproblem results in a memoization table or bottom-up tabulation array.

---

## Why This Pattern Exists

When a recursive problem evaluates the exact same state multiple times, naive execution leads to exponential time complexity ($O(2^N)$).

### The DP Spectrum

```text
Naive Recursion O(2^N) ──► Top-Down Memoization O(N) ──► Bottom-Up Tabulation O(N) ──► Space-Optimized O(1)
(Redundant Calls)          (Recursion + Cache)           (Iterative Array)            (State Variables)
```

1. **Overlapping Subproblems:** The recursive call tree computes identical subproblems repeatedly (e.g., $F(n-1)$ and $F(n-2)$ both call $F(n-3)$).
2. **Optimal Substructure:** The optimal solution to a problem of size $N$ can be constructed directly from optimal solutions to subproblems of size $< N$.
3. **1D State Definition:** A single parameter `dp[i]` represents the optimal answer for a subproblem of length or prefix $i$.

---

## ☕ Standard Java Templates

### 1. Top-Down Memoization Template
```java
public int rob(int[] nums) {
    int[] memo = new int[nums.length];
    Arrays.fill(memo, -1);
    return solve(nums, nums.length - 1, memo);
}

private int solve(int[] nums, int i, int[] memo) {
    if (i < 0) return 0; // Base Case
    if (memo[i] != -1) return memo[i]; // Return cached answer
    
    // Recurrence Relation: max(skip current house, rob current house + solve(i-2))
    int ans = Math.max(solve(nums, i - 1, memo), nums[i] + solve(nums, i - 2, memo));
    memo[i] = ans; // Cache result
    return ans;
}
```

### 2. Bottom-Up Tabulation with $O(1)$ Space Optimization
```java
public int robSpaceOptimized(int[] nums) {
    if (nums.length == 0) return 0;
    int prev2 = 0; // Represents dp[i-2]
    int prev1 = 0; // Represents dp[i-1]
    
    for (int num : nums) {
        int curr = Math.max(prev1, prev2 + num);
        prev2 = prev1;
        prev1 = curr;
    }
    return prev1;
}
```

---

## 🔗 Cross-Section Reference Only

> **Maximum Subarray (<a href="https://leetcode.com/problems/maximum-subarray/" target="_blank">LeetCode 53</a>):**
> ⚠️ **Canonical Ownership Note:** LC 53 belongs canonically to **Arrays (Kadane's Algorithm)**. It is referenced here as a 1D DP transition (`dp[i] = max(nums[i], dp[i-1] + nums[i])`), but does NOT count toward Section 10's canonical question total.

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Maximum Subarray (LC 53)** | Housed canonically in **Section 01 (Arrays / Kadane)**. |
| **Unique Paths (LC 62)** | Requires 2D grid coordinates `dp[i][j]` — belongs to **Grid DP (Pattern 02)**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 2 |
| **Medium** | 3 |
| **Total Canonical Questions** | **5** |

---

## 🎯 Question Set (5 Canonical Questions)

### Q1. Climbing Stairs
<a href="https://leetcode.com/problems/climbing-stairs/" target="_blank">LeetCode 70</a> — **Easy**

**Target Skill:** Basic 1D state transition and space optimization.

**Core Reasoning:**
- To reach step $N$, you can take 1 step from $(N-1)$ or 2 steps from $(N-2)$.
- Recurrence: `dp[i] = dp[i-1] + dp[i-2]`.
- Base cases: `dp[1] = 1`, `dp[2] = 2`.

**Why it belongs here:** The fundamental entry problem for 1D DP tabulation.

**Complexity:** Time: $O(N)$, Space: $O(1)$.

---

### Q2. Min Cost Climbing Stairs
<a href="https://leetcode.com/problems/min-cost-climbing-stairs/" target="_blank">LeetCode 746</a> — **Easy**

**Target Skill:** Minimization state transition on 1D arrays.

**Core Reasoning:**
- Cost at step $i$ is `cost[i]`. Min cost to reach step $i$ is `dp[i] = cost[i] + min(dp[i-1], dp[i-2])`.
- Top floor $N$ can be reached from $(N-1)$ or $(N-2)$ without paying additional step cost.
- Answer: `min(dp[N-1], dp[N-2])`.

**Why it belongs here:** Teaches minimum cost accumulation over 1D states.

**Complexity:** Time: $O(N)$, Space: $O(1)$.

---

### Q3. House Robber
<a href="https://leetcode.com/problems/house-robber/" target="_blank">LeetCode 198</a> — **Medium**

**Target Skill:** Non-adjacent choice state transitions.

**Core Reasoning:**
- Cannot rob adjacent houses.
- For house $i$: Choice A (Skip house $i$, take `dp[i-1]`) vs Choice B (Rob house $i$, take `nums[i] + dp[i-2]`).
- Recurrence: `dp[i] = max(dp[i-1], nums[i] + dp[i-2])`.

**Why it belongs here:** Canonical non-adjacent decision DP problem.

**Complexity:** Time: $O(N)$, Space: $O(1)$.

---

### Q4. House Robber II
<a href="https://leetcode.com/problems/house-robber-ii/" target="_blank">LeetCode 213</a> — **Medium**

**Target Skill:** Circular array reduction to 1D DP subproblems.

**Core Reasoning:**
- Houses are arranged in a **circle**, meaning house $0$ and house $N-1$ are adjacent!
- You cannot rob both house $0$ and house $N-1$.
- **Reduction:** Run standard House Robber 1D DP on two linear slices:
  1. Slice A: `nums[0 ... N-2]` (Includes 1st house, excludes last).
  2. Slice B: `nums[1 ... N-1]` (Excludes 1st house, includes last).
- Answer: `max(robLinear(Slice A), robLinear(Slice B))`.

**Why it belongs here:** Demonstrates reducing circular constraints into linear 1D DP calls.

**Complexity:** Time: $O(N)$, Space: $O(1)$.

---

### Q5. Decode Ways
<a href="https://leetcode.com/problems/decode-ways/" target="_blank">LeetCode 91</a> — **Medium**

**Target Skill:** Valid single-digit and double-digit string decoding state transitions.

**Core Reasoning:**
- **Canonical Ownership:** Belongs canonically to **Dynamic Programming**.
- String of digits mapped to letters (`'1'` $\to$ 'A', ..., `'26'` $\to$ 'Z').
- At index $i$:
  - If `s[i-1] != '0'`: single digit decode is valid $\to$ `dp[i] += dp[i-1]`.
  - If `s[i-2...i-1]` is between `"10"` and `"26"`: double digit decode is valid $\to$ `dp[i] += dp[i-2]`.

**Why it belongs here:** Canonical DP problem requiring string boundary validity checks for 1D step transitions.

**Complexity:** Time: $O(N)$, Space: $O(1)$.

---

## ⚡ Mastery Checklist

- [ ] Can you identify whether a problem has **Overlapping Subproblems** and **Optimal Substructure**?
- [ ] How do you optimize 1D DP space from $O(N)$ to $O(1)$ when `dp[i]` only depends on `dp[i-1]` and `dp[i-2]`?
- [ ] How does `House Robber II` reduce a circular array problem into two linear 1D DP subproblems?
- [ ] Can you write `Decode Ways (LC 91)` bottom-up in $O(N)$ time with proper zero-handling?
