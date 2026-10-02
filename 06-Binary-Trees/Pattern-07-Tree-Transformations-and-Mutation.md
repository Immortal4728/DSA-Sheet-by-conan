# Pattern 07: Tree Transformations & Mutation

> Tree mutation modifies node values, swaps pointers, prunes invalid subtrees, or restructures tree topology in-place. Postorder traversal is dominant because child modifications must finalize before parent rewiring occurs.

---

## Why This Pattern Exists

Unlike read-only tree algorithms, tree transformation requires updating existing pointers or returning modified child references up to the parent.

Core Mental Model:
```java
// 1. Transform child subtrees recursively
node.left = transform(node.left);
node.right = transform(node.right);

// 2. Perform local mutation / pruning on current node
if (shouldDelete(node)) {
    return null; // Return null to sever connection from parent
}
return node;
```

Common Use Cases:
- **Mirroring / Inverting:** Swapping `node.left` and `node.right`.
- **In-Place Flattening:** Converting a 2D binary tree into a 1D singly linked list using existing `right` pointers.
- **Subtree Pruning:** Deleting nodes that fail a condition (e.g., subtrees containing only `0`s).
- **Forest Decomposition:** Disconnecting deleted nodes and collecting newly orphaned subtrees.

---

## ☕ Standard Java Templates

### Subtree Pruning Pattern (LC 814)
```java
public TreeNode pruneTree(TreeNode root) {
    if (root == null) return null;
    
    // Postorder: Prune children first
    root.left = pruneTree(root.left);
    root.right = pruneTree(root.right);
    
    // Prune current node if it's a leaf with value 0
    if (root.val == 0 && root.left == null && root.right == null) {
        return null; // Sever link from parent
    }
    
    return root;
}
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Flatten Binary Tree (LC 114)** | Belongs **exclusively to Pattern 07** as an in-place right-pointer restructuring exercise. |
| **Construct Tree from Preorder/Inorder** | Creates new nodes from arrays — belongs to **Construction (Pattern 06)**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 2 |
| **Medium** | 3 |
| **Total** | **5** |

---

## 🎯 Question Set (5 Questions)

### Q1. Invert Binary Tree
<a href="https://leetcode.com/problems/invert-binary-tree/" target="_blank">LeetCode 226</a> — **Easy**

**Target Skill:** In-place left/right child pointer swapping.

**Core Reasoning:**
- Base case: `if (root == null) return null;`
- Swap `root.left` and `root.right`.
- Recurse `invertTree(root.left)` and `invertTree(root.right)`.
- Return `root`.

**Why it belongs here:** Canonical tree pointer mutation problem.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

### Q2. Merge Two Binary Trees
<a href="https://leetcode.com/problems/merge-two-binary-trees/" target="_blank">LeetCode 617</a> — **Easy**

**Target Skill:** Dual-tree in-place overlap mutation.

**Core Reasoning:**
- If `t1 == null`, return `t2`. If `t2 == null`, return `t1`.
- `t1.val += t2.val`.
- `t1.left = mergeTrees(t1.left, t2.left)`.
- `t1.right = mergeTrees(t1.right, t2.right)`.
- Return `t1`.

**Why it belongs here:** Demonstrates in-place mutation combining two distinct tree structures.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

### Q3. Flatten Binary Tree to Linked List
<a href="https://leetcode.com/problems/flatten-binary-tree-to-linked-list/" target="_blank">LeetCode 114</a> — **Medium**

**Target Skill:** In-place right-pointer rewiring using Reverse Preorder (Right $\to$ Left $\to$ Root).

**Core Reasoning:**
- Target list ordering matches Preorder (Root $\to$ Left $\to$ Right).
- **Reverse Preorder Trick:** Process in reverse (Right $\to$ Left $\to$ Root) while maintaining `prev` pointer.
- `flatten(root.right)` $\to$ `flatten(root.left)`.
- Set `root.right = prev`, `root.left = null`.
- Update `prev = root`.

**Why it belongs here:** **Canonical home for LC 114.** Excellent pointer restructuring without allocating new nodes.

**Complexity:** Time: $O(N)$, Space: $O(H)$ recursion stack (or $O(1)$ space using Morris Traversal).

---

### Q4. Binary Tree Pruning
<a href="https://leetcode.com/problems/binary-tree-pruning/" target="_blank">LeetCode 814</a> — **Medium**

**Target Skill:** Postorder bottom-up subtree removal.

**Core Reasoning:**
- Remove all subtrees that do NOT contain a `1`.
- Recurse postorder: `root.left = pruneTree(root.left)`, `root.right = pruneTree(root.right)`.
- If `root.val == 0 && root.left == null && root.right == null`, return `null`.

**Why it belongs here:** Replaces trivial insertion problems (like LC 623) with deep postorder conditional pruning.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

### Q5. Delete Nodes And Return Forest
<a href="https://leetcode.com/problems/delete-nodes-and-return-forest/" target="_blank">LeetCode 1110</a> — **Medium**

**Target Skill:** Node deletion with root orphan collection for forest generation.

**Core Reasoning:**
- Given set `to_delete`.
- Postorder DFS helper `process(node, isRoot)`:
  - Check if `deleted = to_delete.contains(node.val)`.
  - If `isRoot && !deleted`, add `node` to `forest` result list.
  - `node.left = process(node.left, deleted)`
  - `node.right = process(node.right, deleted)`
  - Return `deleted ? null : node`.

**Why it belongs here:** Advanced mutation problem that breaks a single binary tree into a collection of disjoint trees (forest).

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

## ⚡ Mastery Checklist

- [ ] Can you invert a binary tree recursively and iteratively in under 3 minutes?
- [ ] Why does `Flatten Binary Tree` work cleanly when processed in Reverse Preorder (Right $\to$ Left $\to$ Root)?
- [ ] Do you know why `Binary Tree Pruning` MUST use Postorder traversal instead of Preorder?
- [ ] How do you handle registering new root nodes in `Delete Nodes And Return Forest` when a parent node is deleted?
