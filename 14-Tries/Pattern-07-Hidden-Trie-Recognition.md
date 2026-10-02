# Pattern 07 — Hidden Trie Recognition

In technical coding interviews, Trie problems rarely state: *"Use a Prefix Tree."* They appear disguised as String Filtering, Dictionary Sentence Replacement, or Word Construction puzzles.

Hidden Trie Recognition tests your ability to **recognize the underlying prefix structure before choosing the data structure**.

---

## Hidden Trie Diagnostic Test

Ask these 4 diagnostic questions when encountering an unfamiliar string problem:

```text
1. Are there many words sharing common prefixes?
                        +
2. Is there a large fixed dictionary with repeated prefix lookups or replacements?
                        +
3. Can short prefixes eliminate searching through longer word suffixes?
                        +
4. Are words built character-by-character from smaller valid prefix components?

If YES to 2 or more questions  ==►  USE A TRIE
```

---

## Pattern Questions (2 Core Questions)

### 1. Replace Words (Understand & Replace)
- **Problem:** <a href="https://leetcode.com/problems/replace-words/" target="_blank">LeetCode 648</a>
- **Difficulty:** Medium
- **Core Idea:** Shortest root prefix matching using a Trie.
- **Decision / Mechanics:** Insert all dictionary roots into a Trie. Split the sentence into words. For each word, traverse down the Trie character by character. As soon as a node with `isEnd == true` is reached, return that root prefix immediately to replace the word; if no root prefix matches, keep the original word.
- **Invariant / Proof Intuition:** The shortest matching root prefix occurs at the first node with `isEnd == true` along the path. Traversing the Trie matches the shortest root in $O(L)$ time without checking longer redundant prefixes.
- **Recognition Clue:** Replacing sentence words with their shortest matching dictionary root prefixes.
- **Common Wrong Approach:** Searching every word against all dictionary roots using `String.startsWith()`, causing $O(W \times D \times L)$ time limit exceeded on large sentences.
- **Complexity:** Time: Build Trie $O(D \times L)$, Sentence Replacement $O(W \times L)$ where $W$ is sentence word count and $D$ is dictionary size. Space: $O(D \times L)$.
- **Cross-Pattern Connection:** Practical application of Pattern 01 (Trie Fundamentals) to sentence parsing.

---

### 2. Longest Word in Dictionary (Apply & Construct)
- **Problem:** <a href="https://leetcode.com/problems/longest-word-in-dictionary/" target="_blank">LeetCode 720</a>
- **Difficulty:** Medium
- **Core Idea:** Prefix-by-prefix word construction tracking valid terminal states.
- **Decision / Mechanics:** Insert all words into a Trie. Perform DFS/BFS traversal starting from root `0`. A child branch is valid to explore **only if `child.isEnd == true`** (meaning every prefix of the current word is also a valid word in the dictionary). Track the longest lexicographical word encountered.
- **Invariant / Proof Intuition:** Enforcing `child.isEnd == true` at every step of the traversal guarantees that any reachable word can be built one character at a time by other words in the dictionary.
- **Recognition Clue:** Finding the longest word formed by adding one character at a time, where every intermediate prefix must exist in the vocabulary.
- **Common Wrong Approach:** Sorting words by length and running set lookups for all prefixes, which performs duplicate substring work ($O(N \times L^2)$).
- **Complexity:** Time: $O(\sum L_i)$ build + traversal time. Space: $O(\sum L_i)$.
- **Cross-Pattern Connection:** Combines Pattern 01 (Trie Fundamentals) with Pattern 04 (Trie + DFS).
