# Pattern 03: Knapsack & Subset DP

> Knapsack DP models selecting items under a total weight/capacity constraint $W$. The two core sub-families are **0/1 Knapsack** (items used at most once) and **Unbounded Knapsack** (items can be reused infinitely).

---

## Why This Pattern Exists

When making binary choices (*include item $i$* vs *exclude item $i$*) to achieve a capacity target $W$:
- **0/1 Knapsack:** Each item $i$ can be chosen **at most once**.
- **Unbounded Knapsack:** Each item $i$ can be chosen **unlimited times**.

### The Iteration Direction Invariant (1D Space Optimization)

When compressing 2D `dp[items][capacity]` down to a 1D `dp[capacity]` array:

$$\begin{array}{c}
\text{0/1 Knapsack (Single Use): Iterate capacity } w \text{ BACKWARD } (W \to \text{weight}[i]) \\
\downarrow \\
\text{Unbounded Knapsack (Unlimited Use): Iterate capacity } w \text{ FORWARD } (\text{weight}[i] \to W)
\end{array}$$

> **Why Backward Iteration is Mandatory for 0/1 Knapsack:** Iterating backward ensures `dp[w - weight[i]]` represents the state **before** item $i$ was considered, preventing the same item from being used multiple times in the same step.

---

## Three Problem Questions

1. **Boolean Feasibility (*Can I achieve target $W$?*):**
   `dp[w] = dp[w] || dp[w - num]` (e.g., Partition Equal Subset Sum)
2. **Minimum Cost/Items (*What is min items to form target $W$?*):**
   `dp[w] = min(dp[w], 1 + dp[w - coin])` (e.g., Coin Change I)
3. **Combinatorial Ways (*How many ways to form target $W$?*):**
   `dp[w] = dp[w] + dp[w - coin]` (e.g., Coin Change II, Target Sum)

---

## ☕ Standard Java Templates

### 1. Classic 0/1 Knapsack (Tabulation)
```java
public int knapsack01(int[] weights, int[] values, int W) {
    int[] dp = new int[W + 1];
    
    for (int i = 0; i < weights.length; i++) {
        int w = weights[i], v = values[i];
        // BACKWARD iteration prevents reusing item i in the same pass!
        for (int cap = W; cap >= w; cap--) {
            dp[cap] = Math.max(dp[cap], v + dp[cap - w]);
        }
    }
    return dp[W];
}
```

### 2. Unbounded Knapsack (Coin Change II — Combinations)
```java
public int change(int amount, int[] coins) {
    int[] dp = new int[amount + 1];
    dp[0] = 1; // 1 way to form amount 0
    
    for (int coin : coins) {
        // FORWARD iteration allows unlimited reuse of coin!
        for (int w = coin; w <= amount; w++) {
            dp[w] += dp[w - coin];
        }
    }
    return dp[amount];
}
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Combination Sum I / II (LC 39/40)** | Requires constructing exact combination lists — belongs to **Recursion & Backtracking (Module 09)**. |
| **Word Break (LC 139)** | String prefix dictionary matching — belongs to **Hidden DP Recognition (Pattern 09)**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Medium** | 5 (4 LeetCode + 1 Classic Interview Problem) |
| **Total Canonical Questions** | **5** |

---

## 🎯 Question Set (5 Canonical Questions)

### Q1. 0/1 Knapsack Problem
**Classic Interview Problem** — **Medium**

**Target Skill:** Fundamental 0/1 Knapsack state transition and backward space optimization.

**Core Reasoning:**
- Given $N$ items with weights and values, find max value for capacity $W$.
- 1D DP `dp[cap]`. Iterate items $i=0 \dots N-1$.
- Inner loop: `cap` from $W$ down to `weights[i]`. `dp[cap] = max(dp[cap], values[i] + dp[cap - weights[i]])`.

**Why it belongs here:** Canonical foundation for all 0/1 subset DP problems.

**Complexity:** Time: $O(N \cdot W)$, Space: $O(W)$.

---

### Q2. Partition Equal Subset Sum
<a href="https://leetcode.com/problems/partition-equal-subset-sum/" target="_blank">LeetCode 416</a> — **Medium**

**Target Skill:** Boolean feasibility 0/1 subset sum reduction.

**Core Reasoning:**
- Determine if array can be partitioned into two subsets with equal sum.
- If total sum is odd, return `false`. Target $W = \text{sum} / 2$.
- Boolean 1D DP `dp[w]`. Iterate `num` in `nums`, `w` from $W$ down to `num`: `dp[w] = dp[w] || dp[w - num]`.

**Why it belongs here:** Demonstrates boolean feasibility knapsack state (`dp[W] == true`).

**Complexity:** Time: $O(N \cdot W)$, Space: $O(W)$.

---

### Q3. Target Sum
<a href="https://leetcode.com/problems/target-sum/" target="_blank">LeetCode 494</a> — **Medium**

**Target Skill:** Subset sum reduction for signed expression counting.

**Core Reasoning:**
- Assign `+` or `-` to each element to achieve target $T$.
- Mathematical Reduction: Let $P$ be positive subset, $N$ be negative subset.
  $$P - N = T \implies P - (\text{sum} - P) = T \implies 2P = \text{sum} + T \implies P = \frac{\text{sum} + T}{2}$$
- Problem reduces to finding number of subsets summing to $P$ using 0/1 Knapsack counting!

**Why it belongs here:** Mathematical transformation into a 0/1 Knapsack subset counting problem.

**Complexity:** Time: $O(N \cdot P)$, Space: $O(P)$.

---

### Q4. Coin Change
<a href="https://leetcode.com/problems/coin-change/" target="_blank">LeetCode 322</a> — **Medium**

**Target Skill:** Unbounded Knapsack min-coins optimization.

**Core Reasoning:**
- Find minimum number of coins to form `amount`. Coins can be reused infinitely.
- Initialize `dp` array of size `amount + 1` filled with `amount + 1` (infinity placeholder), `dp[0] = 0`.
- Outer loop: `coin` in `coins`. Inner loop: `w` from `coin` up to `amount` (**FORWARD iteration**).
- `dp[w] = min(dp[w], 1 + dp[w - coin])`.

**Why it belongs here:** Canonical Unbounded Knapsack min-optimization problem.

**Complexity:** Time: $O(N \cdot \text{amount})$, Space: $O(\text{amount})$.

---

### Q5. Coin Change II
<a href="https://leetcode.com/problems/coin-change-ii/" target="_blank">LeetCode 518</a> — **Medium**

**Target Skill:** Unbounded Knapsack combination counting (outer coins loop vs inner coins loop).

**Core Reasoning:**
- Count total distinct combinations that sum to `amount`. Unlimited coin reuse.
- **Outer loop coins, Inner loop amount:** `dp[w] += dp[w - coin]`. (Generates **Combinations**, e.g., `1+2` and `2+1` are counted once).

**Why it belongs here:** Teaches combination counting vs permutation counting in DP.

**Complexity:** Time: $O(N \cdot \text{amount})$, Space: $O(\text{amount})$.

---

## ⚡ Mastery Checklist

- [ ] Can you explain why 0/1 Knapsack requires **backward** capacity iteration while Unbounded Knapsack uses **forward** iteration?
- [ ] How does `Target Sum (LC 494)` reduce mathematically to finding a subset sum $P = (\text{sum} + T)/2$?
- [ ] What happens if you swap the loops in `Coin Change II` (Outer loop `amount`, Inner loop `coins`)? (It counts **Permutations** instead of Combinations!).
- [ ] Can you write `Partition Equal Subset Sum` in $O(W)$ space from scratch?
