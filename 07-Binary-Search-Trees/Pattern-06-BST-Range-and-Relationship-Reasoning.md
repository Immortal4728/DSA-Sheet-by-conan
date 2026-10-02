# Pattern 06: BST Range & Relationship Reasoning

> Unlike general Binary Trees where both subtrees must be searched, BST ordering allows pruning entire branches when values fall outside search targets or range boundaries.

---

## Why This Pattern Exists

In Module 06 (Binary Trees), finding the Lowest Common Ancestor (LCA) or calculating range sums requires visiting subtrees unconditionally ($O(N)$ time).

In a BST, ordering allows **branch elimination**:

$$\begin{array}{c}
\text{Binary Tree: Explore BOTH left and right subtrees } O(N) \\
\downarrow \\
\text{BST Ordering: Prune irrelevant branch } O(H)
\end{array}$$

### BST Range Pruning Rules

1. **LCA Decision Rule:**
   - If both target values `p` and `q` are **smaller** than `root.val` $\to$ LCA MUST be in the **left** subtree.
   - If both target values `p` and `q` are **larger** than `root.val` $\to$ LCA MUST be in the **right** subtree.
   - If `p` and `q` split across `root.val` (or one equals `root.val`) $\to$ **`root` IS the LCA!**

2. **Range Sum Pruning Rule:**
   - If `root.val < low`: entire left subtree is $< low$ $\to$ **prune left branch**.
   - If `root.val > high`: entire right subtree is $> high$ $\to$ **prune right branch**.

---

## ☕ Standard Java Templates

### 1. Iterative BST LCA ($O(H)$ Time, $O(1)$ Auxiliary Space)
```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    TreeNode curr = root;
    while (curr != null) {
        if (p.val < curr.val && q.val < curr.val) {
            curr = curr.left; // Both targets in left subtree
        } else if (p.val > curr.val && q.val > curr.val) {
            curr = curr.right; // Both targets in right subtree
        } else {
            return curr; // Split point: curr is the LCA!
        }
    }
    return null;
}
```

### 2. Range Pruning Sum (LC 938)
```java
public int rangeSumBST(TreeNode root, int low, int high) {
    if (root == null) return 0;
    
    // Prune left subtree if current value is smaller than low
    if (root.val < low) return rangeSumBST(root.right, low, high);
    
    // Prune right subtree if current value is greater than high
    if (root.val > high) return rangeSumBST(root.left, low, high);
    
    // In range: include node.val and recurse both sides
    return root.val + rangeSumBST(root.left, low, high) + rangeSumBST(root.right, low, high);
}
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **LCA of a Binary Tree (LC 236)** | Requires bottom-up boolean returns because tree lacks ordering — belongs to **Binary Trees (Module 06)**. |
| **Trim a BST** | Modifies and re-links node pointers — belongs to **BST Modification (Pattern 04)**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 2 |
| **Medium** | 2 |
| **Total** | **4** |

---

## 🎯 Question Set (4 Questions)

### Q1. Lowest Common Ancestor of a Binary Search Tree
<a href="https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/" target="_blank">LeetCode 235</a> — **Medium**

**Target Skill:** Branch elimination for $O(1)$ space LCA search.

**Core Reasoning:**
- Compare `p.val` and `q.val` with `root.val`.
- If both $< root.val$, move left. If both $> root.val$, move right.
- Otherwise, `root` is the split point where $P$ and $Q$ diverge $\to$ return `root`.

**Why it belongs here:** Canonical BST relationship problem demonstrating $O(1)$ space LCA search.

**Complexity:** Time: $O(H)$, Space: $O(1)$ iterative / $O(H)$ recursive.

---

### Q2. Range Sum of BST
<a href="https://leetcode.com/problems/range-sum-of-bst/" target="_blank">LeetCode 938</a> — **Easy**

**Target Skill:** Conditional branch pruning for range aggregation.

**Core Reasoning:**
- Sum values of all nodes in range `[low, high]`.
- If `root.val < low`, prune left subtree and return `rangeSumBST(root.right)`.
- If `root.val > high`, prune right subtree and return `rangeSumBST(root.left)`.
- Otherwise add `root.val` and recurse both subtrees.

**Why it belongs here:** Direct demonstration of pruning out-of-bounds branches.

**Complexity:** Time: $O(N)$ worst case ($O(K + H)$ where $K$ is number of nodes in range), Space: $O(H)$.

---

### Q3. Closest Binary Search Tree Value
<a href="https://leetcode.com/problems/closest-binary-search-tree-value/" target="_blank">LeetCode 270</a> — **Easy**

**Target Skill:** Binary search distance minimization.

**Core Reasoning:**
- Find node value closest to floating-point `target`.
- Maintain `closest = root.val`.
- While `curr != null`:
  - Update `closest` if $|curr.val - target| < |closest - target|$.
  - Move `curr = target < curr.val ? curr.left : curr.right`.

**Why it belongs here:** Single-path binary search for closest value in $O(H)$ time and $O(1)$ space.

**Complexity:** Time: $O(H)$, Space: $O(1)$.

---

### Q4. Closest Nodes Queries in a Binary Search Tree
<a href="https://leetcode.com/problems/closest-nodes-queries-in-a-binary-search-tree/" target="_blank">LeetCode 2476</a> — **Medium**

**Target Skill:** Inorder array conversion + binary search for batch range queries.

**Core Reasoning:**
- For $Q$ queries, performing BST search for each query takes $O(Q \cdot H)$ time, which TLEs if the BST is skewed ($O(Q \cdot N)$).
- **Optimal Batch Strategy:**
  1. Inorder traversal to extract elements into a sorted array `arr` ($O(N)$ time).
  2. For each query `val`, use Binary Search (`lower_bound` / `upper_bound`) on `arr` to find max value $\le val$ and min value $\ge val$ in $O(\log N)$ time.
- Total Time: $O(N + Q \log N)$.

**Why it belongs here:** Teaches batch query optimization by converting BST into an indexed sorted array.

**Complexity:** Time: $O(N + Q \log N)$, Space: $O(N)$.

---

## ⚡ Mastery Checklist & Follow-up Q&A

- [ ] Can you implement `LCA of a BST` iteratively using zero recursion space?
- [ ] *Q: Why is LCA in a BST $O(1)$ auxiliary space, while LCA in a general Binary Tree (LC 236) requires $O(H)$ space?*
  - *A:* Because BST ordering lets us navigate top-down along a single path without keeping parent recursion frames in memory!
- [ ] Why does `Closest Nodes Queries` convert the BST to a sorted array first? (Because skewed BST search is $O(N)$ per query, while array binary search is strictly $O(\log N)$!).
