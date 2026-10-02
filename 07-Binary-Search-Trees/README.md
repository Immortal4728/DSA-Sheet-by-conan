# 🌲 Module 07: Binary Search Trees (BST)

> Don't count repetitions. Count genuinely different reasoning. Build on Module 06 (Binary Trees) by mastering ordering invariants, branch pruning, and sorted sequence traversals.

---

## 🎯 Section Objective & SDE-1 Scope

Binary Search Trees (BSTs) extend binary tree recursive decomposition with a strict **spatial ordering invariant**.

Where **Module 06 (Binary Trees)** teaches *"How do I reason about a tree topology?"*, **Module 07 (BST)** teaches *"How can ordering information make that reasoning faster ($O(H)$) and simpler?"*

This curriculum is locked at **24 unique questions** across **7 pattern files**. It focuses on core SDE-1 interview requirements and explicitly excludes competitive-programming bloat (no AVL/Red-Black implementations, Treaps, or Splay Trees).

---

## 🧠 The BST Invariant & Relationship to Module 06

For every node $N$ in a valid BST:
$$\text{All values in } N.\text{left} < N.\text{val} < \text{All values in } N.\text{right}$$

```text
               Binary Trees (Module 06)
              Must search BOTH subtrees: O(N)
                          │
                          ▼
            Binary Search Trees (Module 07)
              Ordering enables branch pruning: O(H)
```

### The Inorder Paradigm
$$\text{BST} \longrightarrow \text{Inorder Traversal (Left } \to \text{ Node } \to \text{ Right)} \longrightarrow \text{Strictly Sorted Sequence}$$

---

## 🗺️ Curriculum Architecture

| Pattern File | Focus Area | Unique Questions | Core Invariant / Mechanism |
|--------------|------------|------------------|----------------------------|
| **[Pattern 01](./Pattern-01-BST-Fundamentals-and-Search.md)** | BST Fundamentals & Search | 3 | $O(H)$ search, adjacent difference in inorder, mode counting |
| **[Pattern 02](./Pattern-02-BST-Validation-and-Ordering.md)** | BST Validation & Ordering | 4 | Inherited global bounds `(low, high)`, recover swapped nodes, reverse inorder |
| **[Pattern 03](./Pattern-03-Ordered-Traversal-Predecessor-and-Successor.md)** | Ordered Traversal & Successor | 5 | Inorder successor/predecessor, rank statistics, parent pointers |
| **[Pattern 04](./Pattern-04-BST-Modification.md)** | BST Modification | 4 | Insertion, 3-case deletion (0, 1, 2 children), trimming, splitting |
| **[Pattern 05](./Pattern-05-BST-Construction-and-Balance.md)** | BST Construction & Balance | 4 | Median selection, divide-and-conquer, upper-bound preorder parsing |
| **[Pattern 06](./Pattern-06-BST-Range-and-Relationship-Reasoning.md)** | BST Range & Relationships | 4 | Branch pruning, $O(1)$ space LCA, range sum, batch query binary search |
| **[Pattern 07](./Pattern-07-Hidden-BST-Recognition.md)** | Hidden BST Recognition | *Recognition* | Unlabeled diagnostic recognition (0 new questions) |

---

## 🔒 Canonical Placement Rules

- **LC 230 (Kth Smallest):** Housed in **Pattern 03** (not duplicated in Pattern 01).
- **LC 450 (Delete Node in BST):** Housed in **Pattern 04** (canonical home for BST deletion).
- **LC 235 (LCA in BST):** Housed in **Pattern 06** ($O(1)$ space iterative BST search).
- **Pattern 07 (Hidden Recognition):** Serves as a diagnostic review layer referencing existing canonical problems without inflating the 24-question total.

---

## 📑 Full Question Matrix (24 Unique Questions)

### Pattern 01: BST Fundamentals & Search (3 Questions)
1. <a href="https://leetcode.com/problems/search-in-a-binary-search-tree/" target="_blank">LeetCode 700: Search in a Binary Search Tree</a> — **Easy**
2. <a href="https://leetcode.com/problems/minimum-absolute-difference-in-bst/" target="_blank">LeetCode 530: Minimum Absolute Difference in BST</a> — **Easy**
3. <a href="https://leetcode.com/problems/find-mode-in-binary-search-tree/" target="_blank">LeetCode 501: Find Mode in Binary Search Tree</a> — **Easy**

### Pattern 02: BST Validation & Ordering Invariants (4 Questions)
1. <a href="https://leetcode.com/problems/validate-binary-search-tree/" target="_blank">LeetCode 98: Validate Binary Search Tree</a> — **Medium**
2. <a href="https://leetcode.com/problems/recover-binary-search-tree/" target="_blank">LeetCode 99: Recover Binary Search Tree</a> — **Medium**
3. <a href="https://leetcode.com/problems/convert-bst-to-greater-tree/" target="_blank">LeetCode 538: Convert BST to Greater Tree</a> — **Medium**
4. <a href="https://leetcode.com/problems/two-sum-iv-input-is-a-bst/" target="_blank">LeetCode 653: Two Sum IV — Input is a BST</a> — **Easy**

### Pattern 03: Ordered Traversal / Predecessor / Successor (5 Questions & Variants)
1. <a href="https://leetcode.com/problems/kth-smallest-element-in-a-bst/" target="_blank">LeetCode 230: Kth Smallest Element in a BST</a> — **Medium**
2. **Kth Largest Element in a BST** — **Interview Follow-Up** (Medium)
3. <a href="https://leetcode.com/problems/inorder-successor-in-bst/" target="_blank">LeetCode 285: Inorder Successor in BST</a> — **Medium**
4. <a href="https://leetcode.com/problems/inorder-successor-in-bst-ii/" target="_blank">LeetCode 510: Inorder Successor in BST II</a> — **Medium**
5. **Inorder Predecessor in BST** — **Interview Follow-Up** (Medium)

### Pattern 04: BST Modification (4 Questions)
1. <a href="https://leetcode.com/problems/insert-into-a-binary-search-tree/" target="_blank">LeetCode 701: Insert into a Binary Search Tree</a> — **Medium**
2. <a href="https://leetcode.com/problems/delete-node-in-a-bst/" target="_blank">LeetCode 450: Delete Node in a BST</a> — **Medium**
3. <a href="https://leetcode.com/problems/trim-a-binary-search-tree/" target="_blank">LeetCode 669: Trim a Binary Search Tree</a> — **Medium**
4. <a href="https://leetcode.com/problems/split-bst/" target="_blank">LeetCode 776: Split BST</a> — **Medium**

### Pattern 05: BST Construction & Balance (4 Questions)
1. <a href="https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/" target="_blank">LeetCode 108: Convert Sorted Array to Binary Search Tree</a> — **Easy**
2. <a href="https://leetcode.com/problems/convert-sorted-list-to-binary-search-tree/" target="_blank">LeetCode 109: Convert Sorted List to Binary Search Tree</a> — **Medium**
3. <a href="https://leetcode.com/problems/balance-a-binary-search-tree/" target="_blank">LeetCode 1382: Balance a Binary Search Tree</a> — **Medium**
4. <a href="https://leetcode.com/problems/construct-binary-search-tree-from-preorder-traversal/" target="_blank">LeetCode 1008: Construct Binary Search Tree from Preorder Traversal</a> — **Medium**

### Pattern 06: BST Range & Relationship Reasoning (4 Questions)
1. <a href="https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/" target="_blank">LeetCode 235: Lowest Common Ancestor of a Binary Search Tree</a> — **Medium**
2. <a href="https://leetcode.com/problems/range-sum-of-bst/" target="_blank">LeetCode 938: Range Sum of BST</a> — **Easy**
3. <a href="https://leetcode.com/problems/closest-binary-search-tree-value/" target="_blank">LeetCode 270: Closest Binary Search Tree Value</a> — **Easy**
4. <a href="https://leetcode.com/problems/closest-nodes-queries-in-a-binary-search-tree/" target="_blank">LeetCode 2476: Closest Nodes Queries in a Binary Search Tree</a> — **Medium**

### Pattern 07: Hidden BST Recognition (Recognition Layer Only — 0 New Questions)
- Diagnostic review layer referencing LC 99, LC 501, LC 653, LC 2476, LC 230, and LC 938.

---

## ⚡ Complexity & Height Analysis

- **Balanced BST ($H = \log N$):** Operations (`Search`, `Insert`, `Delete`, `LCA`) execute in optimal **$O(\log N)$ time**.
- **Skewed BST ($H = N$):** Operations degrade to **$O(N)$ time** (behaves like a singly linked list).
- **Re-balancing Strategy:** Rebuilding via Inorder array conversion (LC 1382) takes $O(N)$ time and restores $H = \lceil \log_2 N \rceil$.

---

## ⚡ SDE-1 Interview Mastery Checklist

- [ ] Explain the BST invariant and why Inorder traversal produces a strictly sorted sequence.
- [ ] Validate a BST using inherited global bounds `(low, high)` rather than local child checks.
- [ ] Find the Inorder Successor in $O(1)$ space using binary search and with parent pointers.
- [ ] Implement 3-case BST deletion (0 children, 1 child, 2 children using Inorder Successor).
- [ ] Construct a balanced BST from a sorted array/list in $O(N)$ time using median selection.
- [ ] Prune branches in Range Sum and LCA queries in $O(H)$ time and $O(1)$ space.
- [ ] Convert a BST to a sorted array to optimize batch closest-value queries ($O(N + Q \log N)$).
