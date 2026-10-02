# Pattern 01 — Trie Fundamentals

A **Trie** (or Prefix Tree) is a tree-like search data structure used to store a dynamic set of strings where keys are usually strings. Unlike a binary search tree, no node in the tree stores the key associated with that node; instead, its position in the tree defines the key it is associated with.

---

## Learning Progression

```text
Node Structure Definition ──► Character Path Traversal ──► Terminal State Tracking ──► Prefix vs Word Validation
```

---

## Core Concept & Node Architecture

```text
                  (Root Node)  [isEnd = false]
                 /     |     \
               'a'    'b'    'c'
               /       |       \
            [node]   [node]   [node]
```

### Standard Trie Node Definition (26-Child Array)
```java
class TrieNode {
    TrieNode[] children = new TrieNode[26];
    boolean isEnd = false;
}
```

### Key Fundamental Operations
1. **Insert**: Traverse character by character. Instantiate missing child nodes (`children[ch - 'a'] = new TrieNode()`). Mark `isEnd = true` at terminal character.
2. **Search**: Traverse character path. Return `true` if and only if final node exists AND `isEnd == true`.
3. **StartsWith**: Traverse character path. Return `true` if final node exists (regardless of `isEnd`).

---

## Canonical Question Set (1 Core Question)

### 1. Implement Trie (Prefix Tree) (Understand & Core Foundation)
- **Problem:** <a href="https://leetcode.com/problems/implement-trie-prefix-tree/" target="_blank">LeetCode 208</a>
- **Difficulty:** Medium
- **Core Idea:** Fundamental Trie data structure construction with character pointer arrays.
- **Decision / Mechanics:** Implement `insert(word)`, `search(word)`, and `startsWith(prefix)` using `TrieNode[] children` of size 26 and a boolean `isEnd` flag.
- **Invariant / Proof Intuition:** Sharing common character prefixes across words reduces total string storage memory and guarantees $O(L)$ search time independent of dictionary size $N$.
- **Recognition Clue:** Need fast $O(L)$ prefix lookup, autocomplete, or word insertion over a fixed alphabet dictionary.
- **Common Wrong Approach:** Storing words in a HashSet (`Set<String>`), which provides $O(1)$ exact lookup but fails to support $O(L)$ prefix matching (`startsWith`).
- **Complexity:** Time: Insert $O(L)$, Search $O(L)$, StartsWith $O(L)$ where $L$ is word length. Space: $O(N \times L \times \Sigma)$ where $\Sigma = 26$.
- **Cross-Pattern Connection:** Foundational node architecture for all subsequent Trie patterns.
