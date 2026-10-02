# Pattern 10: Hidden Tree Recognition

> In interviews, problems are rarely titled with their exact algorithm. Success requires reading the problem description, uncovering the implicit structural constraints, and deriving the tree representation required.

---

## Why This Pattern Exists

When encountering an unfamiliar interview problem, you must refrain from blindly applying memorized templates.

First ask:
1. *"What structural representation or information does this problem require?"*
2. *"Do I need to traverse upwards toward parents as well as downwards to children?"* $\longrightarrow$ Requires building a parent mapping or converting tree nodes to graph adjacency lists.
3. *"Does the problem depend on distance from bottom leaf nodes?"* $\longrightarrow$ Requires postorder height calculations.
4. *"Can path frequencies be encoded efficiently?"* $\longrightarrow$ Requires bitmask state passing down DFS branches.

---

## ☕ Standard Java Templates

### Parent Mapping for Bidirectional Traversal (LC 863 / LC 2385)
```java
// Step 1: Map each node to its parent pointer using DFS
Map<TreeNode, TreeNode> parentMap = new HashMap<>();

private void buildParentMap(TreeNode node, TreeNode parent) {
    if (node == null) return;
    parentMap.put(node, parent);
    buildParentMap(node.left, node);
    buildParentMap(node.right, node);
}

// Step 2: Perform BFS starting from target node moving to (left, right, parent)
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Path Sum III** | Pure downward path tracking with prefix sum map — belongs to **Tree DFS (Pattern 02)**. |
| **Lowest Common Ancestor** | Direct recursive ancestor bubbling without parent maps — belongs to **LCA (Pattern 05)**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 1 |
| **Medium** | 4 |
| **Total** | **5** |

---

## 🎯 Question Set (5 Questions)

### Q1. Subtree of Another Tree
<a href="https://leetcode.com/problems/subtree-of-another-tree/" target="_blank">LeetCode 572</a> — **Easy**

**Target Skill:** Dual-level recursive decomposition (outer search vs inner structural equality).

**Core Reasoning:**
- Determine if binary tree `subRoot` is a subtree of `root`.
- Outer recursive function checks if `isSameTree(root, subRoot)` is true.
- If not, recurse `isSubtree(root.left, subRoot) || isSubtree(root.right, subRoot)`.

**Why it belongs here:** Teaches decomposing a problem into structural matching at an unknown target node location.

**Complexity:** Time: $O(N \cdot M)$, Space: $O(H)$.

---

### Q2. Find Leaves of Binary Tree
<a href="https://leetcode.com/problems/find-leaves-of-binary-tree/" target="_blank">LeetCode 366</a> — **Medium**

**Target Skill:** Height-from-bottom indexing for leaf removal layering.

**Core Reasoning:**
- Collect and remove leaves level by level until tree is empty.
- Instead of repeatedly modifying pointers, compute **height from bottom leaves**:
  $$\text{height}(node) = 1 + \max(\text{height}(node.left), \text{height}(node.right))$$
- Nodes with height `0` are removed in 1st round, height `1` in 2nd round, height `H` in $H$-th round.
- Group node values by `height` into `result.get(height)`.

**Why it belongs here:** Discovers that iterative leaf removal is mathematically identical to postorder height grouping.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

### Q3. Pseudo-Palindromic Paths in a Binary Tree
<a href="https://leetcode.com/problems/pseudo-palindromic-paths-in-a-binary-tree/" target="_blank">LeetCode 1457</a> — **Medium**

**Target Skill:** Bitmask parity tracking along root-to-leaf paths.

**Core Reasoning:**
- A path can form a palindrome if at most ONE digit has an odd frequency.
- Track digit frequency using bitwise XOR bitmask: `pathMask ^= (1 << node.val)`.
- At leaf node, check if `(pathMask & (pathMask - 1)) == 0` (popcount $\le 1$).

**Why it belongs here:** Combines tree DFS root-to-leaf path traversal with bit manipulation state optimization.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

### Q4. Amount of Time for Binary Tree to Be Infected
<a href="https://leetcode.com/problems/amount-of-time-for-binary-tree-to-be-infected/" target="_blank">LeetCode 2385</a> — **Medium**

**Target Skill:** Structural transformation into undirected graph for radial infection spread.

**Core Reasoning:**
- Infection spreads from `start` node to left child, right child, AND parent every minute.
- Standard tree traversal cannot move to parent. Convert tree to undirected Graph adjacency map (`Map<Integer, List<Integer>>`).
- Perform BFS starting from `start` node to calculate maximum radial depth.

**Why it belongs here:** Excellent recognition problem where tree constraints must be relaxed into graph adjacency traversal.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

### Q5. All Nodes Distance K in Binary Tree
<a href="https://leetcode.com/problems/all-nodes-distance-k-in-binary-tree/" target="_blank">LeetCode 863</a> — **Medium**

**Target Skill:** Parent mapping and 3-directional BFS distance exploration.

**Core Reasoning:**
- Find all nodes at distance $K$ from target node.
- Build `parentMap` using DFS.
- Perform BFS starting from `target` node, exploring `left`, `right`, and `parent`.
- Maintain `visited` set. When BFS level equals $K$, collect all nodes in current queue level.

**Why it belongs here:** Canonical problem demonstrating tree parent mapping to enable distance-based graph traversal.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

## ⚡ Mastery Checklist

- [ ] Can you explain why `Find Leaves` reduces to calculating node height from bottom?
- [ ] How does a bitwise XOR bitmask check if at most one digit has an odd frequency?
- [ ] When presented with bidirectional distance queries on a tree, do you know how to build a parent pointer map?
- [ ] Can you articulate why converting a tree to an undirected graph solves `Infection Time` (LC 2385)?
