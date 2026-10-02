# Pattern 04: Sequence DP

> Sequence DP models optimal subsequence properties where state `dp[i]` depends on all previously compatible subproblem states `j < i` rather than just immediate neighbors.

---

## Why This Pattern Exists

In 1D DP (Pattern 01), state `dp[i]` only depends on immediate neighbors `dp[i-1]` or `dp[i-2]`.

In Sequence DP:
- Elements in a **subsequence** are not required to be contiguous.
- For current element `nums[i]`, we must inspect all prior elements `j < i` to check compatibility (e.g., `nums[j] < nums[i]`).
- This requires an inner loop searching over all valid prior states $j$:

$$\text{dp}[i] = 1 + \max_{0 \le j < i, \, \text{compatible}(j, i)} \Big( \text{dp}[j] \Big)$$

---

## ☕ Standard Java Templates

### 1. Longest Increasing Subsequence ($O(N^2)$ DP Tabulation)
```java
public int lengthOfLIS(int[] nums) {
    if (nums.length == 0) return 0;
    int n = nums.length;
    int[] dp = new int[n];
    Arrays.fill(dp, 1); // Every element is an LIS of length 1
    
    int maxLIS = 1;
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < i; j++) {
            if (nums[j] < nums[i]) { // Compatibility condition
                dp[i] = Math.max(dp[i], 1 + dp[j]);
            }
        }
        maxLIS = Math.max(maxLIS, dp[i]);
    }
    return maxLIS;
}
```

### 2. Patience Sorting Binary Search ($O(N \log N)$ Optimization)
```java
public int lengthOfLISOptimized(int[] nums) {
    List<Integer> tails = new ArrayList<>();
    for (int num : nums) {
        int idx = Collections.binarySearch(tails, num);
        if (idx < 0) idx = -(idx + 1);
        if (idx == tails.size()) tails.add(num);
        else tails.set(idx, num);
    }
    return tails.size();
}
```

---

## 🛑 Pattern Boundary & Exclusions

| Problem | Why it belongs elsewhere / Excluded |
|---------|-------------------------------------|
| **Longest Continuous Increasing Subsequence (LC 674)** | ❌ **EXCLUDED.** Simple contiguous 1D array loop, not a core Sequence DP benchmark. |
| **Maximum Length of Pair Chain (LC 646)** | ❌ **EXCLUDED from DP.** Belongs canonically to **Greedy / Interval Scheduling**. |
| **Longest Palindromic Subsequence (LC 516)** | ✅ **Canonically owned by DP!** String section references it as a DP bridge. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Medium** | 3 |
| **Total Canonical Questions** | **3** |

---

## 🎯 Question Set (3 Canonical Questions)

### Q1. Longest Increasing Subsequence
<a href="https://leetcode.com/problems/longest-increasing-subsequence/" target="_blank">LeetCode 300</a> — **Medium**

**Target Skill:** Non-contiguous sequence DP transition over prior indices $j < i$.

**Core Reasoning:**
- Find length of longest strictly increasing subsequence.
- `dp[i]` = length of LIS ending at index `i`.
- For each $i$, check all $j < i$: if `nums[j] < nums[i]`, `dp[i] = max(dp[i], 1 + dp[j])`.

**Why it belongs here:** Canonical foundation for Sequence DP.

**Complexity:** Time: $O(N^2)$ DP / $O(N \log N)$ Binary Search, Space: $O(N)$.

---

### Q2. Longest Palindromic Subsequence
<a href="https://leetcode.com/problems/longest-palindromic-subsequence/" target="_blank">LeetCode 516</a> — **Medium**

**Target Skill:** Interval sequence state `dp[i][j]` over substring ranges.

**Core Reasoning:**
- **Canonical Ownership:** Belongs canonically to **Dynamic Programming**.
- Find longest palindromic subsequence in string $S$.
- State `dp[i][j]`: length of LPS in substring `s[i...j]`.
- Outer loop length $L = 1 \dots N$, inner loop $i = 0 \dots N-L$:
  - If `s[i] == s[j]`: `dp[i][j] = 2 + dp[i+1][j-1]` (or `1` if $i == j$).
  - Else: `dp[i][j] = max(dp[i+1][j], dp[i][j-1])`.

**Why it belongs here:** Canonical interval-based sequence DP.

**Complexity:** Time: $O(N^2)$, Space: $O(N^2)$.

---

### Q3. Wiggle Subsequence
<a href="https://leetcode.com/problems/wiggle-subsequence/" target="_blank">LeetCode 376</a> — **Medium**

**Target Skill:** Dual-direction sequence state tracking (`up[i]` vs `down[i]`).

**Core Reasoning:**
- A sequence is wiggle if differences between adjacent numbers alternate between positive and negative.
- Maintain `up[i]` (length of longest wiggle sequence ending at $i$ with a positive difference) and `down[i]` (ending with a negative difference).
- If `nums[i] > nums[i-1]`: `up[i] = down[i-1] + 1`, `down[i] = down[i-1]`.
- If `nums[i] < nums[i-1]`: `down[i] = up[i-1] + 1`, `up[i] = up[i-1]`.

**Why it belongs here:** Demonstrates multi-state sequence tracking based on directional transitions.

**Complexity:** Time: $O(N)$, Space: $O(1)$.

---

## ⚡ Mastery Checklist

- [ ] Can you explain why `LIS (LC 300)` requires checking all prior states $j < i$ instead of just $i-1$?
- [ ] How does `Patience Sorting` achieve $O(N \log N)$ for LIS using binary search?
- [ ] Why is `Longest Palindromic Subsequence (LC 516)` processed by increasing interval lengths $L$?
- [ ] How do `up` and `down` state variables track alternating signs in `Wiggle Subsequence` in $O(1)$ space?
