# Pattern 05: LCA & Ancestor Reasoning

> The Lowest Common Ancestor (LCA) of two nodes $P$ and $Q$ is the lowest (deepest) node in a tree that has both $P$ and $Q$ as descendants. LCA algorithms answer fundamental sub-tree relationship questions.

---

## Why This Pattern Exists

In hierarchical data structures, finding common ancestors, distances between nodes, or pathways connecting arbitrary nodes requires identifying the lowest point where their branches diverge.

The core question every LCA sub-tree must answer is:
> *"What information does each child subtree return to its parent?"*

For standard LCA:
- Subtrees return non-null if they contain target node $P$ or $Q$.
- If both left and right child calls return non-null, **current node is the LCA**.
- If only one child returns non-null, pass that non-null node up to parent.

---

## ☕ Standard Java Templates

### 1. Classic Lowest Common Ancestor (LC 236)
```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    // Base Case: null node or matched target node
    if (root == null || root == p || root == q) {
        return root;
    }
    
    TreeNode left = lowestCommonAncestor(root.left, p, q);
    TreeNode right = lowestCommonAncestor(root.right, p, q);
    
    // If P is in one subtree and Q is in the other, root is the LCA!
    if (left != null && right != null) {
        return root;
    }
    
    // Otherwise return whichever subtree found a target
    return left != null ? left : right;
}
```

### 2. Deepest Subtree Return Pair Pattern (LC 1123 / LC 865)
```java
class Result {
    int depth;
    TreeNode lca;
    Result(int depth, TreeNode lca) { this.depth = depth; this.lca = lca; }
}

private Result dfs(TreeNode node) {
    if (node == null) return new Result(0, null);
    
    Result left = dfs(node.left);
    Result right = dfs(node.right);
    
    if (left.depth == right.depth) {
        return new Result(left.depth + 1, node); // Equal depth: node is LCA
    } else if (left.depth > right.depth) {
        return new Result(left.depth + 1, left.lca);
    } else {
        return new Result(right.depth + 1, right.lca);
    }
}
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Lowest Common Ancestor of a BST** | Binary Search Tree ordering allows $O(H)$ iterative value comparison — belongs to **BST (Section 07)**. |
| **All Nodes Distance K** | Requires bidirectional graph traversal or parent pointers — belongs to **Hidden Recognition (Pattern 10)**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Medium** | 5 |
| **Total** | **5** |

---

## 🎯 Question Set (5 Questions)

### Q1. Lowest Common Ancestor of a Binary Tree
<a href="https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/" target="_blank">LeetCode 236</a> — **Medium**

**Target Skill:** Bottom-up target detection and ancestor convergence.

**Core Reasoning:**
- Recurse left and right.
- If `root == null || root == p || root == q`, return `root`.
- If `left != null && right != null`, `p` and `q` are split across current subtrees $\to$ return `root`.
- Otherwise return whichever child returned non-null.

**Why it belongs here:** Canonical foundation for ancestor reasoning in binary trees.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

### Q2. Lowest Common Ancestor of Deepest Leaves
<a href="https://leetcode.com/problems/lowest-common-ancestor-of-deepest-leaves/" target="_blank">LeetCode 1123</a> — **Medium**

**Target Skill:** Dual-attribute return (depth + subtree LCA) from bottom-up recursion.

**Core Reasoning:**
- Subtree returns `Pair<Integer, TreeNode>` containing max depth and LCA of deepest leaves in that subtree.
- If `left.depth == right.depth`: deepest leaves exist in both subtrees, so `current node` is LCA of deepest leaves, depth = `left.depth + 1`.
- If `left.depth > right.depth`: return `(left.depth + 1, left.lca)`.
- If `right.depth > left.depth`: return `(right.depth + 1, right.lca)`.

**Why it belongs here:** Demonstrates returning a state tuple (`depth`, `lca`) to identify structural common ancestors dynamically.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

### Q3. Smallest Subtree with all the Deepest Nodes
<a href="https://leetcode.com/problems/smallest-subtree-with-all-the-deepest-nodes/" target="_blank">LeetCode 865</a> — **Medium**

**Target Skill:** Subtree depth equality verification.

**Core Reasoning:**
- Identical logic and structure to LC 1123.
- Finding the smallest subtree containing all deepest nodes is mathematically equivalent to finding the LCA of all deepest leaves.
- Use `dfs(node)` returning `(depth, node)` pair.

**Why it belongs here:** Reinforces that subtree isolation of deepest elements reduces to LCA pair aggregation.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

### Q4. Step-By-Step Directions From a Binary Tree Node to Another
<a href="https://leetcode.com/problems/step-by-step-directions-from-a-binary-tree-node-to-another/" target="_blank">LeetCode 2096</a> — **Medium**

**Target Skill:** Path construction via LCA reference point.

**Core Reasoning:**
- Path from $S$ to $T$ goes UP from $S$ to `LCA(S, T)` as `'U'`, then DOWN from `LCA(S, T)` to $T$ using `'L'` and `'R'`.
- Step 1: Find path string from `root` to $S$ (`pathS`) and `root` to $T$ (`pathT`).
- Step 2: Strip common prefix of `pathS` and `pathT` (common prefix represents path to LCA).
- Step 3: Replace all remaining characters of `pathS` with `'U'` (moving up to LCA).
- Step 4: Result = `U_string + remaining_pathT`.

**Why it belongs here:** Shows how finding LCA optimizes tree node-to-node path construction without converting tree into an explicit graph.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

### Q5. Distance Between Two Nodes in a Binary Tree
**Standard Interview Problem** — **Medium**

**Target Skill:** Path length calculation using node depth and LCA depth.

**Core Reasoning:**
- Distance between node $A$ and node $B$ is:
  $$\text{Dist}(A, B) = \text{Dist}(\text{root}, A) + \text{Dist}(\text{root}, B) - 2 \times \text{Dist}(\text{root}, \text{LCA}(A, B))$$
- Find LCA of $A$ and $B$.
- Compute depth from LCA to $A$ and depth from LCA to $B$.
- Sum of depths = distance between $A$ and $B$.

**Why it belongs here:** Essential application of LCA for distance metrics on tree graphs.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

## ⚡ Mastery Checklist

- [ ] Can you write `lowestCommonAncestor` (LC 236) cleanly in 10 lines of Java?
- [ ] Do you understand why returning a tuple `(depth, lca)` solves `LCA of Deepest Leaves` in a single pass?
- [ ] How do you convert paths from root $\to S$ and root $\to T$ into direct directions from $S \to T$?
- [ ] Why is BST LCA simpler than Binary Tree LCA? (Keep BST LCA in Section 07!)
