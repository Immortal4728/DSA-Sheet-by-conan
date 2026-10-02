# Pattern 04: Merge, Split, Partition & Sort

> Rearrange linked list structure by operating on multiple chains simultaneously — merging ordered chains, splitting into independent chains, partitioning around a pivot, and sorting using pointer-based divide-and-conquer.
> The core skill: manipulating pointers across multiple independent chain heads simultaneously.

---

## Why This Pattern Exists

Every previous pattern operated within a single chain. This pattern introduces multi-chain coordination:
- **Merge** combines two or more chains into one, respecting an ordering constraint.
- **Split** divides one chain into multiple independent chains.
- **Partition** rearranges a single chain into segments based on a condition, then reconnects.
- **Sort** combines splitting and merging recursively to produce a sorted chain.

The foundational primitive for merge is: at each step, choose the smaller of two current heads, link it forward, and advance that pointer.

---

## When to Use This Pattern

Use this pattern when:
- Two sorted lists must be merged into one sorted list.
- K sorted lists must be merged efficiently.
- A list must be rearranged so elements satisfying a condition come before those that do not.
- A list must be split into independent sublists by position or index.
- Sorting a linked list is required (merge sort is the natural choice — $O(N \log N)$ without random access).

**Recognition Triggers:**
```text
"merge two sorted linked lists"               ──► Two-pointer merge with dummy head
"merge k sorted linked lists"                 ──► Heap (priority queue) or divide-and-conquer
"partition list around value x"               ──► Two chains (< x and ≥ x), reconnect at end
"separate odd-indexed and even-indexed nodes" ──► Two chains (odd and even), reconnect
"sort a linked list"                          ──► Merge sort: find middle, split, sort halves, merge
"split into k nearly-equal parts"             ──► Length ÷ k with remainder distribution
```

---

## The Core Merge Primitive

All merge operations build on this:

```java
ListNode dummy = new ListNode(0);
ListNode curr = dummy;

while (l1 != null && l2 != null) {
    if (l1.val <= l2.val) { curr.next = l1; l1 = l1.next; }
    else                   { curr.next = l2; l2 = l2.next; }
    curr = curr.next;
}
curr.next = (l1 != null) ? l1 : l2; // attach the remaining chain
return dummy.next;
```

> **Invariant:** At every step, `dummy.next` through `curr` is the correctly merged prefix. `l1` and `l2` are the remaining unprocessed portions of each chain.

---

## ☕ Standard Java Templates

### 1. Two-Chain Merge
```java
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
    ListNode dummy = new ListNode(0), curr = dummy;
    while (l1 != null && l2 != null) {
        if (l1.val <= l2.val) { curr.next = l1; l1 = l1.next; }
        else                   { curr.next = l2; l2 = l2.next; }
        curr = curr.next;
    }
    curr.next = (l1 != null) ? l1 : l2;
    return dummy.next;
}
```

### 2. Partition by Value (Two Separate Chains)
```java
public ListNode partition(ListNode head, int x) {
    ListNode lessHead = new ListNode(0), greaterHead = new ListNode(0);
    ListNode less = lessHead, greater = greaterHead;
    while (head != null) {
        if (head.val < x) { less.next = head; less = less.next; }
        else               { greater.next = head; greater = greater.next; }
        head = head.next;
    }
    greater.next = null;    // IMPORTANT: sever the tail
    less.next = greaterHead.next;
    return lessHead.next;
}
```

### 3. Merge Sort on a Linked List
```java
public ListNode sortList(ListNode head) {
    if (head == null || head.next == null) return head;
    // Find middle and split
    ListNode mid = getMid(head);
    ListNode right = mid.next;
    mid.next = null; // split the list
    return mergeTwoLists(sortList(head), sortList(right));
}

private ListNode getMid(ListNode head) {
    ListNode slow = head, fast = head.next; // fast starts at head.next → slow lands at first middle
    while (fast != null && fast.next != null) {
        slow = slow.next; fast = fast.next.next;
    }
    return slow;
}
```

---

## 🔍 Step-by-Step Dry Run

**Merge Two Sorted Lists (`LeetCode 21`)** — `l1 = [1→2→4]`, `l2 = [1→3→4]`

Initial: `dummy → [?]`, `curr = dummy`

| Step | `l1.val` | `l2.val` | Chosen | `curr` advances to |
|------|----------|----------|--------|--------------------|
| 1 | 1 | 1 | `l1` (≤) | node `1` from l1 |
| 2 | 2 | 1 | `l2` (<) | node `1` from l2 |
| 3 | 2 | 3 | `l1` (≤) | node `2` |
| 4 | 4 | 3 | `l2` (<) | node `3` |
| 5 | 4 | 4 | `l1` (≤) | node `4` from l1 |
| 6 | null | 4 | Attach remaining `l2` | node `4` from l2 |

Result: `[1 → 1 → 2 → 3 → 4 → 4]`

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Reorder List** | Dominant idea is reversal + interleave merge — belongs to **Pattern 03**. |
| **Reverse Nodes in k-Group** | Group reversal, not merge/partition — belongs to **Pattern 03**. |
| **Add Two Numbers** | Multi-chain relationship with carry propagation — belongs to **Pattern 05**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 1 |
| **Medium** | 4 |
| **Hard** | 1 |
| **Total** | **6** |

---

## 🎯 Question Set (6 Questions)

### Q1. Merge Two Sorted Lists
<a href="https://leetcode.com/problems/merge-two-sorted-lists/" target="_blank">LeetCode 21</a> — **Easy**

**Target Skill:** Two-chain merge — the foundational merge primitive.

**Core Reasoning:**
- Maintain two pointers, one per list. At each step, choose the smaller head and advance that pointer.
- Use a dummy node to avoid special-casing the result head.
- **The invariant:** The merged prefix is always correctly ordered. The remaining portions are untouched.

**Why it belongs here:** This is the merge primitive. Every other merge problem in this pattern builds directly on this operation.

---

### Q2. Merge k Sorted Lists
<a href="https://leetcode.com/problems/merge-k-sorted-lists/" target="_blank">LeetCode 23</a> — **Hard**

**Target Skill:** Scaling the merge concept — efficiently coordinating $K$ chains instead of two.

**Core Reasoning:**
- Naïve approach: merge lists one-by-one → $O(N \cdot K)$ time.
- Optimized approach 1 — **Min-Heap (Priority Queue):** Insert the head of each list. Extract the minimum, add it to the result, insert that node's `.next`. Time: $O(N \log K)$.
- Optimized approach 2 — **Divide and Conquer:** Merge pairs of lists, reducing $K$ lists to $K/2$, then $K/4$, etc. Total time: $O(N \log K)$.
- **The escalation:** The merge primitive from Q1 remains identical. The challenge is how to select the minimum across $K$ sources efficiently.

**Why it belongs here:** This is the direct escalation of two-chain merging. A candidate who understands Q1 must now ask: *"How do I scale this to K chains without doing K-1 sequential merges?"* That question drives the heap or divide-and-conquer insight.

---

### Q3. Partition List
<a href="https://leetcode.com/problems/partition-list/" target="_blank">LeetCode 86</a> — **Medium**

**Target Skill:** Two-chain partition — separate nodes satisfying a condition from those that do not, then reconnect.

**Core Reasoning:**
- Maintain two chains: one for nodes with `val < x`, one for `val ≥ x`.
- Each chain has its own dummy head. Traverse the original list, appending each node to the appropriate chain.
- After traversal: terminate the `≥ x` chain with null, connect the tail of `< x` to the head of `≥ x`.
- **Critical edge case:** The tail of the `≥ x` chain must be explicitly nulled — it may still point to a node in the original list.

**Why it belongs here:** Partition is a structural rearrangement using two independent chains — a natural extension of the two-chain merge idea applied to a single input.

---

### Q4. Odd Even Linked List
<a href="https://leetcode.com/problems/odd-even-linked-list/" target="_blank">LeetCode 328</a> — **Medium**

**Target Skill:** Index-based two-chain partition — separate by position (odd/even index), then reconnect.

**Core Reasoning:**
- Maintain two chains: odd-indexed nodes and even-indexed nodes.
- Advance: `odd.next = even.next`, then `even.next = odd.next` (if it exists), alternating.
- After traversal: `odd.next = evenHead`.
- **The structural difference from Q3:** Partition here is positional (node index), not value-based. The two-chain approach is identical.

**Why it belongs here:** Teaches that the two-chain partition approach generalizes to any binary classification — value-based (Q3) or position-based (Q4).

---

### Q5. Sort List
<a href="https://leetcode.com/problems/sort-list/" target="_blank">LeetCode 148</a> — **Medium**

**Target Skill:** Linked list merge sort — understanding why merge sort is the natural sort for linked lists.

**Core Reasoning:**
- Arrays can use quick sort ($O(N \log N)$, $O(1)$ space) because they support random access. Linked lists cannot — pivoting requires traversal.
- Merge sort needs only: find middle (fast/slow), split at middle, sort halves recursively, merge the sorted halves.
- All three operations are $O(N)$ in a linked list.
- **The connection:** This problem synthesizes finding-middle (Pattern 02), merge (Q1), and splitting.

**Why it belongs here:** Sort List is the culminating synthesis of this pattern — it directly uses the merge primitive (Q1) and demonstrates why algorithm choice depends on data structure properties.

**Cross-pattern note:** Middle-finding uses fast/slow from Pattern 02.

---

### Q6. Split Linked List in Parts
<a href="https://leetcode.com/problems/split-linked-list-in-parts/" target="_blank">LeetCode 725</a> — **Medium**

**Target Skill:** Structural splitting — distributing nodes across $K$ independent sublists with remainder handling.

**Core Reasoning:**
- Compute list length $N$. Each part has base size `N / k`. The first `N % k` parts get one extra node.
- Traverse the list, cutting after each part's allocated length and collecting the sublist head.
- **The precision required:** You must cut at exactly the right node — `curr.next = null` after counting — without losing the reference to the next part.

**Why it belongs here:** This is the split complement to merge. Teaches precise position-based chain cutting under a remainder distribution constraint.

---

## 🏆 Mastery Criteria

You have mastered Pattern 04 when you can, **without being shown the approach**:

- [ ] Implement two-chain merge with a dummy head from a blank editor.
- [ ] Explain why sequential K-merge is $O(NK)$ and why heap/divide-and-conquer achieves $O(N \log K)$.
- [ ] Implement partition using two separate chains and explain the null-termination requirement.
- [ ] Apply the same two-chain approach to both value-based and position-based partitioning.
- [ ] Explain why merge sort is preferred over quick sort for linked lists.
- [ ] Implement linked list merge sort combining fast/slow middle-finding with two-chain merge.
- [ ] Split a list into $K$ parts with correct remainder distribution.
- [ ] Handle edge cases: empty list, $K > N$, single-node list, equal-length lists.
- [ ] State time and space complexity for all six problems.

---

## ➡️ Next Pattern

Move to **[Pattern 05: Advanced Pointer Relationships](./Pattern-05-Advanced-Pointer-Relationships.md)**.

These final problems go beyond single-list operations — each node may carry multiple pointers, lists may interact at the value level rather than the structural level, or the structure itself may be multidimensional.
