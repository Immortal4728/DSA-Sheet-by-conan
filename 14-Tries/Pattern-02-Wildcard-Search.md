# Pattern 02 — Wildcard Search

Wildcard search transitions deterministic Trie traversal into **controlled DFS branching**. When non-deterministic wildcard characters (such as `'.'`) are encountered in a query, the search cannot follow a single character pointer; it must branch recursively across all valid non-null child nodes.

---

## Learning Progression

```text
Deterministic Traversal ──► Wildcard Branching '.' ──► Recursive DFS ──► Terminal State Pruning
```

---

## Deterministic vs Wildcard Traversal

```text
Deterministic Traversal (LC 208):
  Query 'c'  ──►  child = node.children['c' - 'a']  ──► Follow 1 pointer.

Wildcard Traversal (LC 211):
  Query '.'  ──►  Loop over all non-null children[0...25]  ──► Recurse DFS on each branch.
```

---

## Canonical Question Set (1 Core Question)

### 1. Design Add and Search Words Data Structure (Apply & Branching)
- **Problem:** <a href="https://leetcode.com/problems/design-add-and-search-words-data-structure/" target="_blank">LeetCode 211</a>
- **Difficulty:** Medium
- **Core Idea:** Trie traversal with DFS branching on wildcard characters (`'.'`).
- **Decision / Mechanics:** Store words in a standard Trie. For `search(word)`, iterate character by character. If `word[i] == '.'`, iterate over all 26 child nodes of `curr`; for each non-null child, recursively invoke `search(word, i + 1, child)`. Return `true` if any branch reaches terminal `isEnd == true`.
- **Invariant / Proof Intuition:** A wildcard `'.'` represents an OR-condition across all 26 possible character branches. Recursive DFS evaluates every valid candidate branch and prunes null paths early.
- **Recognition Clue:** Searching a dictionary with single-character wildcard masks or pattern matching operators.
- **Common Wrong Approach:** Attempting regex evaluation or brute-force string comparisons over a flat list ($O(N \times L)$ per query).
- **Complexity:** Time: `addWord` $O(L)$, `search` $O(L)$ for exact words / $O(26^L)$ worst-case for all-wildcard queries ` "....."`. Space: $O(N \times L)$.
- **Cross-Pattern Connection:** Connects Pattern 01 (Trie Fundamentals) to Pattern 04 (Trie + DFS / Backtracking).
