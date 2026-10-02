# Pattern 01: Basic Traversal & Mutation

> Before pointer invariants, before reversals, before fast/slow — you must be fluent in moving through a linked list node by node, modifying it safely, and handling every edge case correctly.
> This pattern builds the foundational vocabulary that every later pattern depends on.

---

## Why This Pattern Exists

A linked list has no index-based access. Every operation requires explicit pointer movement. The most common early failure mode for candidates is not forgetting an algorithm — it is mishandling null references, moving past the target, or losing the only reference to a node.

This pattern addresses:
- How to traverse without losing a node reference.
- How to insert and delete without breaking the chain.
- How the dummy-node technique eliminates special-casing of head modifications.
- What "pointer movement" means concretely: `curr = curr.next`.

---

## When to Use This Pattern

Use basic traversal and mutation when:
- Building or testing a linked list structure.
- Removing elements by value across the entire list.
- Modifying a single node under structural constraints.
- Finding the middle node as a building block for more complex patterns.

---

## The Dummy-Node Technique

The most important foundational technique in linked list manipulation.

**Problem without it:** Deleting the head node requires special-casing. Inserting before the head requires special-casing.

**Solution:** Create a dummy node before the real head. All real nodes become non-head nodes. Every insertion and deletion is uniform.

```java
ListNode dummy = new ListNode(0);
dummy.next = head;
ListNode prev = dummy;

// traverse and mutate...

return dummy.next; // the new real head
```

> **Rule:** If a problem could delete or replace the head node, use a dummy node.

---

## ☕ Standard Java Templates

### Node Structure
```java
class ListNode {
    int val;
    ListNode next;
    ListNode(int val) { this.val = val; }
}
```

### Basic Traversal
```java
ListNode curr = head;
while (curr != null) {
    // process curr.val
    curr = curr.next;
}
```

### Deletion with Dummy Node
```java
ListNode dummy = new ListNode(0);
dummy.next = head;
ListNode prev = dummy;

while (prev.next != null) {
    if (prev.next.val == target) {
        prev.next = prev.next.next; // unlink the node
    } else {
        prev = prev.next;
    }
}
return dummy.next;
```

### Length Computation
```java
int length = 0;
ListNode curr = head;
while (curr != null) { length++; curr = curr.next; }
```

---

## 🔍 Step-by-Step Dry Run

**Remove Linked List Elements (`LeetCode 203`)** — `head = [1→2→6→3→4→5→6]`, `val = 6`

Initial: `dummy → 1 → 2 → 6 → 3 → 4 → 5 → 6`, `prev = dummy`

| Step | `prev.next.val` | Action | List state |
|------|-----------------|--------|------------|
| 1 | `1` | Move prev | `prev → 1` |
| 2 | `2` | Move prev | `prev → 2` |
| 3 | `6` | Unlink: `prev.next = prev.next.next` | `2 → 3` |
| 4 | `3` | Move prev | `prev → 3` |
| 5 | `4` | Move prev | `prev → 4` |
| 6 | `5` | Move prev | `prev → 5` |
| 7 | `6` | Unlink: `prev.next = null` | `5 → null` |

Result: `[1 → 2 → 3 → 4 → 5]`

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Linked List Cycle** | Fast/slow pointer reasoning — belongs to **Pattern 02**. |
| **Reverse Linked List** | Full pointer rewiring — belongs to **Pattern 03**. |
| **Merge Two Sorted Lists** | Merge reasoning across two chains — belongs to **Pattern 04**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 3 |
| **Medium** | 1 |
| **Total** | **4** |

---

## 🎯 Question Set (4 Questions)

### Q1. Design Linked List
<a href="https://leetcode.com/problems/design-linked-list/" target="_blank">LeetCode 707</a> — **Medium**

**Target Skill:** Full linked list mechanics — node structure, index tracking, insertion at head/tail/index, deletion.

**Core Reasoning:**
- Implement `get(index)`, `addAtHead(val)`, `addAtTail(val)`, `addAtIndex(index, val)`, `deleteAtIndex(index)`.
- Forces you to manage: node creation, traversal to a specific position, pointer rewiring for every case.
- **The dummy-node technique is critical here.** `addAtHead` and `deleteAtIndex(0)` become uniform with a dummy node.

**Why it belongs here:** This is the most comprehensive traversal/mutation exercise. Every technique in this pattern appears here: traversal, insertion, deletion, head manipulation, null handling, and length tracking.

---

### Q2. Remove Linked List Elements
<a href="https://leetcode.com/problems/remove-linked-list-elements/" target="_blank">LeetCode 203</a> — **Easy**

**Target Skill:** Deletion by value using the dummy-node technique.

**Core Reasoning:**
- Remove all nodes whose `val == target`.
- Without a dummy: deleting the head requires a separate condition.
- With a dummy: all deletions are handled uniformly with a `prev` pointer.
- **The question to internalize:** *"What does my `prev` pointer represent at each step, and how do I move it correctly?"*

**Why it belongs here:** This is the cleanest introduction to dummy-node deletion. Establishes the `prev → curr` two-pointer discipline that appears in every deletion problem.

---

### Q3. Delete Node in a Linked List
<a href="https://leetcode.com/problems/delete-node-in-a-linked-list/" target="_blank">LeetCode 237</a> — **Medium**

**Target Skill:** In-place mutation when you cannot traverse backward.

**Core Reasoning:**
- You are given direct access to the node to delete — but not to its predecessor.
- The trick: copy the next node's value into the current node, then skip the next node.
- `node.val = node.next.val; node.next = node.next.next;`
- **The structural insight:** Deletion does not require unlinking the given node — it requires making the given node's position "disappear" from the list's perspective.

**Why it belongs here:** Tests understanding that pointer manipulation is about structural meaning, not just mechanical steps. Breaks the assumption that deletion always requires a `prev` pointer.

---

### Q4. Middle of the Linked List
<a href="https://leetcode.com/problems/middle-of-the-linked-list/" target="_blank">LeetCode 876</a> — **Easy**

**Target Skill:** Two-speed traversal — fast pointer moves twice per step, slow moves once.

**Core Reasoning:**
- When `fast` reaches the end, `slow` is at the middle.
- For even-length lists, decide whether "middle" means first or second middle — understand what the stopping condition gives you.
- `while (fast != null && fast.next != null): slow = slow.next; fast = fast.next.next`

**Why it belongs here:** This is the simplest fast/slow pointer application and directly bridges into Pattern 02. Teaching it here establishes pointer-speed reasoning before cycle detection and kth-from-end.

**Cross-pattern note:** The middle-finding technique reappears as a building block in Palindrome Linked List (Pattern 02) and Reorder List (Pattern 03).

---

## 🏆 Mastery Criteria

You have mastered Pattern 01 when you can, **without being shown the approach**:

- [ ] Implement a linked list with insert, delete, and get from scratch.
- [ ] Apply the dummy-node technique and explain exactly why it simplifies head-deletion.
- [ ] Move a `prev` pointer correctly during deletion (advance only when no deletion occurred).
- [ ] Explain the "copy-next-and-skip" trick for node deletion without a predecessor reference.
- [ ] Find the middle of a list using fast/slow pointers and explain the stopping condition.
- [ ] Handle all edge cases: empty list, single node, target is head, target is tail.
- [ ] State $O(N)$ time and $O(1)$ space for all four problems.

---

## ➡️ Next Pattern

Move to **[Pattern 02: Fast & Slow Pointers](./Pattern-02-Fast-and-Slow-Pointers.md)**.

The middle-finding idea you learned here extends into cycle detection, kth-from-end reasoning, and pointer alignment — all built on the same invariant: two pointers moving at different speeds.
