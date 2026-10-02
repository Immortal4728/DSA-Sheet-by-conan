# Pattern 05: BST Construction & Balance

> Building a height-balanced BST from ordered data requires selecting the median element as the root, ensuring subtrees contain equal numbers of elements, and recursively constructing left and right subtrees.

---

## Why This Pattern Exists

A skewed BST (like a linked list `1 -> 2 -> 3 -> 4`) degrades search, insertion, and deletion performance from $O(\log N)$ to $O(N)$.

To construct or restore a height-balanced BST (where depth difference between subtrees of any node is $\le 1$):
```text
                     [ Sorted Data Array ]
                  /           |           \
           Left Half       Middle       Right Half
          (Left Subtree)   (Root)     (Right Subtree)
```

By picking the **middle element** as the root, we guarantee that half the remaining elements go to the left subtree and half go to the right subtree, producing an optimal height of $H = \lceil \log_2 N \rceil$.

---

## ☕ Standard Java Templates

### 1. Divide & Conquer Array to Balanced BST (LC 108)
```java
public TreeNode sortedArrayToBST(int[] nums) {
    return build(nums, 0, nums.length - 1);
}

private TreeNode build(int[] nums, int left, int right) {
    if (left > right) return null;
    
    int mid = left + (right - left) / 2; // Choose middle element as root
    TreeNode root = new TreeNode(nums[mid]);
    
    root.left = build(nums, left, mid - 1);
    root.right = build(nums, mid + 1, right);
    
    return root;
}
```

### 2. $O(N)$ Preorder to BST Construction using Upper Bounds (LC 1008)
```java
private int idx = 0;

public TreeNode bstFromPreorder(int[] preorder) {
    return build(preorder, Integer.MAX_VALUE);
}

private TreeNode build(int[] preorder, int bound) {
    if (idx >= preorder.length || preorder[idx] > bound) {
        return null; // Exceeds upper bound for current subtree
    }
    
    TreeNode root = new TreeNode(preorder[idx++]);
    root.left = build(preorder, root.val);  // Left child values must be < root.val
    root.right = build(preorder, bound);    // Right child values must be < bound
    
    return root;
}
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Construct Tree from Preorder & Inorder** | Arbitrary binary tree construction using index maps — belongs to **Binary Trees (Module 06)**. |
| **Insert into a BST** | Adds single element to existing BST — belongs to **BST Modification (Pattern 04)**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 1 |
| **Medium** | 3 |
| **Total** | **4** |

---

## 🎯 Question Set (4 Questions)

### Q1. Convert Sorted Array to Binary Search Tree
<a href="https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/" target="_blank">LeetCode 108</a> — **Easy**

**Target Skill:** Divide-and-conquer median selection for balanced tree construction.

**Core Reasoning:**
- Array is sorted in ascending order.
- Pick `mid = (left + right) / 2` as root.
- Recurse `left` half for `root.left`, `right` half for `root.right`.

**Why it belongs here:** Canonical entry problem for balanced BST construction.

**Complexity:** Time: $O(N)$, Space: $O(\log N)$ stack.

---

### Q2. Convert Sorted List to Binary Search Tree
<a href="https://leetcode.com/problems/convert-sorted-list-to-binary-search-tree/" target="_blank">LeetCode 109</a> — **Medium**

**Target Skill:** Fast & Slow pointers median finding vs Inorder simulation on linked lists.

**Core Reasoning:**
- Linked List cannot be accessed by index in $O(1)$.
- **Approach 1 (Fast & Slow Pointers):** Find mid node using slow/fast pointers ($O(N \log N)$ time).
- **Approach 2 (Inorder Simulation):** Convert list to array ($O(N)$ space) OR advance list head pointer sequentially matching Inorder traversal ($O(N)$ time, $O(\log N)$ space).

**Why it belongs here:** Bridges Linked List pointer navigation (Module 04) with BST construction.

**Complexity:** Time: $O(N)$, Space: $O(\log N)$.

---

### Q3. Balance a Binary Search Tree
<a href="https://leetcode.com/problems/balance-a-binary-search-tree/" target="_blank">LeetCode 1382</a> — **Medium**

**Target Skill:** Flattening an unbalanced BST to sorted array then re-balancing.

**Core Reasoning:**
- Given an unbalanced BST.
- Step 1: Inorder traversal to extract all elements into a sorted array (`List<Integer>`). Time $O(N)$.
- Step 2: Run `sortedArrayToBST` on the array to rebuild a perfectly balanced BST. Time $O(N)$.

**Why it belongs here:** Quintessential re-balancing algorithm using Inorder array conversion.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

### Q4. Construct Binary Search Tree from Preorder Traversal
<a href="https://leetcode.com/problems/construct-binary-search-tree-from-preorder-traversal/" target="_blank">LeetCode 1008</a> — **Medium**

**Target Skill:** $O(N)$ upper bound parameter passing for preorder BST reconstruction.

**Core Reasoning:**
- Preorder array gives root first, followed by left subtree nodes, then right subtree nodes.
- **Naive $O(N^2)$ Approach:** Insert elements one by one using BST insertion.
- **Optimal $O(N)$ Approach:** Pass `bound` parameter. Next element belongs to left child if `< root.val`, else belongs to right child if `< bound`.

**Why it belongs here:** Advanced BST construction problem demonstrating upper-bound pruning without sorting.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

## ⚡ Mastery Checklist & Follow-up Q&A

- [ ] *Q: Why does picking the middle element produce a height-balanced BST?*
  - *A:* Because it distributes $\lfloor N/2 \rfloor$ elements into left and right subtrees, guaranteeing depth $\lceil \log_2 N \rceil$.
- [ ] *Q: Can a BST be constructed from Preorder in $O(N)$ time without sorting?*
  - *A:* Yes! Using an inherited `bound` constraint parameter (`dfs(preorder, bound)`), each node is processed exactly once in $O(N)$ total time.
- [ ] Can you implement `Balance a BST` (LC 1382) in two simple steps?
