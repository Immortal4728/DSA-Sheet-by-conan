# 🌲 Module 06: Binary Trees

> Master recursive tree decomposition, level-order BFS, structural coordinates, LCA ancestor reasoning, tree construction, in-place mutations, subtree Tree DP, iterative stack traversals, and hidden tree recognition for SDE-1 interviews.

---

## 🎯 Section Objective & SDE-1 Focus

Binary Trees test a candidate's ability to think recursively, manage structural state, and manipulate pointer hierarchies under pressure.

This section is strictly optimized for **SDE-1 technical interview success**, avoiding competitive-programming bloat (no Heavy-Light Decomposition, Euler Tour, or Link-Cut Trees).

The curriculum contains **53 unique, mandatory questions** organized across **10 distinct pattern files**. Every problem has **exactly one canonical home**.

---

## 🗺️ Curriculum Architecture

| Pattern File | Focus Area | Unique Questions | Core Mental Model / Mechanism |
|--------------|------------|------------------|-------------------------------|
| **[Pattern 01](./Pattern-01-Tree-Fundamentals-and-Traversal.md)** | Tree Fundamentals & Traversal | 6 | Preorder, Inorder, Postorder recursive traversal, structural equality |
| **[Pattern 02](./Pattern-02-Tree-DFS-Depth-Paths-and-Properties.md)** | Tree DFS: Depth, Paths & Properties | 9 | `solve(left)`, `solve(right)`, `combine()`, depth, global updates, prefix sums |
| **[Pattern 03](./Pattern-03-Tree-BFS-Level-Order.md)** | Tree BFS / Level-Order Patterns | 6 | Queue level snapshotting, level metrics, right side view, positional indexing |
| **[Pattern 04](./Pattern-04-Tree-Views-and-Structural-Traversal.md)** | Tree Views & Structural Traversal | 5 | 2D coordinate system $(col, row)$, vertical sorting, boundary traversal |
| **[Pattern 05](./Pattern-05-LCA-and-Ancestor-Reasoning.md)** | LCA & Ancestor Reasoning | 5 | Bottom-up target detection, deepest leaf LCA, step-by-step path directions |
| **[Pattern 06](./Pattern-06-Tree-Construction-and-Serialization.md)** | Tree Construction & Serialization | 5 | Root location, inorder splitting, null-delimited stream serialization |
| **[Pattern 07](./Pattern-07-Tree-Transformations-and-Mutation.md)** | Tree Transformations & Mutation | 5 | In-place pointer swapping, postorder pruning, reverse preorder flattening |
| **[Pattern 08](./Pattern-08-Tree-DP-and-Subtree-State.md)** | Tree DP & Subtree State | 7 | Local state tuples `[rob, skip]`, single-leg return vs full path update |
| **[Pattern 09](./Pattern-09-Iterative-Tree-Algorithms.md)** | Iterative Tree Algorithms | *Technique* | Explicit stack mechanics for LC 144, 94, 145 (0 new questions) |
| **[Pattern 10](./Pattern-10-Hidden-Tree-Recognition.md)** | Hidden Tree Recognition | 5 | Parent mapping, 3-directional BFS, height leaf layering, path bitmasking |

---

## 🧠 Core Tree Mental Models

### 1. The Recursive DFS Decomposition
```java
LeftResult left = solve(node.left);
RightResult right = solve(node.right);
return combine(left, right, node);
```
- **Top-Down (Preorder):** Pass information down to children (e.g., path sums, max-so-far values).
- **Bottom-Up (Postorder):** Aggregate values returned from children up to parent (e.g., depth, balance status, camera coverage).

### 2. The BFS Queue Snapshot
```java
int levelSize = queue.size();
for (int i = 0; i < levelSize; i++) {
    TreeNode curr = queue.poll();
    // process level node...
}
```

### 3. Subtree State vs Global Answer (Tree DP)
$$\text{Single-Leg Return to Parent} \neq \text{Full Subtree Path Combination}$$
Always distinguish what is returned to the parent vs what updates a global answer variable.

---

## 🔒 Canonical Placement Rules

- **LC 543 (Diameter):** Housed in **Pattern 02** (not duplicated in Tree DP).
- **LC 199 (Right Side View):** Housed in **Pattern 03** (not duplicated in Pattern 04).
- **LC 114 (Flatten Tree):** Housed in **Pattern 07** (not duplicated elsewhere).
- **LC 124 (Max Path Sum):** Housed in **Pattern 08** (canonical Tree DP).
- **BST-Specific Problems:** Kept completely separate in **Section 07 (BST)**.
- **Pattern 09 (Iterative Algorithms):** Provides explicit stack technique implementations for LC 144, 94, 145 without increasing question count.

---

## 🔗 Cross-Pattern Connections

- **Section 05 Stacks & Queues $\to$ Module 06:** Queue mechanics drive Level-Order BFS (Pattern 03); Stack mechanics drive Iterative Traversal (Pattern 09).
- **Section 03 Hashing $\to$ Module 06:** Prefix sum maps track tree path sums in LC 437 (Pattern 02).
- **Section 10 Dynamic Programming $\to$ Module 06:** Subtree state tuples (Pattern 08) serve as the direct bridge into general DP.
- **Section 11 Graphs $\to$ Module 06:** Trees are directed acyclic graphs. Parent mapping in Pattern 10 bridges tree nodes into undirected graph traversals (LC 863, LC 2385).

---

## 📑 Full Question Matrix (53 Unique Questions)

### Pattern 01: Tree Fundamentals & Traversal (6 Questions)
1. <a href="https://leetcode.com/problems/binary-tree-preorder-traversal/" target="_blank">LeetCode 144: Binary Tree Preorder Traversal</a> — **Easy**
2. <a href="https://leetcode.com/problems/binary-tree-inorder-traversal/" target="_blank">LeetCode 94: Binary Tree Inorder Traversal</a> — **Easy**
3. <a href="https://leetcode.com/problems/binary-tree-postorder-traversal/" target="_blank">LeetCode 145: Binary Tree Postorder Traversal</a> — **Easy**
4. <a href="https://leetcode.com/problems/maximum-depth-of-binary-tree/" target="_blank">LeetCode 104: Maximum Depth of Binary Tree</a> — **Easy**
5. <a href="https://leetcode.com/problems/same-tree/" target="_blank">LeetCode 100: Same Tree</a> — **Easy**
6. <a href="https://leetcode.com/problems/symmetric-tree/" target="_blank">LeetCode 101: Symmetric Tree</a> — **Easy**

### Pattern 02: Tree DFS: Depth, Paths & Properties (9 Questions)
1. <a href="https://leetcode.com/problems/minimum-depth-of-binary-tree/" target="_blank">LeetCode 111: Minimum Depth of Binary Tree</a> — **Easy**
2. <a href="https://leetcode.com/problems/balanced-binary-tree/" target="_blank">LeetCode 110: Balanced Binary Tree</a> — **Easy**
3. <a href="https://leetcode.com/problems/path-sum/" target="_blank">LeetCode 112: Path Sum</a> — **Easy**
4. <a href="https://leetcode.com/problems/path-sum-ii/" target="_blank">LeetCode 113: Path Sum II</a> — **Medium**
5. <a href="https://leetcode.com/problems/sum-root-to-leaf-numbers/" target="_blank">LeetCode 129: Sum Root to Leaf Numbers</a> — **Medium**
6. <a href="https://leetcode.com/problems/binary-tree-paths/" target="_blank">LeetCode 257: Binary Tree Paths</a> — **Easy**
7. <a href="https://leetcode.com/problems/diameter-of-binary-tree/" target="_blank">LeetCode 543: Diameter of Binary Tree</a> — **Easy**
8. <a href="https://leetcode.com/problems/longest-univalue-path/" target="_blank">LeetCode 687: Longest Univalue Path</a> — **Medium**
9. <a href="https://leetcode.com/problems/path-sum-iii/" target="_blank">LeetCode 437: Path Sum III</a> — **Medium**

### Pattern 03: Tree BFS / Level-Order Patterns (6 Questions)
1. <a href="https://leetcode.com/problems/binary-tree-level-order-traversal/" target="_blank">LeetCode 102: Binary Tree Level Order Traversal</a> — **Medium**
2. <a href="https://leetcode.com/problems/binary-tree-level-order-traversal-ii/" target="_blank">LeetCode 107: Binary Tree Level Order Traversal II</a> — **Medium**
3. <a href="https://leetcode.com/problems/average-of-levels-in-binary-tree/" target="_blank">LeetCode 637: Average of Levels in Binary Tree</a> — **Easy**
4. <a href="https://leetcode.com/problems/binary-tree-right-side-view/" target="_blank">LeetCode 199: Binary Tree Right Side View</a> — **Medium**
5. <a href="https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/" target="_blank">LeetCode 103: Binary Tree Zigzag Level Order Traversal</a> — **Medium**
6. <a href="https://leetcode.com/problems/maximum-width-of-binary-tree/" target="_blank">LeetCode 662: Maximum Width of Binary Tree</a> — **Medium**

### Pattern 04: Tree Views & Structural Traversal (5 Questions)
1. **Binary Tree Left Side View** — **Medium**
2. <a href="https://leetcode.com/problems/binary-tree-vertical-order-traversal/" target="_blank">LeetCode 314: Binary Tree Vertical Order Traversal</a> — **Medium**
3. <a href="https://leetcode.com/problems/vertical-order-traversal-of-a-binary-tree/" target="_blank">LeetCode 987: Vertical Order Traversal of a Binary Tree</a> — **Hard**
4. <a href="https://leetcode.com/problems/boundary-of-binary-tree/" target="_blank">LeetCode 545: Boundary of Binary Tree</a> — **Medium**
5. **Top View of Binary Tree** — **Medium**

### Pattern 05: LCA & Ancestor Reasoning (5 Questions)
1. <a href="https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/" target="_blank">LeetCode 236: Lowest Common Ancestor of a Binary Tree</a> — **Medium**
2. <a href="https://leetcode.com/problems/lowest-common-ancestor-of-deepest-leaves/" target="_blank">LeetCode 1123: Lowest Common Ancestor of Deepest Leaves</a> — **Medium**
3. <a href="https://leetcode.com/problems/smallest-subtree-with-all-the-deepest-nodes/" target="_blank">LeetCode 865: Smallest Subtree with all the Deepest Nodes</a> — **Medium**
4. <a href="https://leetcode.com/problems/step-by-step-directions-from-a-binary-tree-node-to-another/" target="_blank">LeetCode 2096: Step-By-Step Directions From a Binary Tree Node to Another</a> — **Medium**
5. **Distance Between Two Nodes in a Binary Tree** — **Medium**

### Pattern 06: Tree Construction & Serialization (5 Questions)
1. <a href="https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/" target="_blank">LeetCode 105: Construct Binary Tree from Preorder and Inorder Traversal</a> — **Medium**
2. <a href="https://leetcode.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/" target="_blank">LeetCode 106: Construct Binary Tree from Inorder and Postorder Traversal</a> — **Medium**
3. <a href="https://leetcode.com/problems/construct-binary-tree-from-preorder-and-postorder-traversal/" target="_blank">LeetCode 889: Construct Binary Tree from Preorder and Postorder Traversal</a> — **Medium**
4. <a href="https://leetcode.com/problems/serialize-and-deserialize-binary-tree/" target="_blank">LeetCode 297: Serialize and Deserialize Binary Tree</a> — **Hard**
5. <a href="https://leetcode.com/problems/construct-string-from-binary-tree/" target="_blank">LeetCode 606: Construct String from Binary Tree</a> — **Easy**

### Pattern 07: Tree Transformations & Mutation (5 Questions)
1. <a href="https://leetcode.com/problems/invert-binary-tree/" target="_blank">LeetCode 226: Invert Binary Tree</a> — **Easy**
2. <a href="https://leetcode.com/problems/merge-two-binary-trees/" target="_blank">LeetCode 617: Merge Two Binary Trees</a> — **Easy**
3. <a href="https://leetcode.com/problems/flatten-binary-tree-to-linked-list/" target="_blank">LeetCode 114: Flatten Binary Tree to Linked List</a> — **Medium**
4. <a href="https://leetcode.com/problems/binary-tree-pruning/" target="_blank">LeetCode 814: Binary Tree Pruning</a> — **Medium**
5. <a href="https://leetcode.com/problems/delete-nodes-and-return-forest/" target="_blank">LeetCode 1110: Delete Nodes And Return Forest</a> — **Medium**

### Pattern 08: Tree DP & Subtree State (7 Questions)
1. <a href="https://leetcode.com/problems/binary-tree-maximum-path-sum/" target="_blank">LeetCode 124: Binary Tree Maximum Path Sum</a> — **Hard**
2. <a href="https://leetcode.com/problems/house-robber-iii/" target="_blank">LeetCode 337: House Robber III</a> — **Medium**
3. <a href="https://leetcode.com/problems/binary-tree-cameras/" target="_blank">LeetCode 968: Binary Tree Cameras</a> — **Hard**
4. <a href="https://leetcode.com/problems/distribute-coins-in-binary-tree/" target="_blank">LeetCode 979: Distribute Coins in Binary Tree</a> — **Medium**
5. <a href="https://leetcode.com/problems/longest-zigzag-path-in-a-binary-tree/" target="_blank">LeetCode 1372: Longest ZigZag Path in a Binary Tree</a> — **Medium**
6. <a href="https://leetcode.com/problems/maximum-difference-between-node-and-ancestor/" target="_blank">LeetCode 1026: Maximum Difference Between Node and Ancestor</a> — **Medium**
7. <a href="https://leetcode.com/problems/count-good-nodes-in-binary-tree/" target="_blank">LeetCode 1448: Count Good Nodes in Binary Tree</a> — **Medium**

### Pattern 09: Iterative Tree Algorithms (Technique Only — 0 New Questions)
- Provides explicit stack implementations for LC 144, LC 94, LC 145.

### Pattern 10: Hidden Tree Recognition (5 Questions)
1. <a href="https://leetcode.com/problems/subtree-of-another-tree/" target="_blank">LeetCode 572: Subtree of Another Tree</a> — **Easy**
2. <a href="https://leetcode.com/problems/find-leaves-of-binary-tree/" target="_blank">LeetCode 366: Find Leaves of Binary Tree</a> — **Medium**
3. <a href="https://leetcode.com/problems/pseudo-palindromic-paths-in-a-binary-tree/" target="_blank">LeetCode 1457: Pseudo-Palindromic Paths in a Binary Tree</a> — **Medium**
4. <a href="https://leetcode.com/problems/amount-of-time-for-binary-tree-to-be-infected/" target="_blank">LeetCode 2385: Amount of Time for Binary Tree to Be Infected</a> — **Medium**
5. <a href="https://leetcode.com/problems/all-nodes-distance-k-in-binary-tree/" target="_blank">LeetCode 863: All Nodes Distance K in Binary Tree</a> — **Medium**

---

## ⚡ SDE-1 Interview Mastery Checklist

- [ ] Write recursive Preorder, Inorder, and Postorder traversals from a blank editor without syntax errors.
- [ ] Explain what a recursive function returns to its parent and distinguish local subtree state from global answer updating.
- [ ] Implement BFS level-order snapshot processing using `queue.size()`.
- [ ] Construct 2D tree coordinate systems $(col, row)$ to solve vertical order traversals.
- [ ] Solve Lowest Common Ancestor (LCA) in $O(N)$ time and use LCA to compute path directions or node distances.
- [ ] Reconstruct binary trees from Preorder + Inorder arrays using HashMap partitioning.
- [ ] Serialize and deserialize binary trees into string streams using null delimiters.
- [ ] Prune or transform binary trees in-place using bottom-up postorder traversal.
- [ ] Design state tuples `[state_1, state_2]` for Tree DP problems like House Robber III.
- [ ] Convert recursive DFS traversals into explicit stack-based iterative traversals.
- [ ] Recognize when a tree problem requires parent mapping to enable 3-directional graph traversal.
