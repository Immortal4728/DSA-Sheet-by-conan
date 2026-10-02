# Pattern 06: Constraint Satisfaction

> Constraint Satisfaction Problems (CSPs) place items into a grid or structure under strict global placement rules. Fast constraint validation (using boolean tracking arrays or bitmasks) enables early pruning of invalid branches.

---

## Why This Pattern Exists

Unlike decision-tree problems where choices are independent, CSPs impose tight mutual exclusion constraints:
- **N-Queens:** No two queens can share the same row, column, or diagonal.
- **Sudoku:** Every number $1 \dots 9$ must appear exactly once in each row, column, and $3 \times 3$ sub-box.

To explore these massive combinatorial search spaces in real time, we maintain **$O(1)$ Constraint Tracking Arrays**:
- **Columns:** `cols[col]`
- **Main Diagonal ($r - c$):** `diag1[r - c + N]`
- **Anti-Diagonal ($r + c$):** `diag2[r + c]`

```text
               Row-by-Row Search Space Exploration
                               │
            Is Column / Diagonals Safe? (O(1) check)
                               │
            ┌──────────────────┴──────────────────┐
            ▼                                     ▼
        [ SAFE ]                              [ UNSAFE ]
   - Place Queen                         - Prune Branch (Backtrack immediately)
   - Mark cols & diags
   - Recurse row + 1
   - Unmark (Undo)
```

---

## ☕ Standard Java Templates

### N-Queens Fast Constraint Tracking Pattern (LC 51 / LC 52)
```java
private boolean[] cols, diag1, diag2;
private int solutionCount = 0;

public int totalNQueens(int n) {
    cols = new boolean[n];
    diag1 = new boolean[2 * n];
    diag2 = new boolean[2 * n];
    solve(0, n);
    return solutionCount;
}

private void solve(int row, int n) {
    if (row == n) {
        solutionCount++; // All rows filled legally!
        return;
    }
    
    for (int col = 0; col < n; col++) {
        int d1 = row - col + n;
        int d2 = row + col;
        
        // O(1) Constraint Check
        if (cols[col] || diag1[d1] || diag2[d2]) continue;
        
        // 1. PLACE QUEEN & MARK CONSTRAINTS
        cols[col] = true; diag1[d1] = true; diag2[d2] = true;
        
        solve(row + 1, n); // 2. RECURSE TO NEXT ROW
        
        // 3. UNDO & RESTORE CONSTRAINTS
        cols[col] = false; diag1[d1] = false; diag2[d2] = false;
    }
}
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Unique Paths III** | Cell coverage pathfinding — belongs to **Grid Backtracking (Pattern 05)**. |
| **Partition to K Equal Sum Subsets** | Numeric bucket packing — belongs to **Hidden Backtracking (Pattern 07)**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Hard** | 3 |
| **Total Canonical Questions** | **3** |

---

## 🎯 Question Set (3 Canonical Questions)

### Q1. N-Queens
<a href="https://leetcode.com/problems/n-queens/" target="_blank">LeetCode 51</a> — **Hard**

**Target Skill:** Solution string board construction with $O(1)$ diagonal constraint arrays.

**Core Reasoning:**
- Place $N$ queens on an $N \times N$ chessboard such that no two queens attack each other.
- Process row by row. At `row`: iterate `col` from $0$ to $N-1$.
- Check `cols[col]`, `diag1[row - col + N]`, `diag2[row + col]`.
- If safe, place `'Q'`, mark arrays, recurse `row + 1`, then unmark and restore `'.'`.
- When `row == N`, convert board state into list of strings and save to results.

**Why it belongs here:** Canonical CSP construction problem.

**Complexity:** Time: $O(N!)$, Space: $O(N^2)$.

---

### Q2. N-Queens II
<a href="https://leetcode.com/problems/n-queens-ii/" target="_blank">LeetCode 52</a> — **Hard**

**Target Skill:** Solution counting optimization without string allocation overhead.

**Core Reasoning:**
- Same constraint placement logic as N-Queens I, but return the total number of distinct solutions.
- Eliminates string creation overhead (`List<String>`), focusing purely on backtracking call stack speed.

**Why it belongs here:** Demonstrates modifying the backtracking return behavior from solution construction to solution counting.

**Complexity:** Time: $O(N!)$, Space: $O(N)$.

---

### Q3. Sudoku Solver
<a href="https://leetcode.com/problems/sudoku-solver/" target="_blank">LeetCode 37</a> — **Hard**

**Target Skill:** Multi-constraint 2D grid cell filling with early boolean exit.

**Core Reasoning:**
- Solve a $9 \times 9$ Sudoku board by filling empty cells `'.'`.
- Maintain `boolean[9][10] rows`, `cols`, and `boxes` where `boxIdx = (r / 3) * 3 + (c / 3)`.
- Find next empty cell `(r, c)`. Try digits `'1'` through `'9'`.
- Check if digit is valid in row, col, and box in $O(1)$ time.
- Place digit, mark arrays. If `solve()` returns `true`, propagate `true` immediately to stop search!
- If false, reset digit to `'.'` and unmark arrays.

**Why it belongs here:** Quintessential 2D grid Constraint Satisfaction Problem.

**Complexity:** Time: $O(9^{81}) \to$ pruned execution, Space: $O(1)$ fixed.

---

## ⚡ Mastery Checklist

- [ ] How do you represent main diagonal (`r - c + N`) and anti-diagonal (`r + c`) as 1D array indices in $O(1)$ time?
- [ ] Why does `Sudoku Solver` use a `boolean` return type to stop searching immediately after finding the first valid solution?
- [ ] How do you map cell coordinates $(r, c)$ in a $9 \times 9$ grid to its $3 \times 3$ sub-box index? (`(r / 3) * 3 + (c / 3)`).
- [ ] What is the difference in execution overhead between N-Queens I (LC 51) and N-Queens II (LC 52)?
