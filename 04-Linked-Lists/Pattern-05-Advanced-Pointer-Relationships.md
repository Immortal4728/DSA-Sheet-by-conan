# Pattern 05: Advanced Pointer Relationships

> Each node in a linked list carries a `next` pointer. These problems challenge that assumption — nodes may carry random pointers, carry arithmetic state that propagates forward, or exist inside a multilevel structure where `next`, `prev`, and `child` pointers all require simultaneous management.
> The skill here is recognizing what "pointer relationship" the problem creates and systematically maintaining all pointer types in every operation.

---

## Why This Pattern Exists

All previous patterns assumed a simple `next`-pointer chain. This pattern exposes three qualitatively different pointer structures:

1. **Carry propagation across nodes:** Two parallel chains interact at the value level, not the structural level. Arithmetic state flows forward through `next` pointers.
2. **Random-pointer copying:** Each node has a `random` pointer that can reference any node — creating a non-sequential graph on top of the sequential chain. Copying requires an $O(1)$-lookup registry.
3. **Multilevel doubly linked structure:** Nodes have both `next`/`prev` and a `child` pointer to an independent sublist. Flattening requires nested traversal and careful reconnection of all pointer types.

These three problems are not variations of each other — they test three distinct pointer reasoning skills.

---

## When to Use This Pattern

Use this pattern when:
- A problem involves nodes with more than one forward pointer.
- Arithmetic state (carry) must be computed and propagated from one node to the next.
- You must clone a structure with cross-references that cannot be resolved in a single forward pass.
- A list has a hierarchical or nested dimension that must be linearized.

**Recognition Triggers:**
```text
"add two numbers represented as linked lists"     ──► Digit-by-digit addition with carry forwarding
"clone a list with random pointers"               ──► Node registry (HashMap: old → new) for O(1) cross-ref
"flatten a multilevel doubly linked list"         ──► DFS/iterative child-first traversal + pointer reconnection
```

---

## Why Only Three Problems

Three problems are sufficient because the goal of this pattern is **qualitative exposure**, not quantity. Each problem represents a structurally distinct pointer relationship:

| Problem | New Pointer Structure |
|---------|-----------------------|
| Add Two Numbers | Value-level state propagation across a sequential chain |
| Copy List with Random Pointer | Non-sequential cross-reference (`random`) in a parallel copy |
| Flatten a Multilevel Doubly Linked List | Hierarchical `child` pointer in a bidirectional chain |

Adding more problems that use the same structure would reduce the transfer value. The goal is: recognize the structural type → derive the pointer management strategy.

---

## ☕ Standard Java Templates

### 1. Digit Addition with Carry
```java
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
    ListNode dummy = new ListNode(0), curr = dummy;
    int carry = 0;
    while (l1 != null || l2 != null || carry != 0) {
        int sum = carry;
        if (l1 != null) { sum += l1.val; l1 = l1.next; }
        if (l2 != null) { sum += l2.val; l2 = l2.next; }
        carry = sum / 10;
        curr.next = new ListNode(sum % 10);
        curr = curr.next;
    }
    return dummy.next;
}
```

### 2. Clone with Random Pointer (HashMap Registry)
```java
public Node copyRandomList(Node head) {
    if (head == null) return null;
    Map<Node, Node> map = new HashMap<>(); // old node → new node

    // Pass 1: Create all new nodes
    Node curr = head;
    while (curr != null) {
        map.put(curr, new Node(curr.val));
        curr = curr.next;
    }
    // Pass 2: Wire all pointers using registry
    curr = head;
    while (curr != null) {
        map.get(curr).next   = map.get(curr.next);
        map.get(curr).random = map.get(curr.random);
        curr = curr.next;
    }
    return map.get(head);
}
```

### 3. Flatten Multilevel Doubly Linked List
```java
public Node flatten(Node head) {
    Node curr = head;
    while (curr != null) {
        if (curr.child != null) {
            Node child = curr.child;
            Node next = curr.next;
            // Connect curr to child
            curr.next = child;
            child.prev = curr;
            curr.child = null;
            // Find the tail of the child sublist
            Node childTail = child;
            while (childTail.next != null) childTail = childTail.next;
            // Reconnect child tail to next
            childTail.next = next;
            if (next != null) next.prev = childTail;
        }
        curr = curr.next;
    }
    return head;
}
```

---

## 🔍 Step-by-Step Dry Run

**Add Two Numbers (`LeetCode 2`)** — `l1 = [2→4→3]` (342), `l2 = [5→6→4]` (465)

Target: 342 + 465 = 807 → `[7→0→8]`

| Step | `l1.val` | `l2.val` | `carry` in | `sum` | `digit` | `carry` out |
|------|----------|----------|------------|-------|---------|-------------|
| 1 | 2 | 5 | 0 | 7 | **7** | 0 |
| 2 | 4 | 6 | 0 | 10 | **0** | 1 |
| 3 | 3 | 4 | 1 | 8 | **8** | 0 |
| 4 | null | null | 0 | — | loop ends | — |

Result: `[7 → 0 → 8]` ✓

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **LRU Cache** | Doubly linked list + HashMap as a cache eviction design problem — belongs to **System Design / Cache Design**. |
| **Reverse a Doubly Linked List** | Pure pointer reversal on a doubly linked structure — belongs to **Pattern 03** conceptually, but is rarely asked as a standalone interview problem. |
| **Copy List with Random Pointer (duplicate)** | Already included here — its HashMap technique was also referenced in **Hashing Module 03 Pattern 05**. Dominant home: this pattern (the pointer structure is the primary challenge). |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Medium** | 3 |
| **Total** | **3** |

---

## 🎯 Question Set (3 Questions)

### Q1. Add Two Numbers
<a href="https://leetcode.com/problems/add-two-numbers/" target="_blank">LeetCode 2</a> — **Medium**

**Target Skill:** Value-level state propagation — traversing two parallel chains and propagating arithmetic state (carry) forward through node creation.

**Core Reasoning:**
- Traverse `l1` and `l2` simultaneously, summing digits with carry at each position.
- Create a new node for each digit result. Forward the carry.
- Continue the loop while either chain has nodes remaining **or** carry is non-zero (the carry may generate an extra node beyond both chains).
- **The structural insight:** The linked lists represent numbers in reverse order — least significant digit first. This is the natural traversal order for addition.

**Why it belongs here:** This is the only problem in the entire module where two chains interact at the *value* level rather than the *structural* level. The pointer relationships are simple, but state management (carry) must be propagated through each `.next` step. This is a qualitatively different reasoning type from all previous patterns.

**Cross-pattern note:** Uses the dummy-head technique from Pattern 01.

---

### Q2. Copy List with Random Pointer
<a href="https://leetcode.com/problems/copy-list-with-random-pointer/" target="_blank">LeetCode 138</a> — **Medium**

**Target Skill:** Non-sequential cross-reference copying — building a registry to resolve arbitrary pointer relationships in a second pass.

**Core Reasoning:**
- Each node has `next` (sequential) and `random` (arbitrary — can point to any node, including null).
- The challenge: when copying a node, its `random` may point to a node not yet created. You cannot set `random` in a single forward pass.
- **Two-pass approach:**
  - Pass 1: Create all new nodes, store `oldNode → newNode` in a HashMap.
  - Pass 2: For each old node, set `newNode.next = map[oldNode.next]` and `newNode.random = map[oldNode.random]`.
- **The invariant:** The HashMap provides $O(1)$ resolution of any cross-reference after Pass 1.

**Why it belongs here:** The `random` pointer creates a graph structure overlaid on the sequential chain. Copying it requires recognizing that forward-pass creation cannot resolve backward or arbitrary references — you need a complete registry first. This is a fundamentally different pointer reasoning challenge from any other problem in this module.

**Cross-pattern note:** The HashMap registry technique is also referenced in Hashing Module 03 Pattern 05, but the primary challenge here is the pointer structure, not the hashing technique.

---

### Q3. Flatten a Multilevel Doubly Linked List
<a href="https://leetcode.com/problems/flatten-a-multilevel-doubly-linked-list/" target="_blank">LeetCode 430</a> — **Medium**

**Target Skill:** Complex pointer rewiring in a multidimensional structure — flattening nested chains while maintaining bidirectional pointer consistency.

**Core Reasoning:**
- Each node may have a `child` pointer to an independent doubly linked sublist (which may itself have children).
- Flattening: when a `child` is encountered, insert the entire child sublist between `curr` and `curr.next`, then continue traversal.
- Four pointer adjustments per encountered child:
  1. `curr.next = child`
  2. `child.prev = curr`
  3. `curr.child = null` (sever the child link)
  4. Find the child sublist's tail. `childTail.next = savedNext` and `savedNext.prev = childTail`.
- **The complexity:** `prev` pointers must remain correct throughout. Missing any one adjustment produces a corrupted structure.

**Why it belongs here:** This problem requires simultaneously managing three pointer types (`next`, `prev`, `child`) across a nested structure. The structural reasoning challenge — identifying which nodes exist, where the child sublist ends, and how to reconnect all pointer types — represents the highest pointer-relationship complexity in this module.

---

## 🏆 Mastery Criteria

You have mastered Pattern 05 when you can, **without being shown the approach**:

- [ ] Implement digit-by-digit addition on two parallel chains with correct carry propagation.
- [ ] Handle the "carry remaining after both chains end" edge case.
- [ ] Explain why a single-pass copy of a list with random pointers fails, and why a two-pass registry approach works.
- [ ] Implement Copy List with Random Pointer using a HashMap registry without confusion between old and new nodes.
- [ ] Identify the four pointer adjustments required when flattening at a `child` node.
- [ ] Traverse a multilevel list iteratively while correctly updating `next`, `prev`, and `child` pointers.
- [ ] Handle all edge cases: `l1` and `l2` of different lengths, `random = null`, nodes with no children, deepest nesting level.
- [ ] State $O(N)$ time and $O(N)$ space (Copy List) vs $O(1)$ space (Add Two Numbers, Flatten iterative) correctly.

---

## ➡️ Module Complete

You have completed the Linked Lists module.

Return to **[Module 04 README](./README.md)** for the full curriculum overview, concept coverage checklist, and cross-pattern relationships.
