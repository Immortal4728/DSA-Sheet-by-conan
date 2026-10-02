# Pattern 07: Hidden BST Recognition (Recognition Layer Only)

> In technical interviews, problems involving BSTs will often present as arbitrary tree, array, or range query problems. The candidate's primary challenge is identifying that the underlying structure is a BST and leveraging its ordering properties.

---

## 🛑 Important Note on Curriculum Architecture

> **This is a RECOGNITION ONLY pattern.** It does NOT introduce new LeetCode questions to the syllabus. It uses canonical problems introduced in earlier patterns as unlabeled recognition exercises:
> 1. **Recover Binary Search Tree** (<a href="https://leetcode.com/problems/recover-binary-search-tree/" target="_blank">LeetCode 99</a> — Pattern 02)
> 2. **Find Mode in Binary Search Tree** (<a href="https://leetcode.com/problems/find-mode-in-binary-search-tree/" target="_blank">LeetCode 501</a> — Pattern 01)
> 3. **Two Sum IV — Input is a BST** (<a href="https://leetcode.com/problems/two-sum-iv-input-is-a-bst/" target="_blank">LeetCode 653</a> — Pattern 02)
> 4. **Closest Nodes Queries in a BST** (<a href="https://leetcode.com/problems/closest-nodes-queries-in-a-binary-search-tree/" target="_blank">LeetCode 2476</a> — Pattern 06)
> 5. **Kth Smallest Element in a BST** (<a href="https://leetcode.com/problems/kth-smallest-element-in-a-bst/" target="_blank">LeetCode 230</a> — Pattern 03)
> 6. **Range Sum of BST** (<a href="https://leetcode.com/problems/range-sum-of-bst/" target="_blank">LeetCode 938</a> — Pattern 06)

---

## The First-Principles Recognition Question

When presented with an unfamiliar tree problem, never ask: *"Which memorized template matches this title?"*

Instead, ask:
> *"What property does this tree give me that an arbitrary binary tree does not?"*

```text
                           [ BST Structure ]
                                  │
      ┌───────────────────────────┼───────────────────────────┐
      ▼                           ▼                           ▼
[ Inorder = Sorted ]    [ Single-Path Search ]       [ Branch Pruning ]
      │                           │                           │
  - LC 99 (Recover)         - LC 230 (Kth Smallest)     - LC 938 (Range Sum)
  - LC 501 (Find Mode)      - LC 2476 (Closest Nodes)   - LC 235 (LCA in BST)
  - LC 653 (Two Sum IV)
```

---

## 🧠 Recognition Diagnostic Table

| Problem Statement Hint | Uncovered Constraint | Derived Strategy | Canonical Reference |
|------------------------|----------------------|------------------|---------------------|
| *"Find two swapped nodes in a tree"* | Swapping nodes disrupts sorted inorder sequence. | Track adjacent inversions (`prev > curr`) during Inorder traversal. | <a href="https://leetcode.com/problems/recover-binary-search-tree/" target="_blank">LC 99</a> |
| *"Find most frequent values without HashMap"* | Inorder traversal groups duplicate keys consecutively. | Inorder traversal with `currCount` and `maxCount` tracking. | <a href="https://leetcode.com/problems/find-mode-in-binary-search-tree/" target="_blank">LC 501</a> |
| *"Find pair of nodes summing to target K"* | BST Inorder yields sorted array structure. | Forward and Reverse Inorder iterators as Two Pointers. | <a href="https://leetcode.com/problems/two-sum-iv-input-is-a-bst/" target="_blank">LC 653</a> |
| *"Batch queries for closest values in tree"* | Skewed BST search is $O(N)$ per query; sorted array binary search is $O(\log N)$. | Flatten BST via Inorder to sorted array, then binary search. | <a href="https://leetcode.com/problems/closest-nodes-queries-in-a-binary-search-tree/" target="_blank">LC 2476</a> |
| *"Find the K-th smallest node in tree"* | Inorder traversal visits nodes in exact $1\text{st}, 2\text{nd}, \dots, K\text{-th}$ rank order. | Inorder traversal with early exit at `count == K`. | <a href="https://leetcode.com/problems/kth-smallest-element-in-a-bst/" target="_blank">LC 230</a> |
| *"Sum all elements within range [low, high]"* | Nodes outside range allow pruning entire subtrees. | Skip `left` if `val < low`; skip `right` if `val > high`. | <a href="https://leetcode.com/problems/range-sum-of-bst/" target="_blank">LC 938</a> |

---

## ⚡ Recognition Mastery Checklist

- [ ] Can you explain why `Inorder Traversal` is the primary unlock for 80% of BST recognition problems?
- [ ] When given an arbitrary binary tree vs a BST, how does your time complexity expectation change?
  - *Binary Tree:* $O(N)$ (must visit all nodes)
  - *BST:* $O(H)$ (can eliminate branches)
- [ ] Do you recognize when flattening a BST to an array via Inorder traversal provides a cleaner $O(N + Q \log N)$ batch solution than multiple tree traversals?
