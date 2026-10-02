# 🔁 Module 09: Recursion & Backtracking

> Master recursive state design, base-case boundaries, decision-tree exploration, choose/undo state restoration, pruning, and hidden backtracking recognition for SDE-1 interviews.

---

## 🎯 Section Objective & SDE-1 Scope

Recursion and Backtracking provide the foundational framework for exploring combinatorial search spaces.

This curriculum is locked at **29 canonical questions + 1 cross-section bridge/reference (LC 91)** organized across **7 pattern files**. It emphasizes systematic pattern recognition, recursive tree mental models, and production-grade Java implementation.

---

## 🧠 Why Recursion Comes Before Backtracking

- **Recursion:** Breaks a problem into smaller subproblems and combines their return values (e.g., `Fibonacci`, `Pow(x, n)`).
- **Backtracking:** An extension of recursion that incrementally builds a candidate solution, evaluates validity constraints, and **undoes (backtracks)** the last choice when reaching a dead end.

$$\text{Recursion (Subproblem Breakdown)} \longrightarrow \text{Backtracking (Choose } \to \text{ Recurse } \to \text{ Undo)}$$

---

## 🗺️ Curriculum Architecture

| Pattern File | Focus Area | Canonical Questions | Core Invariant / Mechanism |
|--------------|------------|---------------------|----------------------------|
| **[Pattern 01](./Pattern-01-Recursion-Fundamentals-and-Recursive-State.md)** | Recursion Fundamentals | 4 *(+1 Bridge)* | State definition, base cases, divide-and-conquer, DP bridge (LC 91) |
| **[Pattern 02](./Pattern-02-Subsets-and-Combinations.md)** | Subsets & Combinations | 6 | Order does NOT matter; forward `start` index, duplicate skip |
| **[Pattern 03](./Pattern-03-Permutations-and-Arrangements.md)** | Permutations & Arrangements | 4 | Order MATTERS ($N!$); `visited[]` array, `!visited[i-1]` duplicate skip |
| **[Pattern 04](./Pattern-04-Partitioning-and-Construction.md)** | Partitioning & Construction | 5 | Contiguous string cuts, prefix validity rules, math operator state |
| **[Pattern 05](./Pattern-05-Grid-and-Path-Backtracking.md)** | Grid & Path Backtracking | 4 | 4-directional 2D grid search, in-place cell marking (`'#'`), Trie pruning |
| **[Pattern 06](./Pattern-06-Constraint-Satisfaction.md)** | Constraint Satisfaction | 3 | $O(1)$ constraint arrays (`cols`, `diag1`, `diag2`), N-Queens, Sudoku |
| **[Pattern 07](./Pattern-07-Hidden-Backtracking-Recognition.md)** | Hidden Recognition | 3 | Diagnostic recognition: multiple choices + undo $\to$ Backtracking |

---

## 🔑 Subsets vs Combinations vs Permutations

| Pattern | Order Sensitivity | State Mechanism | Formula / Complexity |
|---------|-------------------|-----------------|----------------------|
| **Subsets** | Order ignored (`[1,2] == [2,1]`) | `start` index $\to i + 1$ | $2^N$ |
| **Combinations** | Order ignored, fixed size $K$ | `start` index, `size() == K` | $\binom{N}{K}$ |
| **Permutations** | Order matters (`[1,2] != [2,1]`) | `boolean[] visited` array | $N!$ |

---

## 🔒 Canonical Placement & Cross-Section Ownership

To preserve single canonical ownership across the repository:
- **LC 91 (Decode Ways):** Referenced in **Pattern 01** as a bridge showing when recursion with overlapping subproblems becomes Dynamic Programming. Canonical home belongs to **Dynamic Programming**.
- **LC 212 (Word Search II):** Housed canonically in **Pattern 05 (Grid Backtracking)** because the core learning objective is search-state grid interaction. Later **Tries** sections cross-reference LC 212.

---

## 📑 Full Question Matrix (29 Canonical + 1 Bridge)

### Pattern 01: Recursion Fundamentals & Recursive State (4 Canonical + 1 Bridge)
1. <a href="https://leetcode.com/problems/fibonacci-number/" target="_blank">LeetCode 509: Fibonacci Number</a> — **Easy**
2. <a href="https://leetcode.com/problems/powx-n/" target="_blank">LeetCode 50: Pow(x, n)</a> — **Medium**
3. <a href="https://leetcode.com/problems/k-th-symbol-in-grammar/" target="_blank">LeetCode 779: K-th Symbol in Grammar</a> — **Medium**
4. <a href="https://leetcode.com/problems/different-ways-to-add-parentheses/" target="_blank">LeetCode 241: Different Ways to Add Parentheses</a> — **Medium**
- 🌉 *Cross-Section Reference:* <a href="https://leetcode.com/problems/decode-ways/" target="_blank">LeetCode 91: Decode Ways</a> *(Canonical in DP)*

### Pattern 02: Subsets & Combinations (6 Canonical Questions)
1. <a href="https://leetcode.com/problems/subsets/" target="_blank">LeetCode 78: Subsets</a> — **Medium**
2. <a href="https://leetcode.com/problems/subsets-ii/" target="_blank">LeetCode 90: Subsets II</a> — **Medium**
3. <a href="https://leetcode.com/problems/combinations/" target="_blank">LeetCode 77: Combinations</a> — **Medium**
4. <a href="https://leetcode.com/problems/combination-sum/" target="_blank">LeetCode 39: Combination Sum</a> — **Medium**
5. <a href="https://leetcode.com/problems/combination-sum-ii/" target="_blank">LeetCode 40: Combination Sum II</a> — **Medium**
6. <a href="https://leetcode.com/problems/combination-sum-iii/" target="_blank">LeetCode 216: Combination Sum III</a> — **Medium**

### Pattern 03: Permutations & Arrangements (4 Canonical Questions)
1. <a href="https://leetcode.com/problems/permutations/" target="_blank">LeetCode 46: Permutations</a> — **Medium**
2. <a href="https://leetcode.com/problems/permutations-ii/" target="_blank">LeetCode 47: Permutations II</a> — **Medium**
3. <a href="https://leetcode.com/problems/letter-case-permutation/" target="_blank">LeetCode 784: Letter Case Permutation</a> — **Medium**
4. <a href="https://leetcode.com/problems/letter-combinations-of-a-phone-number/" target="_blank">LeetCode 17: Letter Combinations of a Phone Number</a> — **Medium**

### Pattern 04: Partitioning & Recursive Construction (5 Canonical Questions)
1. <a href="https://leetcode.com/problems/generate-parentheses/" target="_blank">LeetCode 22: Generate Parentheses</a> — **Medium**
2. <a href="https://leetcode.com/problems/palindrome-partitioning/" target="_blank">LeetCode 131: Palindrome Partitioning</a> — **Medium**
3. <a href="https://leetcode.com/problems/restore-ip-addresses/" target="_blank">LeetCode 93: Restore IP Addresses</a> — **Medium**
4. <a href="https://leetcode.com/problems/letter-tile-possibilities/" target="_blank">LeetCode 1079: Letter Tile Possibilities</a> — **Medium**
5. <a href="https://leetcode.com/problems/expression-add-operators/" target="_blank">LeetCode 282: Expression Add Operators</a> — **Hard**

### Pattern 05: Grid & Path Backtracking (4 Canonical Questions)
1. <a href="https://leetcode.com/problems/word-search/" target="_blank">LeetCode 79: Word Search</a> — **Medium**
2. <a href="https://leetcode.com/problems/unique-paths-iii/" target="_blank">LeetCode 980: Unique Paths III</a> — **Hard**
3. <a href="https://leetcode.com/problems/word-search-ii/" target="_blank">LeetCode 212: Word Search II</a> — **Hard**
4. <a href="https://leetcode.com/problems/path-with-maximum-gold/" target="_blank">LeetCode 1219: Path with Maximum Gold</a> — **Medium**

### Pattern 06: Constraint Satisfaction (3 Canonical Questions)
1. <a href="https://leetcode.com/problems/n-queens/" target="_blank">LeetCode 51: N-Queens</a> — **Hard**
2. <a href="https://leetcode.com/problems/n-queens-ii/" target="_blank">LeetCode 52: N-Queens II</a> — **Hard**
3. <a href="https://leetcode.com/problems/sudoku-solver/" target="_blank">LeetCode 37: Sudoku Solver</a> — **Hard**

### Pattern 07: Hidden Backtracking Recognition (3 Canonical Questions)
1. <a href="https://leetcode.com/problems/increasing-subsequences/" target="_blank">LeetCode 491: Increasing Subsequences</a> — **Medium**
2. <a href="https://leetcode.com/problems/partition-to-k-equal-sum-subsets/" target="_blank">LeetCode 698: Partition to K Equal Sum Subsets</a> — **Medium**
3. <a href="https://leetcode.com/problems/matchsticks-to-square/" target="_blank">LeetCode 473: Matchsticks to Square</a> — **Medium**

---

## ⚡ SDE-1 Interview Mastery Checklist

- [ ] Define the recursive state and base cases before typing code.
- [ ] Implement loop-based combination backtracking using a `start` index.
- [ ] Implement permutation backtracking using a `visited[]` array.
- [ ] Filter duplicate branches using sorting and `if (i > start && nums[i] == nums[i-1]) continue;`.
- [ ] Perform in-place 2D grid marking and restoration (`board[r][c] = '#'`).
- [ ] Maintain $O(1)$ diagonal constraint arrays for N-Queens.
- [ ] Recognize when a decision tree problem requires dynamic choice undoing.
