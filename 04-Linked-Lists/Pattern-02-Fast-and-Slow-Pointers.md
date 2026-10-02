# Pattern 02: Fast & Slow Pointers

> Two pointers moving at different speeds through the same structure create precise spatial relationships that can be exploited without extra space.
> The skill is not memorizing five separate tricks — it is understanding the **pointer invariant** and deriving the correct setup from the problem.

---

## Why This Pattern Exists

Linked lists have no random access. You cannot ask "what is at position $n/2$?" or "is there a cycle?" by indexing. Fast/slow pointers solve this by creating controlled spatial relationships through differential traversal speed.

The fundamental insight:
> **If two pointers move at different speeds through the same structure, their relative positions reveal structural properties of the list — without extra space.**

Three core invariants this pattern exploits:

1. **Speed 2 vs Speed 1 → cycle detection:** If there is a cycle, the fast pointer must eventually lap the slow pointer.
2. **Speed 2 vs Speed 1 → middle finding:** When fast reaches end, slow is at middle.
3. **Gap of K → kth-from-end:** If fast leads by $K$ steps and both move at speed 1, when fast reaches null, slow is at the $K$-th node from the end.

---

## When to Use This Pattern

Use fast/slow pointers when:
- The problem involves cycle detection or finding where a cycle begins.
- You need the middle of a list in a single pass.
- You need to position a pointer $K$ nodes from the end in a single pass.
- You need to align two pointer chains of different lengths.
- The problem involves a structural property revealed by differential speed or gap.

**Recognition Triggers:**
```text
"detect if a cycle exists"               ──► Fast/slow at speed 2:1
"find where the cycle starts"            ──► Phase 1: detect meeting point, Phase 2: reset and advance together
"remove the N-th node from the end"      ──► Fast leads by N steps, slow lags
"is the list a palindrome?"              ──► Find middle, reverse second half, compare
"find the intersection of two lists"     ──► Equalize traversal lengths via pointer reassignment
```

---

## The Core Pointer Invariants

### Invariant 1: Cycle Detection (Floyd's Algorithm)
```text
fast moves 2 steps, slow moves 1 step

If cycle exists:
  fast must eventually catch slow (fast gains 1 step per iteration inside cycle)
  → they meet inside the cycle

If no cycle:
  fast reaches null first → no meeting → no cycle
```

### Invariant 2: Cycle Entry Point
```text
After meeting point M inside the cycle:

Let:
  F = distance from head to cycle entry
  C = cycle length
  K = distance from entry to meeting point

At meeting point: fast has traveled F + C + K, slow has traveled F + K
Since fast = 2 × slow: 2(F + K) = F + C + K → F = C - K

Implication:
  Reset one pointer to head, keep the other at M.
  Both move at speed 1.
  They meet exactly at the cycle entry point.
```

### Invariant 3: Kth-From-End (Pointer Gap)
```text
Move fast pointer K steps ahead.
Then move both fast and slow at speed 1.
When fast reaches null (after N total steps), slow has taken N-K steps.
→ slow is at node (N-K+1) from start = K-th from end.
```

### Invariant 4: Length Equalization (Intersection)
```text
List A has length a, List B has length b (a ≠ b).

When pointer pA reaches end of A, redirect it to head of B.
When pointer pB reaches end of B, redirect it to head of A.

Both pointers now travel (a + b) total nodes.
→ They meet at the intersection node (or at null if no intersection).
```

---

## ☕ Standard Java Templates

### Cycle Detection
```java
public boolean hasCycle(ListNode head) {
    ListNode slow = head, fast = head;
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
        if (slow == fast) return true;
    }
    return false;
}
```

### Cycle Entry Point
```java
public ListNode detectCycle(ListNode head) {
    ListNode slow = head, fast = head;
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
        if (slow == fast) {
            slow = head;                         // Reset one to head
            while (slow != fast) {               // Both at speed 1
                slow = slow.next;
                fast = fast.next;
            }
            return slow;                         // Cycle entry
        }
    }
    return null;
}
```

### Remove Nth from End
```java
public ListNode removeNthFromEnd(ListNode head, int n) {
    ListNode dummy = new ListNode(0);
    dummy.next = head;
    ListNode fast = dummy, slow = dummy;
    for (int i = 0; i <= n; i++) fast = fast.next; // fast leads by n+1
    while (fast != null) { slow = slow.next; fast = fast.next; }
    slow.next = slow.next.next; // delete
    return dummy.next;
}
```

---

## 🔍 Step-by-Step Dry Run

**Remove Nth Node From End (`LeetCode 19`)** — `[1→2→3→4→5]`, `n = 2`

After setup: `dummy → 1 → 2 → 3 → 4 → 5`

Lead fast by `n+1 = 3` steps from dummy:

| Phase | `fast` position | `slow` position |
|-------|-----------------|-----------------|
| Init | `dummy` | `dummy` |
| After 3 advances | `3` | `dummy` |
| Advance both until fast=null | moves 2 more | moves 2 more → `3` |

`slow` is at node `3`. `slow.next = 4`. Unlink: `slow.next = slow.next.next = 5`.

Result: `[1 → 2 → 3 → 5]`

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Reverse Linked List** | Pure pointer rewiring with no speed invariant — belongs to **Pattern 03**. |
| **Reorder List** | Uses fast/slow for middle-finding, but the dominant idea is reversal + merge composition — belongs to **Pattern 03**. |
| **Middle of Linked List** | Foundational traversal — introduced in **Pattern 01** as a building block. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 2 |
| **Medium** | 3 |
| **Total** | **5** |

---

## 🎯 Question Set (5 Questions)

### Q1. Linked List Cycle
<a href="https://leetcode.com/problems/linked-list-cycle/" target="_blank">LeetCode 141</a> — **Easy**

**Target Skill:** Floyd's cycle detection — using pointer speed differential to detect a structural loop.

**Core Reasoning:**
- If a cycle exists, `fast` inevitably catches `slow` inside the cycle (gains one node per iteration within the cycle).
- If no cycle, `fast` exits to null.
- **The invariant:** In a cyclic structure, a faster pointer cannot outrun a slower one indefinitely — it must lap it.

**Why it belongs here:** This is the canonical fast/slow problem. Every other problem in this pattern builds on or extends this invariant.

---

### Q2. Linked List Cycle II
<a href="https://leetcode.com/problems/linked-list-cycle-ii/" target="_blank">LeetCode 142</a> — **Medium**

**Target Skill:** Cycle entry detection — deriving the mathematical relationship between the meeting point and the cycle's start.

**Core Reasoning:**
- Phase 1: Use Floyd's algorithm to find any meeting point inside the cycle.
- Phase 2: Reset one pointer to head. Move both at speed 1. They meet at the cycle entry.
- **The derivation:** The distance from head to the entry equals the distance from the meeting point to the entry (through the cycle). This must be derived, not memorized.

**Why it belongs here:** This tests whether you understand the *mathematical invariant* behind Phase 2, not just the steps. A candidate who cannot derive "why reset to head?" has not internalized the pattern.

---

### Q3. Remove Nth Node From End of List
<a href="https://leetcode.com/problems/remove-nth-node-from-end-of-list/" target="_blank">LeetCode 19</a> — **Medium**

**Target Skill:** Pointer gap — creating a fixed-distance gap between two pointers to land at a precise position.

**Core Reasoning:**
- Lead `fast` by `n+1` steps from the dummy node.
- Move both at speed 1 until `fast` reaches null.
- `slow` is now at the node just before the target — enables deletion.
- **The invariant:** A fixed positional gap between two same-speed pointers is preserved throughout traversal.

**Why it belongs here:** Extends fast/slow reasoning to gap-based positioning. The dummy node is essential here to handle deletion of the head node uniformly.

**Cross-pattern note:** Uses the dummy-node technique from Pattern 01.

---

### Q4. Palindrome Linked List
<a href="https://leetcode.com/problems/palindrome-linked-list/" target="_blank">LeetCode 234</a> — **Easy**

**Target Skill:** Structural decomposition via pointer relationships — using multiple techniques in sequence to answer a property question.

**Core Reasoning:**
- Step 1: Find middle using fast/slow.
- Step 2: Reverse the second half.
- Step 3: Compare both halves node by node.
- **The goal:** Recognize that a palindrome check on a linked list requires splitting the problem into three sub-tasks, each drawn from a different technique.

**Why it belongs here:** The dominant pointer invariant is fast/slow (to find the split point). The reversal is a mechanical step using the result. This is where you practice reading a problem and identifying which technique produces the required structural state.

**Cross-pattern note:** Reversal technique comes from Pattern 03. Dominant pattern is Pattern 02 due to the fast/slow split being the structural decision point.

---

### Q5. Intersection of Two Linked Lists
<a href="https://leetcode.com/problems/intersection-of-two-linked-lists/" target="_blank">LeetCode 160</a> — **Easy**

**Target Skill:** Pointer alignment — equalizing cumulative traversal distance across two chains of different lengths.

**Core Reasoning:**
- `pA` traverses list A then list B. `pB` traverses list B then list A.
- Both travel a total of `len(A) + len(B)` nodes.
- If an intersection exists, they meet at it. If not, both reach null simultaneously.
- **The invariant:** Total path length equalization guarantees synchronized arrival at the intersection (or at null).

**Why it belongs here:** This is a pointer relationship problem where the insight is mathematical alignment, not speed differential. The technique generalizes: whenever two chains must be "synchronized" for comparison, equalize their total traversal length.

---

## 🏆 Mastery Criteria

You have mastered Pattern 02 when you can, **without being shown the technique name**:

- [ ] Explain why Floyd's algorithm guarantees a meeting point if a cycle exists.
- [ ] Derive (not recall) why resetting one pointer to head after meeting detects the cycle entry.
- [ ] Set up the gap-pointer invariant for kth-from-end positioning.
- [ ] Implement palindrome checking without being told the steps.
- [ ] Explain the total-path equalization behind intersection detection.
- [ ] Handle edge cases: empty list, single node, no cycle, no intersection, same-length lists.
- [ ] State $O(N)$ time and $O(1)$ space for all five problems.
- [ ] Identify when an unfamiliar problem requires one of these pointer invariants.

---

## ➡️ Next Pattern

Move to **[Pattern 03: Reversal & Pointer Rewiring](./Pattern-03-Reversal-and-Pointer-Rewiring.md)**.

The middle-finding technique you've practiced becomes a building block for more complex structural transformations — splitting, reversing, and reconnecting list segments.
