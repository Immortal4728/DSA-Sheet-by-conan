# Pattern 02: Subsets & Combinations

> Subsets and Combinations select elements from a collection where **order does NOT matter** (`[1, 2]` is identical to `[2, 1]`). Backtracking explores candidates using a `start` index to prevent duplicate permutations.

---

## Why This Pattern Exists

When generating subsets or combinations, we are building sub-collections of an array of size $N$:
- **Subsets:** All $2^N$ possible selections of elements (sizes $0 \dots N$).
- **Combinations:** All $\binom{N}{K}$ selections of exactly size $K$.

To ensure we never generate duplicate orderings (e.g., generating both `[1, 2]` and `[2, 1]`), we enforce a strict **forward-only `start` index invariant**:
$$\text{At index } i \text{, subsequent choices can ONLY come from indices } \ge i \text{ (or } > i\text{)}$$

---

## Two Fundamental Implementation Styles

### Style 1: Start-Index + Loop-Based Backtracking (Preferred for Flexibility)
```java
private void backtrack(int[] nums, int start, List<Integer> current, List<List<Integer>> result) {
    result.add(new ArrayList<>(current)); // Save copy of current subset
    
    for (int i = start; i < nums.length; i++) {
        current.add(nums[i]);            // 1. CHOOSE
        backtrack(nums, i + 1, current, result); // 2. RECURSE (forward only)
        current.remove(current.size() - 1); // 3. UNDO (Backtrack)
    }
}
```

### Style 2: Choose / Don't Choose (Binary Decision Tree)
```java
private void backtrack(int[] nums, int idx, List<Integer> current, List<List<Integer>> result) {
    if (idx == nums.length) {
        result.add(new ArrayList<>(current));
        return;
    }
    // Branch A: INCLUDE nums[idx]
    current.add(nums[idx]);
    backtrack(nums, idx + 1, current, result);
    current.remove(current.size() - 1); // Undo
    
    // Branch B: EXCLUDE nums[idx]
    backtrack(nums, idx + 1, current, result);
}
```

---

## Duplicate Handling Invariant (Subsets II / Combination Sum II)

When the input array contains duplicate values (e.g., `[1, 2, 2]`):
1. **Sort the array first:** `Arrays.sort(nums)`.
2. **Skip duplicate choices in loop:** `if (i > start && nums[i] == nums[i - 1]) continue;`.

> **Why this works:** At the current recursive depth, if `nums[i] == nums[i-1]`, picking `nums[i]` as the candidate would generate an identical decision tree to the one already explored by `nums[i-1]`.

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Permutations (LC 46)** | Order matters (`[1, 2]` != `[2, 1]`) — belongs to **Permutations (Pattern 03)**. |
| **Palindrome Partitioning** | Partitions contiguous substrings rather than selecting elements — belongs to **Partitioning (Pattern 04)**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Medium** | 6 |
| **Total Canonical Questions** | **6** |

---

## 🎯 Question Set (6 Canonical Questions)

### Q1. Subsets
<a href="https://leetcode.com/problems/subsets/" target="_blank">LeetCode 78</a> — **Medium**

**Target Skill:** Basic start-index loop backtracking for power set generation ($2^N$).

**Core Reasoning:**
- At every recursive call, append `new ArrayList<>(current)` to `result`.
- Loop `i` from `start` to `N - 1`: add `nums[i]`, recurse `backtrack(i + 1)`, remove last element.

**Why it belongs here:** The fundamental entry problem for combination backtracking.

**Complexity:** Time: $O(N \cdot 2^N)$, Space: $O(N)$ recursion stack.

---

### Q2. Subsets II
<a href="https://leetcode.com/problems/subsets-ii/" target="_blank">LeetCode 90</a> — **Medium**

**Target Skill:** Duplicate subset suppression via sorting and sibling skip logic.

**Core Reasoning:**
- Input array may contain duplicates.
- Sort `nums`.
- In backtracking loop: `if (i > start && nums[i] == nums[i - 1]) continue;`.

**Why it belongs here:** Essential pattern for handling duplicate elements in combination trees.

**Complexity:** Time: $O(N \cdot 2^N)$, Space: $O(N)$.

---

### Q3. Combinations
<a href="https://leetcode.com/problems/combinations/" target="_blank">LeetCode 77</a> — **Medium**

**Target Skill:** Fixed-size $K$ subset selection with search pruning.

**Core Reasoning:**
- Generate all combinations of $K$ numbers chosen from $1 \dots N$.
- Base case: `if (current.size() == k)` append to result and return.
- Loop `i` from `start` to `N`. Pruning optimization: `i <= N - (k - current.size()) + 1`.

**Why it belongs here:** Standard $\binom{N}{K}$ fixed-depth combination search.

**Complexity:** Time: $O(K \cdot \binom{N}{K})$, Space: $O(K)$.

---

### Q4. Combination Sum
<a href="https://leetcode.com/problems/combination-sum/" target="_blank">LeetCode 39</a> — **Medium**

**Target Skill:** Unlimited element reuse by passing `start = i` in recursion.

**Core Reasoning:**
- Candidate numbers can be chosen **unlimited times**.
- In loop: add `candidates[i]`, recurse `backtrack(i, target - candidates[i])` (pass `i`, NOT `i + 1`!).
- Base case: `if (target == 0)` save combination. If `target < 0` return.

**Why it belongs here:** Teaches element reuse mechanics in backtracking trees.

**Complexity:** Time: $O(N^{\text{target}/\min})$, Space: $O(\text{target}/\min)$.

---

### Q5. Combination Sum II
<a href="https://leetcode.com/problems/combination-sum-ii/" target="_blank">LeetCode 40</a> — **Medium**

**Target Skill:** Single-use elements + duplicate candidate handling (`start = i + 1` + duplicate skip).

**Core Reasoning:**
- Candidates may have duplicates; each number can be used **at most once**.
- Sort `candidates`.
- In loop: skip duplicates `if (i > start && candidates[i] == candidates[i - 1]) continue;`.
- Recurse `backtrack(i + 1, target - candidates[i])`.

**Why it belongs here:** Integrates single-use constraints with duplicate value skipping.

**Complexity:** Time: $O(2^N)$, Space: $O(N)$.

---

### Q6. Combination Sum III
<a href="https://leetcode.com/problems/combination-sum-iii/" target="_blank">LeetCode 216</a> — **Medium**

**Target Skill:** Dual-constraint combinations (exact count $K$ and exact sum $N$).

**Core Reasoning:**
- Find all valid combinations of $K$ numbers that sum to $N$ using numbers $1 \dots 9$. Each number used at most once.
- Base cases: `if (current.size() == k && target == 0)` save combination.
- If `current.size() == k || target < 0` return.
- Loop `i` from `start` to `9`.

**Why it belongs here:** Advanced combination search combining length bounds and target sum constraints.

**Complexity:** Time: $O(\binom{9}{K})$, Space: $O(K)$.

---

## ⚡ Mastery Checklist

- [ ] Can you explain why the `start` index prevents duplicate orderings like `[1, 2]` and `[2, 1]`?
- [ ] Why MUST you sort the array before applying `if (i > start && nums[i] == nums[i-1]) continue;`?
- [ ] How does passing `i` vs `i + 1` in the recursive call control whether elements can be reused?
- [ ] Can you write `Subsets II` and `Combination Sum II` from a blank editor in Java?
