# Section 14 — Tries (Prefix Trees)

A **Trie** (derived from "retrieval") is a tree-based data structure used to store and retrieve keys in a string dataset efficiently. By sharing common character prefixes across words, Tries reduce space redundancy and enable $O(L)$ prefix queries independent of the total dictionary size.

---

## 1. Section Purpose & Mental Model

### Why Tries Exist
HashMap lookups provide $O(1)$ time for exact string matches. However, HashMaps cannot perform **prefix searches** or **autocomplete suggestions** without scanning all keys ($O(N \times L)$). 

A Trie organizes strings by character paths, allowing instant $O(L)$ prefix matching, wildcard branching, and prefix frequency aggregation.

```text
                  (Root Node)  [isEnd = false]
                 /     |     \
               'a'    'b'    'c'
               /       |       \
            [node]   [node]   [node]
```

### Standard Trie Node Definition
```java
class TrieNode {
    TrieNode[] children = new TrieNode[26];
    boolean isEnd = false;
}
```

### When to Choose a Trie
- Repeated prefix queries (`startsWith`, `mapSum`, `autocomplete`).
- Searching with wildcard characters (e.g., `.` matching any character).
- Replacing words in a sentence with their shortest matching root prefixes.
- Bitwise Maximum XOR queries over integer arrays.
- Building words character-by-character from dictionary prefixes.

### When NOT to Choose a Trie
- Exact string key-value lookups only (Use a `HashMap` — simpler $O(1)$ lookup).
- Small static dictionary with rare queries (Use `HashSet` or Array Binary Search).
- Searching for arbitrary substrings/substring matching (Use KMP / Z-Algorithm or Suffix structures).

---

## 2. Fresher / SDE-1 Scope Rules

This section is strictly tuned for **Fresher / SDE-1 interview success**. It intentionally avoids competitive-programming filler that has zero SDE-1 interview relevance:
- ❌ **Excluded**: Suffix Trees, Suffix Arrays, Suffix Automata, Aho–Corasick Algorithm, Ternary Search Trees, Radix / Patricia Trees, Heavy Contest XOR tricks.

---

## 3. Final Architecture & Pattern Navigation

This module contains **8 canonical core questions + 1 selective/advanced question**, organized across 7 structured pattern files:

```text
14-Tries/
├── Pattern-01-Trie-Fundamentals.md
├── Pattern-02-Wildcard-Search.md
├── Pattern-03-Prefix-Search-and-Autocomplete.md
├── Pattern-04-Trie-DFS-and-Backtracking.md
├── Pattern-05-Bitwise-Trie-and-XOR.md
├── Pattern-06-Counting-and-Frequency-State.md
├── Pattern-07-Hidden-Trie-Recognition.md
└── README.md
```

### Question Distribution Summary

| Pattern File | Canonical Core Questions | Selective / Advanced | Core Focus & Mechanics |
| :--- | :--- | :--- | :--- |
| **01 — Trie Fundamentals** | 1 (<a href="https://leetcode.com/problems/implement-trie-prefix-tree/" target="_blank">LeetCode 208</a>) | — | Baseline Trie structure: `insert`, `search`, `startsWith` |
| **02 — Wildcard Search** | 1 (<a href="https://leetcode.com/problems/design-add-and-search-words-data-structure/" target="_blank">LeetCode 211</a>) | — | Wildcard `.` branching via recursive DFS |
| **03 — Prefix Search & Autocomplete** | 2 (<a href="https://leetcode.com/problems/map-sum-pairs/" target="_blank">LeetCode 677</a>, <a href="https://leetcode.com/problems/search-suggestions-system/" target="_blank">LeetCode 1268</a>) | — | Storing prefix sums & pre-sorted top 3 suggestions at nodes |
| **04 — Trie + DFS & Backtracking** | 0 (LC 212 Cross-ref) | — | Global pruning index for 2D grid backtracking |
| **05 — Bitwise Trie & XOR** | 1 (<a href="https://leetcode.com/problems/maximum-xor-of-two-numbers-in-an-array/" target="_blank">LeetCode 421</a>) | 1 (<a href="https://leetcode.com/problems/maximum-xor-with-an-element-from-array/" target="_blank">LeetCode 1707</a>) | Binary Trie (31 bits) for greedy opposite-bit Max XOR |
| **06 — Counting & Frequency State** | 1 (<a href="https://leetcode.com/problems/implement-trie-ii-prefix-tree/" target="_blank">LeetCode 1804</a>) | — | Trie nodes storing `wordCount` & `prefixCount` |
| **07 — Hidden Trie Recognition** | 2 (<a href="https://leetcode.com/problems/replace-words/" target="_blank">LeetCode 648</a>, <a href="https://leetcode.com/problems/longest-word-in-dictionary/" target="_blank">LeetCode 720</a>) | — | Shortest root replacement & prefix-by-prefix word construction |
| **TOTAL** | **8 Core Questions** | **1 Selective Question** | — |

---

## 4. Bitwise Trie Bridge to Bit Manipulation

A **Bitwise Trie** treats 32-bit integers as binary bit strings (`0` or `1`).
To maximize $A \oplus B$, we insert numbers into a 2-child Binary Trie (`children[0]` and `children[1]`). For each bit of $A$ from MSB to LSB, we greedily follow the **opposite bit branch** (`1 - bit`). This acts as the direct bridge from **Tries (Section 14)** to **Bit Manipulation (Section 15)**.

---

## 5. Canonical Ownership & Cross-Section Rules

- **Section 09 — Recursion & Backtracking**: Canonically owns <a href="https://leetcode.com/problems/word-search-ii/" target="_blank">LeetCode 212 (Word Search II)</a>. Pattern 04 cross-references LC 212 to explain how Tries serve as pruning dictionaries during grid DFS, without duplicating the problem.
- **Section 02 — Strings**: Maintains ownership of general substring, string processing, and pattern matching algorithms (KMP).
- **Section 03 — Hashing**: Retains standard dictionary and exact key lookup problems.
- **Section 15 — Bit Manipulation**: Takes ownership of general bitwise arithmetic, bit masks, and bitwise tricks.

---

## 6. Hidden Trie Recognition Strategy

Train yourself to ask these diagnostic questions:
1. Is there a large fixed dictionary with repeated prefix lookups or replacements?
2. Can short root prefixes eliminate searching through longer redundant suffixes?
3. Are words built character-by-character from smaller valid prefix components in the vocabulary?

---

## Mastery Criteria

The section is mastered when you can:

1. **Implement a Trie from scratch** using a clean `TrieNode` class.
2. **Implement `insert`, `search`, and `startsWith`** cleanly in $O(L)$ time.
3. **Handle wildcard branching** (`.`) using controlled recursive DFS.
4. **Perform prefix-based queries** and store node-level aggregation state.
5. **Understand real-time autocomplete traversal** and suggestion caching.
6. **Store counts and metadata in nodes** (`wordCount`, `prefixCount`).
7. **Understand Trie + DFS / Backtracking integration** for grid search pruning.
8. **Implement a Binary Trie for Max XOR** problems ($O(31 \times N)$).
9. **Identify Trie opportunities** without being told the data structure.
10. **Explain Trie vs HashMap tradeoffs** (exact $O(1)$ vs prefix $O(L)$).
11. **Explain Trie vs sorting tradeoffs** ($O(N \log N)$ vs $O(N \times L)$).
12. **State time and space complexity** accurately for all Trie operations.
13. **Adapt the Trie node structure** to problem-specific requirements.
14. **Solve core Trie problems from a blank editor** in Java.
