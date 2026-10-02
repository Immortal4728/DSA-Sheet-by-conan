# Pattern 01: Tree Fundamentals & Traversal

> A binary tree is recursively defined: a `root` node pointing to a `left` binary subtree and a `right` binary subtree. Mastering recursive traversal ordering is the prerequisite for all tree problem solving.

---

## Why This Pattern Exists

Unlike linear structures (Arrays, Linked Lists) where element sequence is univariable, trees branch hierarchically.

Recursive tree traversal breaks tree problems into independent left and right sub-problems. The order in which we process the current node relative to its children defines the three fundamental Depth-First Search (DFS) traversals:
1. **Preorder (Root $\to$ Left $\to$ Right):** Process parent before children. Used when building, cloning, or top-down passing of context.
2. **Inorder (Left $\to$ Root $\to$ Right):** Process left subtree, current node, then right subtree. Produces sorted order in Binary Search Trees (BST).
3. **Postorder (Left $\to$ Right $\to$ Root):** Process both children before parent. Essential for bottom-up aggregation, deletion, and height computations.

---

## ☕ Standard Java Templates

### 1. Base Recursive DFS Structure
```java
public void dfs(TreeNode root) {
    if (root == null) {
        return; // Base Case: empty subtree
    }
    
    // Preorder Work: process root before subtrees
    
    dfs(root.left);
    
    // Inorder Work: process root between subtrees
    
    dfs(root.right);
    
    // Postorder Work: process root after subtrees
}
```

### 2. Structural Tree Comparison Template
```java
public boolean isSameTree(TreeNode p, TreeNode q) {
    if (p == null && q == null) return true;
    if (p == null || q == null) return false;
    if (p.val != q.val) return false;
    
    return isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
}
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Binary Tree Level Order Traversal** | Uses FIFO Queue for depth-by-depth exploration — belongs to **Tree BFS (Pattern 03)**. |
| **Diameter of Binary Tree** | Requires returning subtree depth state while updating global maximum — belongs to **Tree DFS Properties (Pattern 02)**. |
| **Iterative Preorder / Inorder** | Explicit Stack state management — belongs to **Iterative Tree Algorithms (Pattern 09)**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 6 |
| **Total** | **6** |

---

## 🎯 Question Set (6 Questions)

### Q1. Binary Tree Preorder Traversal
<a href="https://leetcode.com/problems/binary-tree-preorder-traversal/" target="_blank">LeetCode 144</a> — **Easy**

**Target Skill:** Root-first recursive traversal sequence.

**Core Reasoning:**
- Visit current node $N$, append `N.val` to result.
- Recursively traverse left subtree `N.left`.
- Recursively traverse right subtree `N.right`.

**Why it belongs here:** Canonical foundation for top-down tree processing.

**Complexity:** Time: $O(N)$, Space: $O(H)$ recursion stack (where $H$ is tree height).

---

### Q2. Binary Tree Inorder Traversal
<a href="https://leetcode.com/problems/binary-tree-inorder-traversal/" target="_blank">LeetCode 94</a> — **Easy**

**Target Skill:** Left-first, root-middle recursive traversal sequence.

**Core Reasoning:**
- Recursively traverse left subtree `N.left`.
- Visit current node $N$, append `N.val` to result.
- Recursively traverse right subtree `N.right`.

**Why it belongs here:** Canonical foundation for symmetric tree visits and BST in-order processing.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

### Q3. Binary Tree Postorder Traversal
<a href="https://leetcode.com/problems/binary-tree-postorder-traversal/" target="_blank">LeetCode 145</a> — **Easy**

**Target Skill:** Subtree-first, root-last recursive traversal sequence.

**Core Reasoning:**
- Recursively traverse left subtree `N.left`.
- Recursively traverse right subtree `N.right`.
- Visit current node $N$, append `N.val` to result.

**Why it belongs here:** Canonical foundation for bottom-up subtree aggregation.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

### Q4. Maximum Depth of Binary Tree
<a href="https://leetcode.com/problems/maximum-depth-of-binary-tree/" target="_blank">LeetCode 104</a> — **Easy**

**Target Skill:** Basic postorder depth calculation.

**Core Reasoning:**
- Base case: If `root == null`, depth is `0`.
- Recurse: `leftDepth = maxDepth(root.left)`, `rightDepth = maxDepth(root.right)`.
- Return `1 + Math.max(leftDepth, rightDepth)`.

**Why it belongs here:** Demonstrates postorder aggregation: parent depth depends on maximum depth of its child subtrees.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

### Q5. Same Tree
<a href="https://leetcode.com/problems/same-tree/" target="_blank">LeetCode 100</a> — **Easy**

**Target Skill:** Dual-tree simultaneous recursive structural matching.

**Core Reasoning:**
- If both nodes are `null`, trees match (`true`).
- If only one is `null` or values differ, trees do not match (`false`).
- Recursively check `isSameTree(p.left, q.left)` AND `isSameTree(p.right, q.right)`.

**Why it belongs here:** Teaches simultaneous traversal of two tree structures.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

### Q6. Symmetric Tree
<a href="https://leetcode.com/problems/symmetric-tree/" target="_blank">LeetCode 101</a> — **Easy**

**Target Skill:** Mirror-image structural comparison.

**Core Reasoning:**
- A tree is symmetric if its left subtree is a mirror reflection of its right subtree.
- Helper function `isMirror(t1, t2)`:
  - If both `null` $\to$ `true`. If one `null` or `t1.val != t2.val` $\to$ `false`.
  - Compare `t1.left` with `t2.right` AND `t1.right` with `t2.left`.

**Why it belongs here:** Extends dual-tree structural traversal to mirror logic.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

## ⚡ Mastery Checklist

- [ ] Can you state the exact execution sequence of Preorder, Inorder, and Postorder traversals?
- [ ] Do you know why `postorder` is required for height/depth aggregation?
- [ ] Can you write `isSameTree` from a blank editor in under 3 minutes?
- [ ] Do you understand why tree recursion call stacks consume $O(H)$ memory rather than $O(N)$ on balanced trees?
