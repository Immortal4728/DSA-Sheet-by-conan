# Pattern 03: Ordered Traversal / Predecessor / Successor

> In a BST, the **Inorder Successor** of a node $P$ is the node with the smallest key strictly greater than $P.val$. The **Inorder Predecessor** is the node with the largest key strictly smaller than $P.val$.

---

## Why This Pattern Exists

Finding neighboring elements in a sorted sequence (predecessor and successor) or finding rank statistics ($K$-th smallest/largest) is a fundamental capability of balanced search trees.

While building a sorted array from Inorder traversal takes $O(N)$ time and space, we can exploit the BST ordering invariant to answer these queries in **$O(H)$ time** and **$O(1)$ to $O(H)$ space**.

### Inorder Successor Rules

Given a target node $P$ in a BST:
1. **Case 1: Node $P$ has a Right Subtree:**
   - The successor is the **leftmost node** in $P$'s right subtree (`min(P.right)`).
2. **Case 2: Node $P$ has NO Right Subtree:**
   - The successor is the **lowest ancestor** of $P$ whose left subtree contains $P$.
   - Using binary search from root: track `ancestor` whenever we move LEFT (`p.val < curr.val`).

```text
       Successor (p.val < curr.val, turn left)
      /
    ...
    /
   P (target, no right child)
```

---

## ☕ Standard Java Templates

### 1. Inorder Successor via Binary Search ($O(H)$ Time, $O(1)$ Space)
```java
public TreeNode inorderSuccessor(TreeNode root, TreeNode p) {
    TreeNode successor = null;
    TreeNode curr = root;
    
    while (curr != null) {
        if (p.val < curr.val) {
            successor = curr; // Potential successor, move left
            curr = curr.left;
        } else {
            curr = curr.right; // Move right, successor unchanged
        }
    }
    
    return successor;
}
```

### 2. Kth Smallest Early-Exit Inorder ($O(H + K)$ Time, $O(H)$ Space)
```java
private int count = 0;
private int result = -1;

public int kthSmallest(TreeNode root, int k) {
    inorder(root, k);
    return result;
}

private void inorder(TreeNode node, int k) {
    if (node == null || count >= k) return;
    
    inorder(node.left, k);
    
    count++;
    if (count == k) {
        result = node.val;
        return; // Early stop!
    }
    
    inorder(node.right, k);
}
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Lowest Common Ancestor of a BST** | Finds split ancestor for two target nodes — belongs to **Pattern 06**. |
| **Search in a BST** | Simple single-element lookup — belongs to **Pattern 01**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Medium** | 5 (3 LeetCode + 2 Interview Follow-ups) |
| **Total** | **5** |

---

## 🎯 Question Set & Interview Follow-ups (5 Items)

### Q1. Kth Smallest Element in a BST
<a href="https://leetcode.com/problems/kth-smallest-element-in-a-bst/" target="_blank">LeetCode 230</a> — **Medium**

**Target Skill:** Early-stopping Inorder traversal.

**Core Reasoning:**
- Inorder traversal visits elements in ascending order.
- Maintain `count`. Increment on visiting each node.
- When `count == k`, capture `node.val` and stop further traversal.

**Why it belongs here:** Canonical rank-statistic problem on BSTs.

**Complexity:** Time: $O(H + K)$, Space: $O(H)$.

---

### Q2. Kth Largest Element in a BST
**Interview Follow-Up** — **Medium**

**Target Skill:** Reverse Inorder traversal (Right $\to$ Node $\to$ Left) with early exit.

**Core Reasoning:**
- Reverse Inorder visits nodes in descending order ($1\text{st}$ largest, $2\text{nd}$ largest, ...).
- Recurse right first, increment count at current node, stop when `count == k`.

**Interview Q&A:**
- *Q: How does Reverse Inorder produce the $K$-th largest without modifying the tree?*
- *A:* Traversing `Right -> Node -> Left` processes elements in exact decreasing order. The $K$-th node visited in this order is the $K$-th largest.

**Complexity:** Time: $O(H + K)$, Space: $O(H)$.

---

### Q3. Inorder Successor in BST
<a href="https://leetcode.com/problems/inorder-successor-in-bst/" target="_blank">LeetCode 285</a> — **Medium**

**Target Skill:** Binary search traversal tracking candidate successor node.

**Core Reasoning:**
- Start at `root`. While `curr != null`:
  - If `p.val < curr.val`: `curr` could be successor! Save `successor = curr`, move `curr = curr.left`.
  - If `p.val >= curr.val`: successor must be in right subtree. Move `curr = curr.right`.

**Why it belongs here:** Quintessential successor search algorithm in $O(H)$ time and $O(1)$ space.

**Complexity:** Time: $O(H)$, Space: $O(1)$.

---

### Q4. Inorder Successor in BST II
<a href="https://leetcode.com/problems/inorder-successor-in-bst-ii/" target="_blank">LeetCode 510</a> — **Medium**

**Target Skill:** Successor navigation when given parent pointers (`Node` has `left`, `right`, `parent`).

**Core Reasoning:**
- You are given node `node` directly (without tree root reference).
- **Case 1 (Right child exists):** Go right once, then left as far as possible (`while (curr.left != null) curr = curr.left`).
- **Case 2 (No right child):** Climb `parent` pointers. Successor is the first parent where `node` is in `parent`'s **left** subtree (`while (node.parent != null && node == node.parent.right) node = node.parent; return node.parent;`).

**Why it belongs here:** Teaches structural parent-pointer traversal in BSTs.

**Complexity:** Time: $O(H)$, Space: $O(1)$.

---

### Q5. Inorder Predecessor in BST
**Interview Follow-Up** — **Medium**

**Target Skill:** Dual binary search for largest key smaller than target $P$.

**Core Reasoning:**
- Predecessor is the node with largest value strictly smaller than `p.val`.
- Start at `root`. While `curr != null`:
  - If `p.val > curr.val`: `curr` is a candidate predecessor! Save `predecessor = curr`, move `curr = curr.right`.
  - If `p.val <= curr.val`: move `curr = curr.left`.

**Interview Q&A:**
- *Q: What if $P$ has a left child?*
- *A:* If $P$ has a left child, its predecessor is the **rightmost node in $P$'s left subtree** (`max(P.left)`).

**Complexity:** Time: $O(H)$, Space: $O(1)$.

---

## ⚡ Mastery Checklist

- [ ] Can you implement `kthSmallest` with early exit in $O(H + K)$ time without converting to an array?
- [ ] How do you find `Inorder Successor` in $O(1)$ space using binary search?
- [ ] What is the logic for finding `Inorder Successor` when nodes have `parent` pointers?
- [ ] How do you adapt `Inorder Successor` to write `Inorder Predecessor`?
