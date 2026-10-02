# Pattern 04 — Trie + DFS & Backtracking

Pattern 04 establishes the structural connection between **Tries** and **Backtracking**. In complex 2D matrix searches (such as Boggle or Word Search puzzles), searching for $N$ distinct words independently using standard DFS causes extreme exponential TLE ($O(N \times 4^L)$).

By inserting all target words into a **Trie**, the Trie serves as a **global pruning dictionary**: a single DFS traversal across matrix cells can check all target words simultaneously, pruning any path as soon as the cell sequence diverges from valid Trie prefixes!

---

## The Dual Responsibility Architecture

```text
       ┌─────────────────────────────────────────────────────────┐
       │                       Trie                              │
       │  • Acts as the Global Pruning Dictionary.              │
       │  • Instantly answers: "Is current prefix valid?"        │
       └─────────────────────────────────────────────────────────┘
                                   │
                                   ▼
       ┌─────────────────────────────────────────────────────────┐
       │                  DFS / Backtracking                     │
       │  • Explores 2D Grid / State Space.                      │
       │  • Backtracks when current path diverges from Trie.     │
       └─────────────────────────────────────────────────────────┘
```

---

## Canonical Ownership Reference (0 New Core Questions)

### Cross-Section Reference: Word Search II
- **Problem:** <a href="https://leetcode.com/problems/word-search-ii/" target="_blank">LeetCode 212</a>
- **Canonical Ownership:** Owned by **Section 09 (Recursion & Backtracking)**.
- **Difficulty:** Hard
- **Core Concept:** Trie-guided 2D Grid Backtracking with Node Pruning.
- **Why it belongs in Section 09:** The core difficulty lies in 2D cell visiting state management, grid boundary checks, and backtracking restoration. The Trie acts as an auxiliary pruning index.
- **Trie Integration Mechanics:**
  1. Build a Trie containing all dictionary words. Store the full word string at terminal nodes (`node.word = "apple"`).
  2. Perform DFS from each grid cell `(r, c)`.
  3. If current cell character is not a child of current `TrieNode`, **prune the search branch immediately**!
  4. **Trie Node Pruning Optimization**: After finding a word, detach leaf nodes to prevent duplicate findings and shrink the search space dynamically.

---

## Summary of Integration Rules

| Component | Responsibility | Performance Benefit |
| :--- | :--- | :--- |
| **Grid DFS** | 4-directional cell navigation + visited state | Explores physical board paths |
| **Trie Index** | Prefix validity check + instant word matching | Eliminates $O(N)$ repeated word searches |
| **Trie Leaf Pruning**| Detaches found terminal nodes from tree | Reduces search space as target words are discovered |
