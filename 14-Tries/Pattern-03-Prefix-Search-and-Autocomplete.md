# Pattern 03 — Prefix Search & Autocomplete

Pattern 03 leverages the Trie as a **prefix-indexing and aggregation structure**. Because all words sharing a common prefix pass through the exact same node path, storing values or top suggestion lists at Trie nodes enables instant prefix aggregation and real-time autocomplete suggestions.

---

## Learning Progression

```text
Prefix State Storage ──► Subtree Prefix Sum Aggregation ──► Real-Time Lexicographic Suggestions
```

---

## Pattern Questions (2 Core Questions)

### 1. Map Sum Pairs (Understand & Aggregate)
- **Problem:** <a href="https://leetcode.com/problems/map-sum-pairs/" target="_blank">LeetCode 677</a>
- **Difficulty:** Medium
- **Core Idea:** Store sum values directly at Trie nodes during insertion for $O(L)$ prefix sum queries.
- **Decision / Mechanics:** Each `TrieNode` maintains an integer `val`.
  - When `insert(key, val)` is called, calculate `delta = val - existing_val`. Traverse the key path and add `delta` to `node.val` at every node along the prefix path.
  - For `sum(prefix)`, traverse to the prefix node and return `node.val` directly in $O(L)$ time!
- **Invariant / Proof Intuition:** Storing prefix sums directly on nodes during insertion converts prefix query aggregations from $O(\text{Subtree Size})$ traversal into $O(L)$ instant node lookups.
- **Recognition Clue:** Frequent prefix sum queries over a set of key-value string pairs.
- **Common Wrong Approach:** Traversing the full subtree under `prefix` on every `sum()` call, causing redundant work when prefix queries are frequent.
- **Complexity:** Time: `insert` $O(L)$, `sum` $O(L)$ where $L$ is length of key/prefix. Space: $O(N \times L)$.
- **Cross-Pattern Connection:** Connects Pattern 01 node architecture to Pattern 06 (Counting & Frequency State).

---

### 2. Search Suggestions System (Apply & Autocomplete)
- **Problem:** <a href="https://leetcode.com/problems/search-suggestions-system/" target="_blank">LeetCode 1268</a>
- **Difficulty:** Medium
- **Core Idea:** Store pre-sorted top 3 lexicographic suggestions at each Trie node.
- **Decision / Mechanics:** Sort `products` array lexicographically. Insert products into the Trie. At each node along a product's path, append the product to a `List<String> suggestions` list stored on the node (capping list size at 3).
  - For each character typed in `searchWord`, advance down the Trie. If node exists, return its pre-stored `suggestions` list; otherwise return an empty list.
- **Invariant / Proof Intuition:** Precomputed sorting combined with prefix node caching guarantees that typing each character instantly yields the top 3 lexicographical recommendations in $O(1)$ time after prefix traversal.
- **Recognition Clue:** Real-time type-ahead search suggestions returning top $K$ lexicographical matches after each character typed.
- **Common Wrong Approach:** Running full $O(N \log N)$ binary search or full subtree DFS after typing every single character.
- **Complexity:** Time: Build Trie $O(N \log N + N \times L)$, Search $O(L)$ where $L$ is length of `searchWord`. Space: $O(N \times L)$.
- **Cross-Pattern Connection:** Standard real-world autocomplete engine architecture.
