# Pattern 01: BST Fundamentals & Search

> A Binary Search Tree (BST) is a binary tree with a strict spatial ordering invariant: for every node $N$, all values in $N$'s left subtree are strictly smaller (`val < N.val`), and all values in $N$'s right subtree are strictly larger (`val > N.val`).

---

## Why This Pattern Exists

In a general Binary Tree (Module 06), searching for an element requires checking both subtrees ($O(N)$ time).

A BST enables **binary search on a 2D tree topology**:
- If `target < current.val`, the target CANNOT exist in the right subtree $\to$ search **left**.
- If `target > current.val`, the target CANNOT exist in the left subtree $\to$ search **right**.

```text
               target < current
                ← search left
                   [ Current ]
                      search right →
                      target > current
```

### The Inorder Property

The single most important property of a valid BST:
$$\text{BST} \longrightarrow \text{Inorder Traversal (Left } \to \text{ Node } \to \text{ Right)} \longrightarrow \text{Strictly Sorted Sequence}$$

Any problem asking about adjacent element differences, mode values, or sorted ordering on a BST can be solved by processing elements in **Inorder sequence**.

---

## ☕ Standard Java Templates

### 1. Recursive BST Search ($O(H)$ Time, $O(H)$ Space)
```java
public TreeNode searchBST(TreeNode root, int val) {
    if (root == null || root.val == val) return root;
    if (val < root.val) return searchBST(root.left, val);
    return searchBST(root.right, val);
}
```

### 2. Iterative BST Search ($O(H)$ Time, $O(1)$ Auxiliary Space)
```java
public TreeNode searchBSTIterative(TreeNode root, int val) {
    TreeNode curr = root;
    while (curr != null && curr.val != val) {
        if (val < curr.val) curr = curr.left;
        else curr = curr.right;
    }
    return curr;
}
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Kth Smallest Element in a BST** | Requires tracking element rank during traversal — belongs to **Pattern 03**. |
| **Validate Binary Search Tree** | Requires enforcing global inherited range boundaries — belongs to **Pattern 02**. |
| **Range Sum of BST** | Uses range bounds to prune both subtrees — belongs to **Pattern 06**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 3 |
| **Total** | **3** |

---

## 🎯 Question Set (3 Questions)

### Q1. Search in a Binary Search Tree
<a href="https://leetcode.com/problems/search-in-a-binary-search-tree/" target="_blank">LeetCode 700</a> — **Easy**

**Target Skill:** Basic BST directional search invariant application.

**Core Reasoning:**
- Compare target `val` with `root.val`.
- If equal, return `root`. If `val < root.val`, search left child; else search right child.

**Why it belongs here:** Canonical entry problem establishing $O(H)$ search logic.

**Complexity:** Time: $O(H)$ ($O(\log N)$ balanced, $O(N)$ skewed), Space: $O(1)$ iterative / $O(H)$ recursive.

---

### Q2. Minimum Absolute Difference in BST
<a href="https://leetcode.com/problems/minimum-absolute-difference-in-bst/" target="_blank">LeetCode 530</a> — **Easy**

**Target Skill:** Adjacent element gap evaluation during Inorder traversal.

**Core Reasoning:**
- Inorder traversal of a BST yields a sorted sequence. The minimum absolute difference between ANY two nodes MUST occur between two adjacent nodes in the inorder traversal.
- Perform Inorder traversal (`dfs(node.left)` $\to$ process `node` $\to$ `dfs(node.right)`).
- Maintain `prev` node value. At current node, `minDiff = Math.min(minDiff, curr.val - prev.val)`. Update `prev = curr.val`.

**Why it belongs here:** Demonstrates how the BST Inorder $\to$ Sorted Sequence property reduces an $O(N^2)$ pairwise search into an $O(N)$ single pass.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

### Q3. Find Mode in Binary Search Tree
<a href="https://leetcode.com/problems/find-mode-in-binary-search-tree/" target="_blank">LeetCode 501</a> — **Easy**

**Target Skill:** Inorder frequency counting without HashMap auxiliary space.

**Core Reasoning:**
- Find all modes (most frequently occurring values) in a BST (duplicates allowed: `left <= parent <= right`).
- Inorder traversal processes identical values consecutively (`... 2, 2, 2, 3 ...`).
- Pass 1: Inorder traversal tracks `currCount` and `maxCount`.
- Pass 2 (or dynamic list clearing): Reset count, collect values whose frequency matches `maxCount`.

**Why it belongs here:** Teaches how consecutive grouping in Inorder traversal eliminates the need for an $O(N)$ HashMap.

**Complexity:** Time: $O(N)$, Space: $O(H)$ recursion stack ($O(1)$ auxiliary memory).

---

## ⚡ Mastery Checklist

- [ ] Can you explain why searching a BST takes $O(H)$ time instead of $O(N)$?
- [ ] What is the worst-case height $H$ of a BST, and when does it occur?
- [ ] Why does Minimum Absolute Difference only need to check adjacent elements during Inorder traversal?
- [ ] Can you write iterative `searchBST` using zero extra stack space?
