# Pattern 04: Tree Views & Structural Traversal

> Structural tree views project a 2D tree layout onto 1D coordinate axes (horizontal columns, depth levels, or outer boundaries). Success requires mapping nodes to coordinate systems $(col, row)$.

---

## Why This Pattern Exists

Standard DFS and BFS traverse nodes according to parent-child pointers. However, structural view problems ask:
- *"Which nodes are visible from directly above the tree (Top View)?"*
- *"Which nodes share the same vertical column (Vertical Traversal)?"*
- *"What is the outer perimeter of the tree (Boundary Traversal)?"*

These questions cannot be answered by standard traversal alone. They require assigning **coordinates**:
- Root node is at column $0$, row $0$.
- Left child is at column $col - 1$, row $row + 1$.
- Right child is at column $col + 1$, row $row + 1$.

---

## Vertical Coordinate System

$$\begin{array}{c}
\text{Root } (col=0, row=0) \\
\swarrow \quad \searrow \\
\text{Left } (-1, 1) \quad \text{Right } (1, 1)
\end{array}$$

By tracking $col$ and $row$, we can group nodes into vertical columns and sort them deterministically.

---

## ☕ Standard Java Templates

### Vertical Order Traversal Map Pattern (LC 314)
```java
public List<List<Integer>> verticalOrder(TreeNode root) {
    List<List<Integer>> result = new ArrayList<>();
    if (root == null) return result;
    
    Map<Integer, List<Integer>> colMap = new HashMap<>();
    Queue<TreeNode> nodeQ = new LinkedList<>();
    Queue<Integer> colQ = new LinkedList<>();
    
    nodeQ.offer(root);
    colQ.offer(0);
    
    int minCol = 0, maxCol = 0;
    
    while (!nodeQ.isEmpty()) {
        TreeNode node = nodeQ.poll();
        int col = colQ.poll();
        
        colMap.computeIfAbsent(col, k -> new ArrayList<>()).add(node.val);
        minCol = Math.min(minCol, col);
        maxCol = Math.max(maxCol, col);
        
        if (node.left != null) {
            nodeQ.offer(node.left);
            colQ.offer(col - 1);
        }
        if (node.right != null) {
            nodeQ.offer(node.right);
            colQ.offer(col + 1);
        }
    }
    
    for (int col = minCol; col <= maxCol; col++) {
        result.add(colMap.get(col));
    }
    return result;
}
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Right Side View (LC 199)** | Simply takes the last node of each BFS level snapshot — belongs to **Tree BFS (Pattern 03)**. |
| **Maximum Width of Binary Tree** | Tracks 1D complete binary tree index $2i$ per level — belongs to **Tree BFS (Pattern 03)**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Medium** | 4 |
| **Hard** | 1 |
| **Total** | **5** |

---

## 🎯 Question Set (5 Questions)

### Q1. Binary Tree Left Side View
**Standard Interview Problem** — **Medium**

**Target Skill:** First-node level extraction via DFS depth tracking or BFS.

**Core Reasoning:**
- Left Side View returns the first node visible at each depth from left to right.
- **DFS Approach:** Traverse using root $\to$ left $\to$ right order.
- Maintain `List<Integer> result`. If `depth == result.size()`, this is the first node visited at `depth`, so append `node.val`.

**Why it belongs here:** Direct contrast to Right Side View. Teaches DFS depth-triggered insertion (`depth == list.size()`).

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

### Q2. Binary Tree Vertical Order Traversal
<a href="https://leetcode.com/problems/binary-tree-vertical-order-traversal/" target="_blank">LeetCode 314</a> — **Medium**

**Target Skill:** BFS column grouping with insertion order preservation.

**Core Reasoning:**
- Group nodes by column $col$. Nodes in the same column should appear top-to-bottom, left-to-right.
- Run BFS with a node queue and column queue (`col - 1` for left, `col + 1` for right).
- Maintain `minCol` and `maxCol`. Put nodes into `Map<Integer, List<Integer>>`.
- Collect columns from `minCol` to `maxCol`.

**Why it belongs here:** Essential vertical coordinate mapping where BFS naturally handles top-to-bottom ordering within columns.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

### Q3. Vertical Order Traversal of a Binary Tree
<a href="https://leetcode.com/problems/vertical-order-traversal-of-a-binary-tree/" target="_blank">LeetCode 987</a> — **Hard**

**Target Skill:** Strict 2D coordinate sorting $(col, row, value)$.

**Core Reasoning:**
- If two nodes are at the same column AND same row (depth), they MUST be sorted by their values in ascending order!
- Store tuple `(col, row, val)` for every node during DFS or BFS.
- Sort tuples by: `col` ascending $\to$ `row` ascending $\to$ `val` ascending.
- Group sorted tuples by `col`.

**Why it belongs here:** Teaches strict multi-attribute coordinate sorting when spatial overlap occurs.

**Complexity:** Time: $O(N \log N)$ (due to sorting), Space: $O(N)$.

---

### Q4. Boundary of Binary Tree
<a href="https://leetcode.com/problems/boundary-of-binary-tree/" target="_blank">LeetCode 545</a> — **Medium**

**Target Skill:** Structural perimeter decomposition (Left Boundary + Leaves + Right Boundary).

**Core Reasoning:**
- Boundary consists of: Root + Left Boundary (excluding leaves) + Leaf Nodes (left to right) + Right Boundary in reverse (excluding leaves).
- **Left Boundary:** Start `curr = root.left`. While `curr != null`, if not leaf, add `curr.val`. Move to `curr.left` (or `curr.right` if left is null).
- **Leaves:** Preorder DFS collecting all nodes where `left == null && right == null`.
- **Right Boundary:** Start `curr = root.right`. Push non-leaf values onto stack. Move to `curr.right` (or `curr.left` if right is null). Pop stack into result.

**Why it belongs here:** Canonical structural boundary traversal requiring non-trivial directional decomposition.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

### Q5. Top View of Binary Tree
**Standard Interview Problem** — **Medium**

**Target Skill:** First-seen column node projection.

**Core Reasoning:**
- Top View consists of nodes visible when looking at the tree from directly above.
- For each vertical column $col$, only the **shallowest** (first encountered) node is visible.
- Run BFS with column tracking `col`.
- If `col` is NOT present in `colMap`, put `colMap.put(col, node.val)`.
- Collect values ordered from `minCol` to `maxCol`.

**Why it belongs here:** Canonical structural projection combining vertical column coordinates with shallowest-depth visibility.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

## ⚡ Mastery Checklist

- [ ] Can you set up a 2D coordinate system $(col, row)$ for a binary tree?
- [ ] Do you know why BFS naturally preserves top-to-bottom order for `Vertical Order Traversal` (LC 314)?
- [ ] Can you explain the sorting difference between LC 314 and LC 987 when two nodes have identical $(col, row)$ coordinates?
- [ ] Can you write `Boundary Traversal` cleanly by breaking it into 3 distinct helper functions?
