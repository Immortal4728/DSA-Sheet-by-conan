# Pattern 02: BST Validation & Ordering Invariants

> BST validity is a global ordering constraint, not merely a comparison with immediate children. Every node must fall strictly within a valid range `(low, high)` inherited from its ancestors.

---

## Why This Pattern Exists

A common mistake in BST validation is comparing a node only to its left and right child:
```text
      10
     /  \
    5    15
        /  \
       6    20    <-- INVALID! Node 6 is in 10's right subtree, but 6 < 10!
```
Comparing node `15` to child `6` locally looks valid (`6 < 15`), but globally violates the BST invariant because `6` is in `10`'s right subtree.

### Inherited Global Bounds

To validate a BST correctly, every node receives inherited min and max constraints from its parent:
```text
                 [ Node ] (low, high)
                 /      \
      (low, Node.val)  (Node.val, high)
```

### Reverse Inorder Traversal (Right $\to$ Node $\to$ Left)

While standard Inorder visits elements in **ascending order**, Reverse Inorder visits elements in **descending order**:
$$\text{Right Subtree} \longrightarrow \text{Current Node} \longrightarrow \text{Left Subtree}$$
This is ideal for cumulative sum problems where smaller keys need the sum of all larger keys.

---

## ☕ Standard Java Templates

### 1. Global Bounds Validation (LC 98)
```java
public boolean isValidBST(TreeNode root) {
    return validate(root, null, null);
}

private boolean validate(TreeNode node, Integer low, Integer high) {
    if (node == null) return true;
    
    // Check global bounds
    if ((low != null && node.val <= low) || (high != null && node.val >= high)) {
        return false;
    }
    
    // Pass updated bounds down to subtrees
    return validate(node.left, low, node.val) && validate(node.right, node.val, high);
}
```

### 2. Reverse Inorder Accumulation (LC 538)
```java
private int runningSum = 0;

public TreeNode convertBST(TreeNode root) {
    if (root == null) return root;
    
    // 1. Visit Right (larger elements first)
    convertBST(root.right);
    
    // 2. Process Node
    runningSum += root.val;
    root.val = runningSum;
    
    // 3. Visit Left (smaller elements last)
    convertBST(root.left);
    
    return root;
}
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Search in a BST** | Pure directional search without global bound checking — belongs to **Pattern 01**. |
| **Trim a BST** | Modifies and reconnects tree pointers based on ranges — belongs to **BST Modification (Pattern 04)**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 1 |
| **Medium** | 3 |
| **Total** | **4** |

---

## 🎯 Question Set (4 Questions)

### Q1. Validate Binary Search Tree
<a href="https://leetcode.com/problems/validate-binary-search-tree/" target="_blank">LeetCode 98</a> — **Medium**

**Target Skill:** Global bound propagation (`low`, `high`) during DFS traversal.

**Core Reasoning:**
- Pass valid range `(low, high)` down the recursive call stack.
- Base case: `null` node is valid (`true`).
- If `node.val <= low` or `node.val >= high`, return `false`.
- Recurse `validate(node.left, low, node.val)` AND `validate(node.right, node.val, high)`.

**Why it belongs here:** Canonical validation problem. Teaches why local child checks are insufficient.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

### Q2. Recover Binary Search Tree
<a href="https://leetcode.com/problems/recover-binary-search-tree/" target="_blank">LeetCode 99</a> — **Medium**

**Target Skill:** Identifying inverted pairs in Inorder sequence and swapping values in-place.

**Core Reasoning:**
- Exactly two node values in a valid BST were swapped by mistake.
- Inorder traversal of a valid BST is strictly increasing. Inverting two nodes creates 1 or 2 adjacent drops (`prev.val > curr.val`).
- Inorder traversal tracking `first`, `second`, and `prev`:
  - On 1st inversion (`prev.val > curr.val`): set `first = prev`, `second = curr`.
  - On 2nd inversion: update `second = curr`.
- Swap values `first.val` and `second.val`.

**Why it belongs here:** Deep application of the BST Inorder sorted invariant to repair corrupted trees.

**Complexity:** Time: $O(N)$, Space: $O(H)$ stack ($O(1)$ space using Morris Traversal).

---

### Q3. Convert BST to Greater Tree
<a href="https://leetcode.com/problems/convert-bst-to-greater-tree/" target="_blank">LeetCode 538</a> — **Medium**

**Target Skill:** Reverse Inorder traversal (Right $\to$ Node $\to$ Left) with running prefix sum.

**Core Reasoning:**
- Transform BST so every key is updated to original key plus sum of all keys greater than original key.
- Reverse Inorder visits nodes in strictly descending order.
- Maintain `runningSum`. For each node: `runningSum += node.val`, then `node.val = runningSum`.

**Why it belongs here:** Canonical reverse inorder accumulation problem.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

### Q4. Two Sum IV — Input is a BST
<a href="https://leetcode.com/problems/two-sum-iv-input-is-a-bst/" target="_blank">LeetCode 653</a> — **Easy**

**Target Skill:** Exploiting BST search/inorder ordering for pair sum finding.

**Core Reasoning:**
- Find if there exist two elements in BST whose sum equals `k`.
- **Approach 1 (HashSet DFS):** Traverse tree, check `set.contains(k - node.val)`. If true, return `true`; else `set.add(node.val)`. Time $O(N)$, Space $O(N)$.
- **Approach 2 (Two Pointers via Inorder Iterators):** Dual BST iterators (one forward Inorder, one reverse Inorder) acting as `left` and `right` pointers. Time $O(N)$, Space $O(H)$.

**Why it belongs here:** Bridges Array Two Sum logic onto BST traversal structures.

**Complexity:** Time: $O(N)$, Space: $O(N)$ (or $O(H)$ using dual iterators).

---

## ⚡ Mastery Checklist

- [ ] Can you explain why checking `node.left.val < node.val < node.right.val` locally fails to validate a BST?
- [ ] How do you handle `Integer.MIN_VALUE` and `Integer.MAX_VALUE` bounds cleanly in `Validate BST`?
- [ ] Can you find the two swapped nodes in `Recover BST` during a single Inorder pass?
- [ ] Why does Reverse Inorder (Right $\to$ Node $\to$ Left) visit elements in strictly descending order?
