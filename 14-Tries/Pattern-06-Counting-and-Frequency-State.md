# Pattern 06 — Counting & Frequency State

Standard Trie nodes store only character child pointers and a boolean `isEnd` flag. Pattern 06 teaches that **Trie nodes can store arbitrary metadata and frequency counters**, transforming the Trie into a dynamic frequency tracker capable of handle duplicate insertions, deletions, exact word counts, and prefix occurrence counts.

---

## Enhanced Trie Node Architecture

```java
class TrieNode {
    TrieNode[] children = new TrieNode[26];
    int wordCount = 0;   // Number of times an exact word ends at this node
    int prefixCount = 0; // Number of words in dictionary passing through this node
}
```

---

## State Maintenance Operations

1. **Insert**: For each character path traversed, increment `node.prefixCount++`. At the final character, increment `node.wordCount++`.
2. **CountWordsEqualTo**: Traverse to the exact word node. Return `node.wordCount`.
3. **CountWordsStartingWith**: Traverse to the prefix node. Return `node.prefixCount`.
4. **Erase**: Decrement `node.prefixCount--` along the path. Decrement `node.wordCount--` at terminal node.

---

## Canonical Question Set (1 Core Question)

### 1. Implement Trie II (Prefix Tree) (Understand & Maintain Frequency State)
- **Problem:** <a href="https://leetcode.com/problems/implement-trie-ii-prefix-tree/" target="_blank">LeetCode 1804</a>
- **Difficulty:** Medium
- **Core Idea:** Enhanced Trie node storing `wordCount` and `prefixCount` supporting insertion, deletion, and frequency queries.
- **Decision / Mechanics:** Implement `insert(word)`, `countWordsEqualTo(word)`, `countWordsStartingWith(prefix)`, and `erase(word)`.
  - Maintain `prefixCount` on every node along the insertion path.
  - Maintain `wordCount` on the terminal node.
  - On `erase(word)`, decrement `prefixCount` along path and `wordCount` at terminal node (pruning nodes when `prefixCount == 0`).
- **Invariant / Proof Intuition:** Incrementing `prefixCount` along insertion paths guarantees that any prefix node holds the exact count of all active dictionary words sharing that prefix, enabling $O(L)$ frequency lookups and dynamic deletions.
- **Recognition Clue:** Trie operations requiring word deletion, duplicate word frequency tracking, or prefix frequency counts.
- **Common Wrong Approach:** Relying solely on `boolean isEnd`, which cannot distinguish duplicate inserted words or track prefix frequencies upon deletion.
- **Complexity:** Time: `insert`, `countWordsEqualTo`, `countWordsStartingWith`, `erase` all take $O(L)$ where $L$ is word length. Space: $O(N \times L)$.
- **Cross-Pattern Connection:** Direct enhancement of Pattern 01 (Trie Fundamentals).
