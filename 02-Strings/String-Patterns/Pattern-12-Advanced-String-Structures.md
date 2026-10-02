# Pattern 12: Advanced String Structures

> A HashMap gives you $O(1)$ exact lookups, but it is blind to the internal structure of strings. When many strings share a common prefix and you need to exploit that shared structure, a Trie (Prefix Tree) eliminates the redundant re-scanning that a HashMap cannot prevent.

---

## 🔍 Beginner Audit: Why Is a HashMap Not Enough?

With a `HashSet`, every lookup for `"apple"` costs $O(|\text{apple}|) = O(5)$ — all 5 characters must be hashed. Looking up `"app"` separately costs another $O(3)$. The `"a-p-p"` prefix is re-processed twice with no shared work.

With a **Trie**, the nodes `a → p → p` are shared. `"apple"` and `"app"` diverge only after the 3rd character. Any subsequent prefix query (`"ap"`, `"app"`, `"appl"`) reuses those already-built nodes.

```text
HashSet:   "apple" ─┐
           "app"   ─┤  → Each stored independently. No prefix sharing.
           "apt"   ─┘

Trie:      root
             └── 'a'
                  └── 'p'
                       ├── 'p' [end]         ← "app"
                       │    └── 'l'
                       │         └── 'e' [end] ← "apple"
                       └── 't' [end]         ← "apt"
```

> **When to Use a Trie:** When many strings must be searched/compared **by prefix**, not by exact match. Exact match → HashSet. Prefix queries → Trie.

---

## Core Technical Intuition — TrieNode Structure

Every Trie consists of nodes. Each node represents **one character in a path from root**:

```text
class TrieNode {
    TrieNode[] children = new TrieNode[26]; // one slot per letter a-z
    boolean isEnd = false;                  // true if a word ends here
}
```

**Three Core Operations:**
1. **Insert:** Walk character by character from root. Create nodes where they don't exist. Mark `isEnd = true` on the last character.
2. **Search:** Walk character by character. Return `false` if any node is missing. Return `isEnd` at the final node.
3. **StartsWith:** Same as Search but return `true` even if `isEnd` is false — only the path matters.

---

## ☕ Standard Java Code Templates

### Template 1: Basic Trie Implementation (Insert, Search, StartsWith)
```java
class Trie {
    private TrieNode root = new TrieNode();

    public void insert(String word) {
        TrieNode node = root;
        for (char c : word.toCharArray()) {
            if (node.children[c - 'a'] == null)
                node.children[c - 'a'] = new TrieNode();
            node = node.children[c - 'a'];
        }
        node.isEnd = true;
    }

    public boolean search(String word) {
        TrieNode node = root;
        for (char c : word.toCharArray()) {
            if (node.children[c - 'a'] == null) return false;
            node = node.children[c - 'a'];
        }
        return node.isEnd;
    }

    public boolean startsWith(String prefix) {
        TrieNode node = root;
        for (char c : prefix.toCharArray()) {
            if (node.children[c - 'a'] == null) return false;
            node = node.children[c - 'a'];
        }
        return true; // don't need isEnd — any path continuation counts
    }
}
```

### Template 2: DFS from a Trie Node (Autocomplete / Search Suggestions)
```java
// Use when: collecting words reachable from a given prefix node
private void dfs(TrieNode node, String prefix, List<String> result, int limit) {
    if (result.size() == limit) return;
    if (node.isEnd) result.add(prefix);
    for (int i = 0; i < 26; i++) {
        if (node.children[i] != null)
            dfs(node.children[i], prefix + (char)('a' + i), result, limit);
    }
}
```

### Template 3: Wildcard Search with DFS Backtracking
```java
// Use when: '.' can match any character (LeetCode 211)
private boolean searchInTrie(TrieNode node, String word, int index) {
    if (index == word.length()) return node.isEnd;
    char c = word.charAt(index);
    if (c != '.') {
        TrieNode next = node.children[c - 'a'];
        return next != null && searchInTrie(next, word, index + 1);
    } else {
        for (TrieNode child : node.children) { // try ALL children
            if (child != null && searchInTrie(child, word, index + 1))
                return true;
        }
        return false;
    }
}
```

### Template 4: Bitwise Trie (XOR Maximization)
```java
// Use when: finding max XOR — insert 32-bit numbers bit by bit, traverse opposite bit
class BitTrie {
    private int[][] trie = new int[32 * 100001][2];
    private int size = 1;

    public void insert(int num) {
        int node = 0;
        for (int i = 31; i >= 0; i--) {
            int bit = (num >> i) & 1;
            if (trie[node][bit] == 0) trie[node][bit] = size++;
            node = trie[node][bit];
        }
    }

    public int maxXOR(int num) {
        int node = 0, xor = 0;
        for (int i = 31; i >= 0; i--) {
            int bit = (num >> i) & 1;
            int opposite = 1 - bit; // take opposite bit to maximize XOR
            if (trie[node][opposite] != 0) {
                xor |= (1 << i);
                node = trie[node][opposite];
            } else {
                node = trie[node][bit];
            }
        }
        return xor;
    }
}
```

---

## 🔍 Step-by-Step Trace Table (Dry Run)

Tracing **Replace Words (`LeetCode 648`)** with dictionary `["cat","bat","rat"]` and sentence `"the cattle was rattled"`:

| Word | Trie Traversal | Hit `isEnd` at? | Output |
|------|---------------|-----------------|--------|
| `"the"` | `t→h→e` — no `isEnd` hit | — (no root in dict) | `"the"` |
| `"cattle"` | `c→a→t` → `isEnd = true` at `"cat"` | `"cat"` | `"cat"` |
| `"was"` | `w` — not in trie | — | `"was"` |
| `"rattled"` | `r→a→t` → `isEnd = true` at `"rat"` | `"rat"` | `"rat"` |

**Result:** `"the cat was rat"`

---

## Pattern Recognition Layer

```text
Am I performing exact-match lookups only?
        ↓ No pattern-sharing needed → Use HashSet/HashMap (O(1) per lookup)

Am I performing repeated PREFIX-based lookups across many strings?
        ↓ Yes → Trie. Shared prefix nodes avoid re-processing common beginnings.

Am I finding autocomplete suggestions (top-K words with a given prefix)?
        ↓ Yes → Insert all words into Trie. Navigate to prefix node. DFS to collect words.

Am I replacing words with their shortest dictionary root/prefix?
        ↓ Yes → Insert roots into Trie. For each word, traverse and stop at first isEnd.

Am I searching for words with wildcard characters ('.')?
        ↓ Yes → Trie + DFS backtracking: on '.', recurse into ALL non-null children.

Am I maximizing XOR between pairs of integers?
        ↓ Yes → Bitwise Trie: insert binary representations, traverse opposite bits greedily.

Am I searching a grid for multiple words simultaneously?
        ↓ Yes → Insert all words into Trie. Run DFS on grid simultaneously traversing Trie.
               → Any grid path without a corresponding Trie branch is pruned immediately.
```

---

## Core Mental Models

- **Shared Prefix Invariant:** *"Any two strings sharing a common prefix share a path from the root. The more strings share that path, the greater the savings over storing them independently in a HashMap."*
- **isEnd is a Marker, Not a Blocker:** *"Reaching a node where `isEnd == true` doesn't stop traversal. For `startsWith`, you continue. For `replace-words`, you stop. The behavior on `isEnd` is determined by the problem, not by the Trie."*
- **Wildcard Means Branch:** *"The `.` wildcard cannot follow a single path. You must branch into all non-null children and check all possibilities. This is DFS on the Trie — not linear traversal."*
- **Bitwise Trie = Trie on Bits:** *"The exact same structure that processes characters `a-z` (26 children) can process bits `0-1` (2 children). This enables greedy XOR maximization by always trying to take the opposite bit."*

---

## 🛑 Pattern Boundary

| Problem Requirement | Move To Pattern |
|---------------------|-----------------|
| Pure exact-match lookups | **HashMap / HashSet** — $O(1)$ vs Trie's $O(L)$ |
| Single pattern search in one text | **Pattern #11 — String Pattern Matching (KMP)** |
| Finding all occurrences of multiple patterns simultaneously | **Aho-Corasick (beyond SDE scope)** |
| Edit distance between strings | **String DP** |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 0 |
| **Medium** | 4 |
| **Hard** | 2 |
| **Total** | **6** |

---

## 🎯 Question Progression (Curated 6-Question Set)

### 1. Foundation (1 Question)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 1 | Implement Trie (Prefix Tree) | <a href="https://leetcode.com/problems/implement-trie-prefix-tree/" target="_blank">LeetCode 208</a> | Medium | Build a Trie from scratch: `TrieNode` with 26 children + `isEnd`. Implement `insert`, `search`, `startsWith`. |

---

### 2. Core (2 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 2 | Search Suggestions System | <a href="https://leetcode.com/problems/search-suggestions-system/" target="_blank">LeetCode 1268</a> | Medium | Navigate to a prefix node, then DFS to collect the top 3 lexicographically smallest matching words. |
| 3 | Replace Words | <a href="https://leetcode.com/problems/replace-words/" target="_blank">LeetCode 648</a> | Medium | Insert dictionary roots; for each sentence word, traverse the Trie and stop at the first `isEnd` node. |

---

### 3. Advanced (2 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 4 | Design Add and Search Words Data Structure | <a href="https://leetcode.com/problems/design-add-and-search-words-data-structure/" target="_blank">LeetCode 211</a> | Medium | Wildcard search: on `'.'`, recursively try all non-null children (DFS backtracking on Trie). |
| 5 | Maximum XOR of Two Numbers in an Array | <a href="https://leetcode.com/problems/maximum-xor-of-two-numbers-in-an-array/" target="_blank">LeetCode 421</a> | Medium | Bitwise Trie: insert 32-bit binary representations; greedily take the opposite bit at each level. |

---

### 4. Interview Recognition — Pattern Hidden

| # | Problem | LeetCode Link | Difficulty | Objective |
|---|---------|---------------|------------|-----------|
| 6 | Word Search II | <a href="https://leetcode.com/problems/word-search-ii/" target="_blank">LeetCode 212</a> | Hard | Given a grid of characters and a dictionary of words, find all words that can be formed by connected adjacent cells. |

<details>
<summary>💡 Reveal Pattern Hint (Click after attempting from a blank editor)</summary>

- **Problem 6:** Running a separate DFS for each word is $O(W \times \text{GridDFS})$ — too slow when W is large and words share prefixes. Instead, insert all words into a Trie. Run a single DFS on the grid. At each cell, advance the Trie by one character. If the current grid character has no corresponding Trie child, **prune immediately** — no words with this prefix exist. If `isEnd` is hit, record the word. Classification: **Trie + Grid DFS with Shared-Prefix Pruning**.
</details>

---

## 🏆 Mastery Criteria

You have mastered **Pattern #12** when you can:

- [ ] Implement a `TrieNode` class with `children[26]` and `isEnd` from memory.
- [ ] Implement `insert`, `search`, and `startsWith` without reference.
- [ ] Write a DFS from a given prefix node to collect words.
- [ ] Implement wildcard search with DFS backtracking on `.` characters.
- [ ] Explain the Bitwise Trie structure and why opposite-bit traversal maximizes XOR.
- [ ] Recognize when prefix sharing fundamentally changes the complexity of a multi-string search problem.
- [ ] Implement all 6 solutions from a blank editor without tutorial dependence.

---

## 🎉 String Curriculum Complete

You have completed all 12 String Patterns. The complete curriculum builds progressively:

| Phase | Patterns | What Was Built |
|-------|----------|----------------|
| **Foundation** | 1–2 | Character-level access, frequency state management |
| **Core Mechanics** | 3–5 | Structural symmetry, pointer invariants, window states |
| **Application** | 6–8 | Anagram reasoning, construction, sequence classification |
| **Advanced State** | 9–10 | LIFO resolution, rule-governed simulation |
| **Algorithmic Depth** | 11–12 | Prefix/suffix structures, shared prefix exploitation |

Return to the **[String Patterns README](../README.md)** to review your progress checklist.
