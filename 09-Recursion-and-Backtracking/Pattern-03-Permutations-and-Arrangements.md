# Pattern 03: Permutations & Arrangements

> Permutations explore all possible **orderings** of a collection where sequence position matters (`[1, 2]` is DIFFERENT from `[2, 1]`). Rather than a forward-only `start` index, permutation backtracking uses a `visited[]` state array or in-place element swapping.

---

## Why This Pattern Exists

Unlike combinations where order is ignored:
- For an array of size $N$, there are **$N!$ permutations**.
- At every position in the output permutation, **ANY unvisited element** from the entire array can be selected.

```text
                  Root (Current = [])
            /              |              \
       Choose 1         Choose 2         Choose 3
       Visited: {1}     Visited: {2}     Visited: {3}
        /    \           /    \           /    \
     Choose 2 Choose 3 Choose 1 Choose 3 Choose 1 Choose 2
```

---

## ☕ Standard Java Templates

### 1. Visited Array Pattern (Permutations — LC 46)
```java
private void backtrack(int[] nums, boolean[] visited, List<Integer> current, List<List<Integer>> result) {
    if (current.size() == nums.length) {
        result.add(new ArrayList<>(current));
        return;
    }
    
    for (int i = 0; i < nums.length; i++) {
        if (visited[i]) continue; // Skip already used elements
        
        visited[i] = true;              // 1. CHOOSE
        current.add(nums[i]);
        
        backtrack(nums, visited, current, result); // 2. RECURSE
        
        current.remove(current.size() - 1); // 3. UNDO
        visited[i] = false;
    }
}
```

### 2. Duplicate Permutation Handling (Permutations II — LC 47)
```java
// Requires sorting array first!
Arrays.sort(nums);

// In loop:
if (visited[i]) continue;

// Skip duplicate element if its identical predecessor was NOT visited in this branch!
if (i > 0 && nums[i] == nums[i - 1] && !visited[i - 1]) continue;
```

> **Why `!visited[i-1]` suppresses duplicates:** It enforces that identical numbers are always chosen in left-to-right relative order along any branch, eliminating duplicate permutation decision trees.

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Subsets (LC 78)** | Order does not matter; uses `start` index — belongs to **Subsets & Combinations (Pattern 02)**. |
| **Letter Tile Possibilities** | Count of distinct sequences of all lengths — belongs to **Partitioning & Construction (Pattern 04)**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Medium** | 4 |
| **Total Canonical Questions** | **4** |

---

## 🎯 Question Set (4 Canonical Questions)

### Q1. Permutations
<a href="https://leetcode.com/problems/permutations/" target="_blank">LeetCode 46</a> — **Medium**

**Target Skill:** Basic $N!$ permutation decision tree generation using `visited[]` array.

**Core Reasoning:**
- Given array of distinct integers `nums`.
- Loop `i` from $0$ to $N-1$. If `!visited[i]`, mark `visited[i] = true`, add `nums[i]`, recurse, then unmark `visited[i] = false` and remove element.
- Base case: `current.size() == nums.length`.

**Why it belongs here:** Canonical entry problem for permutation backtracking.

**Complexity:** Time: $O(N \cdot N!)$, Space: $O(N)$.

---

### Q2. Permutations II
<a href="https://leetcode.com/problems/permutations-ii/" target="_blank">LeetCode 47</a> — **Medium**

**Target Skill:** Duplicate permutation filtering via sorting and `!visited[i-1]` skip logic.

**Core Reasoning:**
- Array `nums` contains duplicate values.
- Sort `nums`.
- Skip condition: `if (visited[i] || (i > 0 && nums[i] == nums[i - 1] && !visited[i - 1])) continue;`.

**Why it belongs here:** Quintessential duplicate suppression pattern for permutation trees.

**Complexity:** Time: $O(N \cdot N!)$, Space: $O(N)$.

---

### Q3. Letter Case Permutation
<a href="https://leetcode.com/problems/letter-case-permutation/" target="_blank">LeetCode 784</a> — **Medium**

**Target Skill:** Binary branching on character transformation.

**Core Reasoning:**
- Transform string characters by toggling letter cases (uppercase $\leftrightarrow$ lowercase).
- At index `idx`:
  - If character is a digit: keep as is, recurse `idx + 1`.
  - If character is a letter: Branch 1 (lowercase letter, recurse `idx + 1`), Branch 2 (uppercase letter, recurse `idx + 1`).
- Base case: `idx == s.length()`.

**Why it belongs here:** Demonstrates binary choice permutation decision trees ($2^K$ where $K$ is number of letters).

**Complexity:** Time: $O(N \cdot 2^K)$, Space: $O(N)$.

---

### Q4. Letter Combinations of a Phone Number
<a href="https://leetcode.com/problems/letter-combinations-of-a-phone-number/" target="_blank">LeetCode 17</a> — **Medium**

**Target Skill:** Multi-choice digit mapping permutation tree.

**Core Reasoning:**
- Map digits to letters (e.g., `'2' -> "abc"`, `'3' -> "def"`).
- At index `idx` in `digits`: lookup letters for `digits.charAt(idx)`.
- Loop through mapped letters: append letter to `StringBuilder`, recurse `idx + 1`, backtrack delete last char.

**Why it belongs here:** Classic multi-choice arrangement problem ($3^N \times 4^M$).

**Complexity:** Time: $O(4^N)$, Space: $O(N)$.

---

## ⚡ Mastery Checklist

- [ ] Why do Permutations use a `visited[]` array while Subsets use a `start` index?
- [ ] Can you explain why `if (i > 0 && nums[i] == nums[i-1] && !visited[i-1])` eliminates duplicate permutations?
- [ ] What is the time complexity of generating all permutations of $N$ distinct elements? ($O(N \cdot N!)$).
- [ ] Can you implement `Permutations II` from scratch in Java?
