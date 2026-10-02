# Pattern 04: BST Modification

> Modifying a BST (insertion, deletion, trimming, splitting) requires updating parent-child pointer connections while preserving the strict BST ordering invariant across all remaining nodes.

---

## Why This Pattern Exists

Unlike array insertions or deletions that require $O(N)$ element shifts, modifying a BST can be done in **$O(H)$ time** by rewiring pointer references.

The core paradigm for recursive tree modification:
```java
node.left = modify(node.left, target);
node.right = modify(node.right, target);
return node; // Return updated subtree root to parent
```

---

## BST Deletion Mechanics (LC 450)

Deleting a node from a BST is a fundamental SDE interview requirement. It requires handling 3 distinct structural cases:

```text
                  [ Node to Delete ]
                 /                  \
         Case 1: 0 Children    --> Return null
         Case 2: 1 Child       --> Return non-null child
         Case 3: 2 Children    --> 1. Find Inorder Successor (min in right subtree)
                                   2. Overwrite node's value with successor's value
                                   3. Recursively delete successor from right subtree
```

### 3 Structural Deletion Cases

1. **Case 1: Node has NO children (Leaf Node):**
   - Simply return `null` to sever the connection from parent.
2. **Case 2: Node has ONE child:**
   - Return the non-null child (`left != null ? left : right`) to bypass the deleted node.
3. **Case 3: Node has TWO children:**
   - Find the **Inorder Successor** (smallest node in right subtree `findMin(node.right)`).
   - Copy successor's value to `node.val`.
   - Recursively delete successor node from right subtree (`node.right = deleteNode(node.right, successor.val)`).

---

## ☕ Standard Java Templates

### 1. BST Deletion ($O(H)$ Time, $O(H)$ Space)
```java
public TreeNode deleteNode(TreeNode root, int key) {
    if (root == null) return null;
    
    if (key < root.val) {
        root.left = deleteNode(root.left, key);
    } else if (key > root.val) {
        root.right = deleteNode(root.right, key);
    } else {
        // Found node to delete!
        if (root.left == null) return root.right;  // Case 1 & Case 2 (no left)
        if (root.right == null) return root.left; // Case 2 (no right)
        
        // Case 3: 2 Children -> Find min in right subtree
        TreeNode minNode = findMin(root.right);
        root.val = minNode.val; // Overwrite value
        root.right = deleteNode(root.right, minNode.val); // Delete duplicate in right
    }
    return root;
}

private TreeNode findMin(TreeNode node) {
    while (node.left != null) node = node.left;
    return node;
}
```

### 2. Trimming a BST (LC 669)
```java
public TreeNode trimBST(TreeNode root, int low, int high) {
    if (root == null) return null;
    
    // If value is too small, entire left subtree is out of range!
    if (root.val < low) return trimBST(root.right, low, high);
    
    // If value is too large, entire right subtree is out of range!
    if (root.val > high) return trimBST(root.left, low, high);
    
    // Value is in range: trim both subtrees
    root.left = trimBST(root.left, low, high);
    root.right = trimBST(root.right, low, high);
    return root;
}
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Balance a BST** | Reconstructs complete tree to minimize height — belongs to **Construction & Balance (Pattern 05)**. |
| **Invert Binary Tree** | Swaps left/right children unconditionally without BST rules — belongs to **Binary Trees (Module 06)**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Medium** | 4 |
| **Total** | **4** |

---

## 🎯 Question Set (4 Questions)

### Q1. Insert into a Binary Search Tree
<a href="https://leetcode.com/problems/insert-into-a-binary-search-tree/" target="_blank">LeetCode 701</a> — **Medium**

**Target Skill:** Leaf position discovery and pointer connection.

**Core Reasoning:**
- Search for value `val` using standard BST rules.
- When reaching a `null` reference, create `new TreeNode(val)` and return it.
- **Recursive:** `root.left = insertIntoBST(root.left, val)` if `val < root.val`.
- **Iterative:** Traverse with `curr` until `curr.left == null` or `curr.right == null`, then attach new node.

**Why it belongs here:** Entry problem for BST pointer modification.

**Complexity:** Time: $O(H)$, Space: $O(H)$ recursive / $O(1)$ iterative.

---

### Q2. Delete Node in a BST
<a href="https://leetcode.com/problems/delete-node-in-a-bst/" target="_blank">LeetCode 450</a> — **Medium**

**Target Skill:** Handling 3 structural deletion cases preserving BST invariants.

**Core Reasoning:**
- Locate `key` using search.
- Apply 3-case deletion logic: leaf $\to$ return null; 1 child $\to$ return child; 2 children $\to$ replace with min of right subtree and recurse right.

**Why it belongs here:** **Canonical home for LC 450.** Quintessential BST modification requirement for SDE interviews.

**Complexity:** Time: $O(H)$, Space: $O(H)$.

---

### Q3. Trim a Binary Search Tree
<a href="https://leetcode.com/problems/trim-a-binary-search-tree/" target="_blank">LeetCode 669</a> — **Medium**

**Target Skill:** Range-based subtree bypass and pruning.

**Core Reasoning:**
- Trim tree so all remaining elements lie within `[low, high]`.
- If `root.val < low`, all elements in `root.left` are also $< low$. Prune `root` and `root.left`, returning `trimBST(root.right, low, high)`.
- If `root.val > high`, prune `root` and `root.right`, returning `trimBST(root.left, low, high)`.
- If in range, trim children recursively.

**Why it belongs here:** Demonstrates pruning entire subtrees based on BST ordering bounds.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

### Q4. Split BST
<a href="https://leetcode.com/problems/split-bst/" target="_blank">LeetCode 776</a> — **Medium**

**Target Skill:** Partitioning a BST into two valid BSTs based on value threshold $V$.

**Core Reasoning:**
- Split tree into two trees: $T_1$ (all nodes $\le V$) and $T_2$ (all nodes $> V$).
- Return array `TreeNode[]{T1, T2}`.
- If `root == null`, return `new TreeNode[]{null, null}`.
- If `root.val <= V`: `root` belongs to $T_1$. Recurse right: `sub = splitBST(root.right, V)`. `root.right = sub[0]`, return `[root, sub[1]]`.
- If `root.val > V`: `root` belongs to $T_2$. Recurse left: `sub = splitBST(root.left, V)`. `root.left = sub[1]`, return `[sub[0], root]`.

**Why it belongs here:** Advanced structural partition problem extending BST modification principles.

**Complexity:** Time: $O(H)$, Space: $O(H)$.

---

## ⚡ Mastery Checklist & Follow-up Q&A

- [ ] Can you implement all 3 deletion cases of `Delete Node in a BST` from scratch?
- [ ] *Follow-up:* Can you replace the deleted node with its **Inorder Predecessor** (max in left subtree) instead of Inorder Successor? (Yes, both preserve BST validity!)
- [ ] *Follow-up:* What is the worst-case time complexity of BST deletion on a skewed tree? ($O(N)$ when height $H = N$).
- [ ] Why does `Trim BST` return `trimBST(root.right)` directly when `root.val < low`?
