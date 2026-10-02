# Pattern 03: Reversal & Pointer Rewiring

> Linked list reversal is not a single trick — it is a family of pointer rewiring operations with increasing structural complexity.
> The skill is understanding what "reversing" means at the pointer level, and deriving the correct `prev → curr → next` sequence for each variant.

---

## Why This Pattern Exists

Array reversal uses index swaps. Linked list reversal requires explicitly redirecting pointers — the reverse of each `next` pointer — without losing access to the remaining chain.

Once you understand full reversal, every variant in this pattern is a scoped application of the same idea:
- Reverse a specific portion only.
- Reverse every pair.
- Reverse every group of $K$.
- Reverse the second half and merge.
- Rotate by rewiring the tail to the head.
- Reverse groups that satisfy a structural condition.

---

## When to Use This Pattern

Use reversal / pointer rewiring when:
- The problem explicitly asks to reverse all or part of a list.
- You need to rearrange list structure by reconnecting nodes rather than swapping values.
- A problem decomposes into: find a position → rewire pointers at that position.
- Rotation or reconnection can be reduced to a reversal or tail-to-head link.
- Palindrome, reorder, or structural symmetry requires a split + reverse + merge.

**Recognition Triggers:**
```text
"reverse the list"                           ──► Full reversal: three-pointer in-place
"reverse from position m to n"               ──► Partial reversal with saved connection points
"swap every two adjacent nodes"              ──► Pair rewiring
"reverse every k nodes"                      ──► Group reversal with saved head/tail
"reorder list: interleave first and second half"  ──► split + reverse second + interleave merge
"rotate right by k"                          ──► Find tail, reconnect as new head
"reverse even-length groups"                 ──► Group identification + conditional reversal
```

---

## The Core Reversal Mechanism

All reversals are built on one primitive:

```java
ListNode prev = null;
ListNode curr = head;
while (curr != null) {
    ListNode nextNode = curr.next; // save forward reference
    curr.next = prev;              // redirect pointer backward
    prev = curr;                   // advance prev
    curr = nextNode;               // advance curr
}
// prev is now the new head
```

> **The invariant:** At every step, `[head...prev]` is fully reversed. `[curr...end]` is untouched. `curr.next` must be saved before overwriting.

---

## ☕ Standard Java Templates

### 1. Full Reversal
```java
public ListNode reverseList(ListNode head) {
    ListNode prev = null, curr = head;
    while (curr != null) {
        ListNode next = curr.next;
        curr.next = prev;
        prev = curr;
        curr = next;
    }
    return prev;
}
```

### 2. Partial Reversal (Reverse Between m and n)
```java
public ListNode reverseBetween(ListNode head, int m, int n) {
    ListNode dummy = new ListNode(0);
    dummy.next = head;
    ListNode beforeM = dummy;
    for (int i = 1; i < m; i++) beforeM = beforeM.next;

    ListNode curr = beforeM.next;
    ListNode prev = null;
    for (int i = 0; i <= n - m; i++) {
        ListNode next = curr.next;
        curr.next = prev;
        prev = curr;
        curr = next;
    }
    beforeM.next.next = curr; // old m-node (now reversed tail) connects to node after n
    beforeM.next = prev;      // node before m connects to new head of reversed segment
    return dummy.next;
}
```

### 3. Group Reversal (Reverse K Nodes)
```java
public ListNode reverseKGroup(ListNode head, int k) {
    // Check if k nodes exist
    ListNode check = head;
    for (int i = 0; i < k; i++) {
        if (check == null) return head; // fewer than k nodes left — do not reverse
        check = check.next;
    }
    // Reverse k nodes
    ListNode prev = null, curr = head;
    for (int i = 0; i < k; i++) {
        ListNode next = curr.next;
        curr.next = prev;
        prev = curr;
        curr = next;
    }
    head.next = reverseKGroup(curr, k); // head is now the tail of reversed group
    return prev; // prev is the new head of this group
}
```

---

## 🔍 Step-by-Step Dry Run

**Reverse Linked List II (`LeetCode 92`)** — `[1→2→3→4→5]`, `m=2`, `n=4`

Step 1: Advance `beforeM` to node `1` (just before position 2).

Step 2: Reverse nodes at positions 2, 3, 4:

| Iteration | `prev` | `curr` | `curr.next` after rewire |
|-----------|--------|--------|--------------------------|
| 0 | `null` | `2` | `null` |
| 1 | `2` | `3` | `2` |
| 2 | `3` | `4` | `3` |
| 3 | `4` | `5` | — (loop ends) |

Step 3: Reconnect:
- `beforeM.next.next = curr` → node `2`'s next = node `5`
- `beforeM.next = prev` → node `1`'s next = node `4`

Result: `[1 → 4 → 3 → 2 → 5]`

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Palindrome Linked List** | Uses reversal as a sub-step, but dominant pointer reasoning is fast/slow for middle-finding — belongs to **Pattern 02**. |
| **Merge Two Sorted Lists** | Merge logic, not reversal — belongs to **Pattern 04**. |
| **Copy List with Random Pointer** | Multi-pointer node copying — belongs to **Pattern 05**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Medium** | 5 |
| **Hard** | 2 |
| **Total** | **7** |

---

## 🎯 Question Set (7 Questions)

### Q1. Reverse Linked List
<a href="https://leetcode.com/problems/reverse-linked-list/" target="_blank">LeetCode 206</a> — **Easy**

**Target Skill:** Full in-place reversal — the primitive operation for all reversal variants.

**Core Reasoning:**
- Three-pointer approach: `prev`, `curr`, `nextNode`.
- At each step: save `curr.next`, redirect `curr.next` to `prev`, advance both.
- **The invariant:** At every step, the segment `[head...prev]` is already reversed. The segment `[curr...end]` is untouched.

**Why it belongs here:** This is the foundational reversal. Every other problem in this pattern either scopes, extends, or combines this operation.

---

### Q2. Reverse Linked List II
<a href="https://leetcode.com/problems/reverse-linked-list-ii/" target="_blank">LeetCode 92</a> — **Medium**

**Target Skill:** Partial reversal — reversing a designated segment while preserving connections to the rest.

**Core Reasoning:**
- Advance to the node just before position $m$ (call it `beforeM`).
- Apply the full reversal mechanism for exactly $n - m + 1$ nodes.
- Reconnect: `beforeM.next.next = curr` (tail of reversed segment connects forward), `beforeM.next = prev` (node before connects to new head of segment).
- **The critical discipline:** Save the connection points before reversing — you cannot recover them after.

**Why it belongs here:** Scoped reversal is the most common reversal variant in interviews. Teaches that the same three-pointer mechanism works within a window, not just on the full list.

---

### Q3. Swap Nodes in Pairs
<a href="https://leetcode.com/problems/swap-nodes-in-pairs/" target="_blank">LeetCode 24</a> — **Medium**

**Target Skill:** Local pair rewiring — reversing adjacency at the granularity of two nodes without reversing the full list.

**Core Reasoning:**
- For each pair `(a, b)`: save `b.next`, rewire `b.next = a`, `a.next = next_pair_head`, `prev.next = b`.
- Advance `prev` to `a` (which is now the second of the pair), then to the next pair's `a`.
- **The insight:** Swapping adjacent nodes requires adjusting three connections: `prev→a→b→next` becomes `prev→b→a→next`.

**Why it belongs here:** Bridges single-node reversal to group-level rewiring. Builds the structural intuition needed for k-group reversal.

---

### Q4. Reverse Nodes in k-Group
<a href="https://leetcode.com/problems/reverse-nodes-in-k-group/" target="_blank">LeetCode 25</a> — **Hard**

**Target Skill:** Group reversal with conditional termination — apply full reversal to every group of $k$ nodes, preserve remainder.

**Core Reasoning:**
- Count whether $k$ nodes remain. If not, return head unchanged.
- Reverse exactly $k$ nodes using the standard mechanism.
- Recursively process the rest: `head.next = reverseKGroup(remaining, k)` (after reversal, `head` is the tail of the current group).
- **The key:** `head` before reversal is the tail after reversal. This enables clean recursive reconnection.

**Why it belongs here:** This is the natural escalation of pair swapping to arbitrary group size. The recursive structure mirrors the iterative pointer-advance pattern and teaches structural reasoning at a group level.

---

### Q5. Reorder List
<a href="https://leetcode.com/problems/reorder-list/" target="_blank">LeetCode 143</a> — **Medium**

**Target Skill:** Composite structural rearrangement — identify the three-phase decomposition from the problem statement alone.

**Core Reasoning:**
- The target structure `L0 → Ln → L1 → Ln-1 → ...` is equivalent to interleaving the first half with the reversed second half.
- Phase 1: Find the middle (fast/slow).
- Phase 2: Reverse the second half.
- Phase 3: Interleave merge the two halves.
- **The recognition challenge:** The problem does not say "find middle, reverse, merge." You must derive this decomposition.

**Why it belongs here:** This is the first composite reversal problem. Dominant idea: a reversal-based structural transformation. The fast/slow middle-finding from Pattern 02 is a sub-step.

**Cross-pattern note:** Middle-finding comes from Pattern 02. Reversal comes from Q1. This problem teaches you to combine techniques when no single technique suffices.

---

### Q6. Rotate List
<a href="https://leetcode.com/problems/rotate-list/" target="_blank">LeetCode 61</a> — **Medium**

**Target Skill:** Rotation via tail-to-head reconnection — recognizing that a rotation is equivalent to finding a new tail and re-linking.

**Core Reasoning:**
- Rotation by $k$ = making the $(N-k)$-th node the new tail and $(N-k+1)$-th the new head.
- Step 1: Find length $N$ and the current tail.
- Step 2: Form a ring: `tail.next = head`.
- Step 3: Advance to the new tail position: `N - (k % N)` steps from old head.
- Step 4: Set `newHead = newTail.next`, `newTail.next = null`.

**Why it belongs here:** Rotation is pointer rewiring that reuses the tail-connection idea. It is not an algorithmic sort or search — it is a structural relinking that requires recognizing where to cut and reconnect.

---

### Q7. Reverse Nodes in Even Length Groups
<a href="https://leetcode.com/problems/reverse-nodes-in-even-length-groups/" target="_blank">LeetCode 2074</a> — **Medium**

**Target Skill:** Transfer problem — apply conditional group reversal to a non-uniform group structure.

**Core Reasoning:**
- Groups have sizes $1, 2, 3, \ldots$ but the last group may be shorter.
- Reverse a group only if its actual length is even.
- Requires: group traversal to determine actual group size, saving the predecessor of each group, applying partial reversal within the group, reconnecting.
- **The recognition challenge:** No pointer invariant is given. You must recognize this as a conditional scoped reversal over non-uniform group sizes.

**Why it belongs here:** This is the transfer/recognition problem of the pattern. Earlier problems taught you the mechanisms. This tests whether you can apply them without explicit instruction — recognizing from the structure of the problem that conditional group-level reversal is required.

---

## 🏆 Mastery Criteria

You have mastered Pattern 03 when you can, **without being shown the technique name**:

- [ ] Perform full reversal from a blank editor: `prev`, `curr`, `nextNode` — explain the invariant at each step.
- [ ] Perform partial reversal between positions $m$ and $n$ — save connection points before reversing.
- [ ] Swap adjacent pairs by adjusting exactly three connections per pair.
- [ ] Reverse in groups of $k$ and correctly reconnect each group's tail.
- [ ] Decompose Reorder List into find-middle, reverse, interleave — without being told the steps.
- [ ] Reduce rotation to a ring-formation + re-cut operation.
- [ ] Apply conditional group reversal to non-uniform group sizes.
- [ ] Handle all edge cases: single-node list, $k$ larger than list length, even/odd length lists.
- [ ] State time $O(N)$ and space $O(1)$ (iterative) for all seven problems.

---

## ➡️ Next Pattern

Move to **[Pattern 04: Merge, Split, Partition & Sort](./Pattern-04-Merge-Split-Partition-and-Sort.md)**.

Many of the techniques you've used — pointer movement, dummy nodes, segment identification — now combine with a new operation: merging two ordered chains and partitioning a single chain based on a condition.
