# 🔗 Module 04: Linked Lists

> Arrays give you indexed access. Linked lists give you dynamic pointer chains.
> The skill is not memorizing reversal steps — it is understanding pointer invariants and deriving the correct sequence from the problem's structural requirement.

---

## 📌 Core Linked List Mechanics

### Node Structure
```java
class ListNode {
    int val;
    ListNode next;
    ListNode(int val) { this.val = val; }
}
```

### The Most Important Technique: The Dummy Node

Inserting before the head or deleting the head creates special cases in every linked list problem.

**Solution:** Add a dummy node before the real head. Every node becomes a non-head node from the algorithm's perspective.

```java
ListNode dummy = new ListNode(0);
dummy.next = head;
// operate on dummy.next as the working list
return dummy.next; // real head may have changed
```

> **Rule:** Use a dummy node any time the head itself could be deleted or replaced.

### Time & Space Characteristics
| Operation | Time | Notes |
|-----------|------|-------|
| Access by index | $O(N)$ | No random access — must traverse |
| Insert at head | $O(1)$ | Rewire one pointer |
| Insert at tail | $O(N)$ | Must traverse to find tail (unless tail pointer is maintained) |
| Delete by reference | $O(1)$ | Given the predecessor node |
| Delete by value | $O(N)$ | Must traverse to find it |
| Length | $O(N)$ | No `.length` property |

---

## 🗺️ The 5-Pattern Architecture (25 Unique Questions)

| Pattern | Questions | Core Idea |
|---------|-----------|-----------|
| **[Pattern 01: Basic Traversal & Mutation](./Pattern-01-Basic-Traversal-and-Mutation.md)** | 4 | Node structure, traversal, deletion, dummy-node technique |
| **[Pattern 02: Fast & Slow Pointers](./Pattern-02-Fast-and-Slow-Pointers.md)** | 5 | Pointer invariants: cycle detection, gap positioning, pointer alignment |
| **[Pattern 03: Reversal & Pointer Rewiring](./Pattern-03-Reversal-and-Pointer-Rewiring.md)** | 7 | Full → partial → pair → group → composite reversal |
| **[Pattern 04: Merge, Split, Partition & Sort](./Pattern-04-Merge-Split-Partition-and-Sort.md)** | 6 | Two-chain merge, K-merge, partitioning, merge sort |
| **[Pattern 05: Advanced Pointer Relationships](./Pattern-05-Advanced-Pointer-Relationships.md)** | 3 | Carry propagation, random-pointer registry, multilevel flattening |
| **Total** | **25** | |

---

## 🔍 Learning Progression

```text
Traversal & Mutation (Pattern 01)
  → Understand node structure, pointer movement, dummy-node discipline

Fast & Slow Pointers (Pattern 02)
  → Pointer invariants: speed differential, gap, equalization
  → Derive: cycle entry, kth-from-end, intersection

Reversal & Pointer Rewiring (Pattern 03)
  → Full → partial → pair → group → composite reversal
  → Recognize reversal-based structural rearrangement

Merge, Split, Partition & Sort (Pattern 04)
  → Two-chain merge primitive → K-chain scaling
  → Partition and split as two-chain operations
  → Merge sort: synthesis of middle-finding + merge

Advanced Pointer Relationships (Pattern 05)
  → Qualitatively new pointer types: carry, random, child
  → Registry-based copying, multidimensional flattening
```

---

## 🛠️ 20-Concept Coverage Checklist

| # | Concept | Covered By |
|---|---------|------------|
| 1 | Basic traversal / mutation | Pattern 01: Q1, Q2 |
| 2 | Dummy-node technique | Pattern 01: Q1, Q2; Pattern 02: Q3; Pattern 04: Q1 |
| 3 | Fast & slow pointers | Pattern 01: Q4 (intro), Pattern 02: Q1–Q4 |
| 4 | Cycle detection | Pattern 02: Q1 |
| 5 | Cycle entry point | Pattern 02: Q2 |
| 6 | Pointer gap / kth-from-end | Pattern 02: Q3 |
| 7 | Full reversal | Pattern 03: Q1 |
| 8 | Partial reversal | Pattern 03: Q2 |
| 9 | Reverse in groups | Pattern 03: Q4 |
| 10 | Pair swapping | Pattern 03: Q3 |
| 11 | Palindrome via middle + reversal | Pattern 02: Q4 |
| 12 | Reorder via split + reverse + merge | Pattern 03: Q5 |
| 13 | Merge sorted lists | Pattern 04: Q1 |
| 14 | Merge K sorted lists | Pattern 04: Q2 |
| 15 | Partitioning | Pattern 04: Q3, Q4 |
| 16 | Sorting a linked list | Pattern 04: Q5 |
| 17 | Intersection | Pattern 02: Q5 |
| 18 | Multiple pointer relationships | Pattern 05: Q1 |
| 19 | Random-pointer copying | Pattern 05: Q2 |
| 20 | Complex pointer rewiring | Pattern 05: Q3 |

All 20 concepts are covered. ✅

---

## ⚠️ Dominant-Pattern Classification

Each problem has one dominant home. Cross-pattern usage is noted in the pattern files.

| Problem | Dominant Pattern | Reason |
|---------|-----------------|--------|
| **Middle of Linked List** | Pattern 01 | Foundational traversal building block |
| **Palindrome Linked List** | Pattern 02 | Fast/slow provides the structural split decision |
| **Reorder List** | Pattern 03 | Dominant idea: reversal-based structural rearrangement |
| **Copy List with Random Pointer** | Pattern 05 | Primary challenge: pointer structure, not hashing |
| **Sort List** | Pattern 04 | Dominant idea: merge sort synthesis |

---

## 🔗 Cross-Pattern Pointer Technique Reference

```text
Dummy node:
  Pattern 01 (deletion) → Pattern 02 Q3 (remove Nth) → Pattern 04 (merge)

Fast/slow middle-finding:
  Pattern 01 Q4 (intro) → Pattern 02 (full development)
  → used in Pattern 02 Q4 (palindrome), Pattern 03 Q5 (reorder), Pattern 04 Q5 (sort)

Three-pointer reversal (prev/curr/next):
  Pattern 03 Q1 (full) → Q2 (partial) → Q3 (pair) → Q4 (group)
  → used in Pattern 02 Q4 (palindrome) and Pattern 03 Q5 (reorder)

Two-chain merge:
  Pattern 04 Q1 (primitive) → Q2 (K-chain) → Q5 (sort uses merge)

Partition into two chains:
  Pattern 04 Q3 (value-based) → Q4 (index-based) → Q6 (count-based splitting)

HashMap registry:
  Pattern 05 Q2 (random pointer copy) — same technique referenced in Hashing Module 03
```

---

## 📊 Difficulty Distribution (All 25 Questions)

| Difficulty | Count |
|------------|-------|
| **Easy** | 6 |
| **Medium** | 16 |
| **Hard** | 3 |
| **Total** | **25** |

---

## 🏆 Module Mastery Standard

This module is not optimized for passing average coding tests. It is optimized for:

> *"Can the learner recognize and derive solutions to unfamiliar SDE interview problems under time pressure?"*

You have mastered this module when you can:

- [ ] Draw and trace any linked list operation mentally with `prev`, `curr`, `next` labeled.
- [ ] Derive the dummy-node discipline without being reminded.
- [ ] State the pointer invariant for fast/slow before coding any problem that uses it.
- [ ] Implement full, partial, pair, and group reversal from a blank editor.
- [ ] Decompose Reorder List into its three phases without being told the steps.
- [ ] Scale the two-chain merge to K chains and explain the time complexity difference.
- [ ] Explain why merge sort is preferred over quick sort for linked lists.
- [ ] Implement Copy List with Random Pointer and explain why a single pass fails.
- [ ] Flatten a multilevel doubly linked list while updating all four pointer types correctly.
- [ ] Given an unfamiliar problem, identify which pointer invariant applies and derive the solution.
