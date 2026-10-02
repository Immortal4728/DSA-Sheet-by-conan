# Pattern 07: Hidden Backtracking Recognition

> In SDE interviews, backtracking problems rarely use keywords like "Backtrack" in their problem statements. You must infer the necessity of state-space exploration from core problem characteristics.

---

## Why This Pattern Exists

When encountering an unfamiliar interview question, you must learn to derive the algorithm from first principles.

Ask yourself the **4-Step Backtracking Recognition Checklist**:
1. **Multiple Choices:** Are there multiple valid decisions at the current step?
2. **Dependent Future States:** Does choosing option $X$ now constrain or alter what options are valid in subsequent steps?
3. **Search Space Exploration:** Does the problem ask to find *all* valid configurations, or test if *any* valid allocation exists?
4. **Need to Undo:** If a sequence of choices leads to a dead end, do I need to backtrack and try an alternative option?

If the answer to all 4 is **YES** $\longrightarrow$ **The problem requires Backtracking.**

```text
               4-Step Backtracking Recognition Checklist:
               1. Multiple choices at current step?
               2. Choice constrains future choices?
               3. Search space needs exploration?
               4. Dead end requires undoing choice?
                                 │
                                 ▼
                     [ Backtracking Candidate ]
```

---

## 🧠 Recognition Diagnostic Table

| Problem Hint | Uncovered Constraint | Derived Backtracking Strategy | Canonical Reference |
|--------------|----------------------|-------------------------------|---------------------|
| *"Find non-decreasing subsequences in an unsorted array with duplicates"* | Cannot sort array without destroying order. Order must be preserved. | Loop-based choice with local `HashSet` at each depth to skip duplicate values. | <a href="https://leetcode.com/problems/increasing-subsequences/" target="_blank">LC 491</a> |
| *"Partition array into K subsets of equal sum"* | Subsets must sum to exact target `total / K`. | Sort descending. Bucket packing backtracking: try placing item into $K$ buckets. | <a href="https://leetcode.com/problems/partition-to-k-equal-sum-subsets/" target="_blank">LC 698</a> |
| *"Form a square using all matchsticks"* | 4 sides of equal length `total / 4`. Each matchstick used once. | 4-bucket packing backtracking (Special case of $K=4$). | <a href="https://leetcode.com/problems/matchsticks-to-square/" target="_blank">LC 473</a> |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Medium** | 3 |
| **Total Canonical Questions** | **3** |

---

## 🎯 Question Set (3 Canonical Questions)

### Q1. Increasing Subsequences
<a href="https://leetcode.com/problems/increasing-subsequences/" target="_blank">LeetCode 491</a> — **Medium**

**Target Skill:** Non-decreasing subsequence generation without input sorting.

**Core Reasoning:**
- Find all different non-decreasing subsequences of length $\ge 2$. Array has duplicates.
- **Why input cannot be sorted:** Sorting alters the original relative sequence order.
- In backtracking loop `i = start ... N-1`:
  - Condition: `nums[i] >= lastVal`.
  - Duplicate suppression without sorting: Use a `Set<Integer> usedInThisDepth = new HashSet<>()` local to the loop body at current recursive call!
  - If `usedInThisDepth.contains(nums[i])`, skip `nums[i]`.

**Why it belongs here:** Discovers that duplicate suppression when input CANNOT be sorted requires local depth set tracking.

**Complexity:** Time: $O(2^N)$, Space: $O(N)$.

---

### Q2. Partition to K Equal Sum Subsets
<a href="https://leetcode.com/problems/partition-to-k-equal-sum-subsets/" target="_blank">LeetCode 698</a> — **Medium**

**Target Skill:** $K$-bucket sum allocation with pruning optimizations.

**Core Reasoning:**
- Determine if array can be partitioned into $K$ subsets with equal sum `target = sum / K`.
- If `sum % K != 0` or max element $> target$, return `false`.
- **Pruning 1:** Sort `nums` descending (place larger numbers into buckets first to fail early).
- Backtracking: Try placing `nums[idx]` into bucket `b` ($0 \dots K-1$).
- **Pruning 2:** If bucket `b` is empty (`bucket[b] == 0`) and fails, skip subsequent empty buckets!

**Why it belongs here:** Classic bucket packing backtracking problem requiring heavy branch pruning.

**Complexity:** Time: $O(K^N)$, Space: $O(N)$.

---

### Q3. Matchsticks to Square
<a href="https://leetcode.com/problems/matchsticks-to-square/" target="_blank">LeetCode 473</a> — **Medium**

**Target Skill:** Perimeter side-length bucket packing ($K=4$).

**Core Reasoning:**
- Form a square using all matchsticks. Target side length = `sum / 4`.
- Equivalent to partitioning array into $K=4$ equal sum subsets.
- Sort matchsticks descending.
- Maintain `int[] sides = new int[4]`. Try adding matchstick `i` to side `0, 1, 2, 3`.

**Why it belongs here:** Shows how geometric perimeter constraints reduce to 4-bucket sum allocation.

**Complexity:** Time: $O(4^N)$, Space: $O(N)$.

---

## ⚡ Recognition Mastery Checklist

- [ ] When an array CANNOT be sorted, how do you prevent duplicate branch calls in backtracking? (Local `HashSet` per depth).
- [ ] Why is sorting array in DESCENDING order critical before bucket packing (`Matchsticks to Square`)? (Larger items fail invalid branches earlier!).
- [ ] Why does `bucket[b] == 0` allow skipping identical subsequent empty buckets?
