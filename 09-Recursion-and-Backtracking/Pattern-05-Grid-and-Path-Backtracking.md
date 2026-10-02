# Pattern 05: Grid & Path Backtracking

> Grid Backtracking explores paths on 2D matrices along 4 directional vectors (`Up`, `Down`, `Left`, `Right`). Cells visited along the current path are marked in-place and restored during backtracking.

---

## Why This Pattern Exists

Unlike 1D array traversals, 2D grid pathfinding allows moving in 4 directions. To prevent cyclic infinite loops (e.g., oscillating endlessly between $(r, c)$ and $(r, c+1)$):
1. **In-Place Marking (Choose):** Modify the current cell (e.g., `board[r][c] = '#'`) before recursing into neighbors.
2. **4-Directional Exploration (Recurse):** Check valid bounds and recurse into all 4 neighbors.
3. **In-Place Restoration (Undo):** Restore `board[r][c] = originalChar` after neighbor calls complete.

```text
Direction Vectors: int[][] DIRS = {{-1,0}, {1,0}, {0,-1}, {0,1}};

               (r - 1, c) [Up]
                   ▲
                   │
 (r, c - 1) ◄── (r, c) ──► (r, c + 1) [Right]
 [Left]            │
                   ▼
               (r + 1, c) [Down]
```

---

## ☕ Standard Java Templates

### In-Place Grid Backtracking Pattern
```java
private final int[][] DIRS = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};

private boolean dfs(char[][] board, int r, int c, String word, int idx) {
    if (idx == word.length()) return true; // Word fully matched!
    
    // Boundary check & character match check
    if (r < 0 || r >= board.length || c < 0 || c >= board[0].length || board[r][c] != word.charAt(idx)) {
        return false;
    }
    
    char temp = board[r][c];
    board[r][c] = '#'; // 1. MARK VISITED (Choose)
    
    for (int[] d : DIRS) {
        if (dfs(board, r + d[0], c + d[1], word, idx + 1)) {
            board[r][c] = temp; // Restore before returning true
            return true;
        }
    }
    
    board[r][c] = temp; // 2. RESTORE (Undo / Backtrack)
    return false;
}
```

---

## 🛑 Pattern Boundary & Ownership Rules

> **Canonical Ownership of Word Search II (<a href="https://leetcode.com/problems/word-search-ii/" target="_blank">LeetCode 212</a>):**
> LC 212 is housed **canonically in Pattern 05 of Section 09** because its primary learning objective is grid backtracking state interaction. Trie is used as a supporting prefix-pruning data structure. Later Trie sections must cross-reference LC 212 rather than duplicate it physically.

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Medium** | 2 |
| **Hard** | 2 |
| **Total Canonical Questions** | **4** |

---

## 🎯 Question Set (4 Canonical Questions)

### Q1. Word Search
<a href="https://leetcode.com/problems/word-search/" target="_blank">LeetCode 79</a> — **Medium**

**Target Skill:** In-place cell marking and 4-directional search backtracking.

**Core Reasoning:**
- Determine if string `word` exists in 2D character matrix `board`.
- For every cell matching `word[0]`, launch 4-directional DFS.
- Mark `board[r][c] = '#'`, recurse matching `idx + 1`, then restore `board[r][c] = original`.

**Why it belongs here:** Canonical foundation for 2D grid path backtracking.

**Complexity:** Time: $O(M \cdot N \cdot 3^L)$ (where $L$ is word length), Space: $O(L)$.

---

### Q2. Unique Paths III
<a href="https://leetcode.com/problems/unique-paths-iii/" target="_blank">LeetCode 980</a> — **Hard**

**Target Skill:** Hamiltonian path grid backtracking (visiting every non-obstacle cell exactly once).

**Core Reasoning:**
- Count paths from start cell (`1`) to end cell (`2`) that walk over **every non-obstacle cell** (`0`) exactly once.
- Count total non-obstacle cells `emptyCount`.
- Recurse 4 directions. Track `visitedCount`.
- At end cell (`2`): if `visitedCount == emptyCount + 1`, increment valid path count!
- Mark cell as visited (`-1`), recurse, then restore cell (`0`).

**Why it belongs here:** Classic grid Hamiltonian path traversal requiring exact cell coverage constraints.

**Complexity:** Time: $O(3^{M \cdot N})$, Space: $O(M \cdot N)$.

---

### Q3. Word Search II
<a href="https://leetcode.com/problems/word-search-ii/" target="_blank">LeetCode 212</a> — **Hard**

**Target Skill:** Grid backtracking combined with Trie prefix pruning and node optimization.

**Core Reasoning:**
- Given 2D board and array of `words`. Find all words present in grid.
- Searching each word independently takes $O(W \cdot M \cdot N \cdot 3^L)$ (TLE).
- **Trie + Grid Backtracking:** Build Trie from all words.
- Explore grid cells. At `board[r][c]`, move down Trie node. If current Trie node contains no child matching `board[r][c]`, **prune branch immediately**!
- Optimize: Remove matched words from Trie to prevent duplicate output matches.

**Why it belongs here:** **Canonical home for LC 212.** Quintessential hard grid backtracking problem demonstrating Trie prefix search pruning.

**Complexity:** Time: $O(M \cdot N \cdot 3^L)$, Space: $O(\sum L_{\text{words}})$.

---

### Q4. Path with Maximum Gold
<a href="https://leetcode.com/problems/path-with-maximum-gold/" target="_blank">LeetCode 1219</a> — **Medium**

**Target Skill:** Path value collection with localized start points and state restoration.

**Core Reasoning:**
- Collect maximum gold starting from any non-zero cell. Cannot visit cell with $0$ gold or re-visit cells.
- Launch DFS from every cell where `grid[r][c] > 0`.
- Mark `val = grid[r][c]`, set `grid[r][c] = 0`.
- Recurse 4 directions: `maxPath = val + max(dfs(r+dr, c+dc))`.
- Restore `grid[r][c] = val`. Update global max gold.

**Why it belongs here:** Demonstrates path sum accumulation over grid backtracking.

**Complexity:** Time: $O(K \cdot 3^L)$ (where $K$ is number of gold cells), Space: $O(L)$.

---

## ⚡ Mastery Checklist

- [ ] Can you implement 4-directional grid backtracking using in-place cell marking (`'#'` or `0`) without extra `boolean[][] visited` memory?
- [ ] Why does `Word Search II (LC 212)` use a Trie to prune grid backtracking branches?
- [ ] How does `Unique Paths III` verify that EVERY non-obstacle cell was visited along a valid path?
- [ ] What is the branching factor of grid backtracking? (At most $3$ because we never re-enter the cell we came from).
