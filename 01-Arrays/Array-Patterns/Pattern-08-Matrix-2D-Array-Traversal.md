# Pattern 8: Matrix / 2D Array Traversal

> Treat a 2D grid as a structured array of rows and columns, systematically navigating boundaries, diagonals, directions, and transformations in $O(M \times N)$ time.

---

## 🔍 Beginner Audit: 2D Indexing & Coordinates

In programming languages, a 2D matrix is an array of 1D arrays:

```java
int[][] matrix = new int[m][n];
int rows = matrix.length;    // m = Number of Rows
int cols = matrix[0].length; // n = Number of Columns
```

```text
          Col 0   Col 1   Col 2
Row 0   [ (0,0),  (0,1),  (0,2) ]
Row 1   [ (1,0),  (1,1),  (1,2) ]
Row 2   [ (2,0),  (2,1),  (2,2) ]
```

> **Important Boundary Rule:** Always write `matrix[row][col]`. 
> - Row index `r` ranges from `0` to `rows - 1`.
> - Column index `c` ranges from `0` to `cols - 1`.

---

## Core Technical Intuition — Boundary Management & Transformations

Matrix problems test your ability to navigate directional boundaries without indexing errors or extra memory allocation:

1. **4-Boundary Shrinking Engine:** Maintain `top`, `bottom`, `left`, `right` boundary pointers to traverse layered grids in spiral order.
2. **Matrix Transformations:** Rotate matrices by combining fundamental operations (e.g. **Transpose + Row Reversal = 90° Clockwise Rotation**).
3. **Diagonal Invariants:**
   - Main Diagonal: `row == col`
   - Anti-Diagonal: `row + col == N - 1`
   - All cells on the same anti-diagonal share equal `row + col` sums.

---

## ☕ Standard Java Code Templates

### 1. Spiral Boundary Engine (`top`, `bottom`, `left`, `right`)
```java
public List<Integer> spiralOrder(int[][] matrix) {
    List<Integer> result = new ArrayList<>();
    int top = 0, bottom = matrix.length - 1;
    int left = 0, right = matrix[0].length - 1;
    
    while (top <= bottom && left <= right) {
        // Traverse Right on Top row
        for (int c = left; c <= right; c++) result.add(matrix[top][c]);
        top++;
        
        // Traverse Down on Right col
        for (int r = top; r <= bottom; r++) result.add(matrix[r][right]);
        right--;
        
        // Traverse Left on Bottom row (if valid)
        if (top <= bottom) {
            for (int c = right; c >= left; c--) result.add(matrix[bottom][c]);
            bottom--;
        }
        
        // Traverse Up on Left col (if valid)
        if (left <= right) {
            for (int r = bottom; r >= top; r--) result.add(matrix[r][left]);
            left++;
        }
    }
    
    return result;
}
```

### 2. In-Place Matrix Rotation 90° Clockwise (Transpose + Reverse)
```java
public void rotate(int[][] matrix) {
    int n = matrix.length;
    
    // Step 1: Transpose matrix (swap matrix[i][j] with matrix[j][i])
    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {
            int temp = matrix[i][j];
            matrix[i][j] = matrix[j][i];
            matrix[j][i] = temp;
        }
    }
    
    // Step 2: Reverse each row horizontally
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n / 2; j++) {
            int temp = matrix[i][j];
            matrix[i][j] = matrix[i][n - 1 - j];
            matrix[i][n - 1 - j] = temp;
        }
    }
}
```

### 3. Direction Vectors (4-Way Neighbor Step)
```java
// Direction offsets for: Up, Down, Left, Right
int[][] DIRECTIONS = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};

public void checkNeighbors(int[][] grid, int r, int c) {
    int rows = grid.length, cols = grid[0].length;
    for (int[] dir : DIRECTIONS) {
        int newRow = r + dir[0];
        int newCol = c + dir[1];
        if (newRow >= 0 && newRow < rows && newCol >= 0 && newCol < cols) {
            // Valid neighbor at grid[newRow][newCol]
        }
    }
}
```

---

## 🔍 Step-by-Step Trace Table (Dry Run)

Tracing **Spiral Boundary Engine** on `matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]`:

Initial Boundaries: `top = 0, bottom = 2, left = 0, right = 2`

| Step | Direction Traversed | Range Inspected | Elements Added | Updated Boundaries |
|------|---------------------|-----------------|----------------|--------------------|
| **1** | Top Row (Left $\rightarrow$ Right) | `Row 0, Col 0..2` | `1, 2, 3` | `top` becomes `1` |
| **2** | Right Column (Top $\rightarrow$ Bottom) | `Col 2, Row 1..2` | `6, 9` | `right` becomes `1` |
| **3** | Bottom Row (Right $\rightarrow$ Left) | `Row 2, Col 1..0` | `8, 7` | `bottom` becomes `1` |
| **4** | Left Column (Bottom $\rightarrow$ Top) | `Col 0, Row 1..1` | `4` | `left` becomes `1` |
| **5** | Top Row (Left $\rightarrow$ Right) | `Row 1, Col 1..1` | `5` | `top` becomes `2` (Loop Ends) |

**Result:** `[1, 2, 3, 6, 9, 8, 7, 4, 5]`.

---

## Pattern Recognition Layer

When facing an unfamiliar 2D grid problem, walk through this decision tree:

```text
Is the input two-dimensional?
        ↓
What are the row/column boundaries?
        ↓
What direction am I moving?
        ↓
Can I describe the traversal with a boundary invariant?
(e.g. top++, bottom--, left++, right--)
        ↓
Do I need to process layers/boundaries?
        ↓
Can I transform the matrix in-place? (Transpose + Reverse)
        ↓
Is the real algorithm actually BFS/DFS/DP/Prefix Sum?
```

---

## 🛑 Important Pattern Boundaries

Do **NOT** classify every 2D grid problem under Matrix Traversal. Matrix Traversal specifically covers **indexing, boundary movement, diagonal steps, and coordinate transformations**.

If the primary algorithmic optimization relies on another concept, classify it under its true primary pattern:

| Problem Requirement / Technique | Primary Pattern | Why Matrix Traversal Is NOT the Primary Optimization |
|---------------------------------|-----------------|-----------------------------------------------------|
| **Connected Components / Flood Fill** | **Graphs (BFS/DFS)** | Optimization comes from queue/stack traversal of graph components. |
| **Shortest Path in Grid** | **Graphs (BFS / Dijkstra)** | Optimization comes from level-order distance relaxation. |
| **Grid Path Counting / Min Cost Path** | **Dynamic Programming** | Optimization comes from memoized subproblem state transitions. |
| **Subgrid Region Sum Queries** | **Prefix Sum (2D)** | Optimization comes from precomputed 2D cumulative area subtractions. |

---

## 📊 Difficulty Distribution

| Depth Tier | Count |
|------------|-------|
| **Foundation** | 2 |
| **Core** | 4 |
| **Advanced** | 2 |
| **Interview Recognition (Pattern Hidden)** | 1 |
| **Total** | **9** |

---

## 🎯 Question Progression (Curated 9-Question Set)

### 1. Foundation (2 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 1 | Transpose Matrix | <a href="https://leetcode.com/problems/transpose-matrix/" target="_blank">LeetCode 867</a> | Easy | Basic 2D indexing & dimension swapping ($M \times N \rightarrow N \times M$). |
| 2 | Matrix Diagonal Sum | <a href="https://leetcode.com/problems/matrix-diagonal-sum/" target="_blank">LeetCode 1572</a> | Easy | Primary and secondary diagonal traversal logic. |

---

### 2. Core (4 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 3 | Diagonal Traverse | <a href="https://leetcode.com/problems/diagonal-traverse/" target="_blank">LeetCode 498</a> | Medium | Directional diagonal traversal with boundary bounces. |
| 4 | Spiral Matrix | <a href="https://leetcode.com/problems/spiral-matrix/" target="_blank">LeetCode 54</a> | Medium | Layered boundary shrinking (`top++`, `bottom--`, `left++`, `right--`). |
| 5 | Rotate Image | <a href="https://leetcode.com/problems/rotate-image/" target="_blank">LeetCode 48</a> | Medium | In-place matrix rotation via Transpose + Reverse. |
| 6 | Set Matrix Zeroes | <a href="https://leetcode.com/problems/set-matrix-zeroes/" target="_blank">LeetCode 73</a> | Medium | In-place state marking using first row/col as markers without extra space. |

---

### 3. Advanced (2 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 7 | Spiral Matrix II | <a href="https://leetcode.com/problems/spiral-matrix-ii/" target="_blank">LeetCode 59</a> | Medium | 2D matrix construction with layered spiral boundary filling. |
| 8 | Search a 2D Matrix II | <a href="https://leetcode.com/problems/search-a-2d-matrix-ii/" target="_blank">LeetCode 240</a> | Medium | Staircase traversal from top-right corner exploiting row/col sorting. |

---

## 🕵️ Interview Recognition — Pattern Hidden

The following problem tests your ability to recognize matrix coordinate transformations independently without explicit pattern labels. Analyze the requirements and derive the indexing invariants from scratch.

| # | Problem | LeetCode Link | Difficulty | Objective |
|---|---------|---------------|------------|-----------|
| 9 | Search a 2D Matrix | <a href="https://leetcode.com/problems/search-a-2d-matrix/" target="_blank">LeetCode 74</a> | Medium | Search target in a row-sorted 2D matrix efficiently. |

<details>
<summary>💡 Reveal Pattern Hint (Click after attempting from a blank editor)</summary>

- **Problem 9:** Treat the $M \times N$ matrix as a virtual 1D sorted array of length $M \times N$. Map 1D binary search index `mid` to 2D coordinates: `row = mid / cols`, `col = mid % cols`.
</details>

---

## 🏆 Mastery Criteria

You have mastered **Pattern #8** when you can:

- [ ] Navigate 2D array coordinates (`matrix[row][col]`) without out-of-bounds errors.
- [ ] Handle rectangular ($M \times N$) and square ($N \times N$) matrices seamlessly.
- [ ] Traverse primary (`r == c`) and secondary (`r + c == N - 1`) diagonals efficiently.
- [ ] Implement the 4-boundary shrinking engine (`top`, `bottom`, `left`, `right`) for spiral traversals.
- [ ] Rotate square matrices in-place using Transpose + Row Reversal.
- [ ] Use 4-way and 8-way direction arrays (`DIRECTIONS`) for clean neighbor stepping.
- [ ] Perform in-place state marking without allocating auxiliary matrices.
- [ ] Distinguish pure Matrix Traversal from Graph BFS/DFS, Flood Fill, and Grid Dynamic Programming.
- [ ] Implement solutions cleanly from a blank editor.
- [ ] Solve an unfamiliar matrix problem without being told the intended pattern.

---

## ➡️ Next Step

Once Matrix Traversal mechanics feel natural, move to **[Pattern 09: Prefix & Suffix Accumulation](./Pattern-09-Prefix-and-Suffix-Accumulation.md)** to learn how to precompute accumulated state from both left and right directions for element-centered decision making!
