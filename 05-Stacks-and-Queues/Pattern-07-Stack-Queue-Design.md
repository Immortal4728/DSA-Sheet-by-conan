# Pattern 07: Stack/Queue Design

> Abstract data structure design requires building custom behavioral invariants (such as $O(1)$ minimum retrieval or cross-structural simulation) while maintaining strict operational time complexity constraints.

---

## Why This Pattern Exists

Interviewers use data structure design problems to test whether candidates understand structural invariants beyond simple usage.

Instead of calling built-in library functions, you are asked to:
- **Maintain Auxiliary State:** Track secondary properties (like minimum element) alongside main data streams in $O(1)$ time.
- **Simulate Structural Interfaces:** Recreate FIFO queue semantics using LIFO stacks, or LIFO stack semantics using FIFO queues.
- **Analyze Amortized Complexity:** Prove why operations that occasionally cost $O(N)$ achieve an average cost of $O(1)$ across $N$ operations.

---

## Key Design Principles

1. **Auxiliary Parallel State (Min Stack):**
   - Paired stack approach: push `[val, currentMin]` together or maintain a separate `minStack`.
   - On `pop()`, the auxiliary state automatically reverts to the previous minimum.

2. **Lazy Transfer / In-Out Stacks (Queue using Stacks):**
   - Use `inStack` for `push()` ($O(1)$).
   - Use `outStack` for `pop()` / `peek()`. Only transfer elements from `inStack` to `outStack` when `outStack` is completely empty.
   - Amortized complexity: Each element is moved between stacks exactly once $\to$ **Amortized $O(1)$**.

3. **Rotation / Re-Queueing (Stack using Queues):**
   - On `push(x)` to a single queue: offer `x`, then rotate the previous $N-1$ elements from front to back `queue.offer(queue.poll())`.
   - `top()` and `pop()` become direct $O(1)$ queue operations.

---

## ☕ Standard Java Templates

### Implement Queue using Stacks (Amortized $O(1)$)
```java
class MyQueue {
    private final Deque<Integer> inStack = new ArrayDeque<>();
    private final Deque<Integer> outStack = new ArrayDeque<>();

    public void push(int x) {
        inStack.push(x);
    }

    public int pop() {
        move();
        return outStack.pop();
    }

    public int peek() {
        move();
        return outStack.peek();
    }

    public boolean empty() {
        return inStack.isEmpty() && outStack.isEmpty();
    }

    private void move() {
        if (outStack.isEmpty()) {
            while (!inStack.isEmpty()) {
                outStack.push(inStack.pop());
            }
        }
    }
}
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Design Circular Queue** | Uses raw array circular pointers rather than structural stack simulation — belongs to **Queue Fundamentals (Pattern 05)**. |
| **Implement Stack using Queues (LC 225)** | Belongs **exclusively to Pattern 07**, NOT Pattern 01. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 2 |
| **Medium** | 1 |
| **Total** | **3** |

---

## 🎯 Question Set (3 Questions)

### Q1. Min Stack
<a href="https://leetcode.com/problems/min-stack/" target="_blank">LeetCode 155</a> — **Medium**

**Target Skill:** $O(1)$ auxiliary minimum state tracking.

**Core Reasoning:**
- Design a stack supporting `push`, `pop`, `top`, and `getMin` in $O(1)$ time.
- Maintain a main `stack` and an auxiliary `minStack`.
- `push(x)`: push `x` to `stack`. Push `min(x, minStack.peek())` to `minStack`.
- `pop()`: pop from both `stack` and `minStack`.
- `getMin()`: return `minStack.peek()`.

**Why it belongs here:** Canonical auxiliary state tracking design problem. Demonstrates how LIFO ordering guarantees historical minimum state can be restored on pop.

**Complexity:** All operations $O(1)$ Time, $O(N)$ Space.

---

### Q2. Implement Queue using Stacks
<a href="https://leetcode.com/problems/implement-queue-using-stacks/" target="_blank">LeetCode 232</a> — **Easy**

**Target Skill:** Reversing LIFO ordering via dual stacks to achieve amortized $O(1)$ FIFO behavior.

**Core Reasoning:**
- Implement FIFO queue using two stacks: `inStack` and `outStack`.
- `push(x)`: push directly to `inStack` ($O(1)$).
- `pop()` / `peek()`: if `outStack` is empty, pop all elements from `inStack` and push them into `outStack`. Then pop/peek from `outStack`.
- Pouring `inStack` into `outStack` reverses the LIFO order back into FIFO order.

**Why it belongs here:** Classic amortized analysis example. Even though a single transfer takes $O(N)$, each element is transferred at most once, giving amortized $O(1)$ per operation.

**Complexity:** Amortized $O(1)$ Time per operation, $O(N)$ Space.

---

### Q3. Implement Stack using Queues
<a href="https://leetcode.com/problems/implement-stack-using-queues/" target="_blank">LeetCode 225</a> — **Easy**

**Target Skill:** Queue rotation techniques to invert FIFO into LIFO behavior.

**Core Reasoning:**
- Implement LIFO stack using FIFO queue(s).
- **Single Queue Method:** When pushing `x`:
  - `queue.offer(x)`.
  - For `size - 1` times: `queue.offer(queue.poll())`.
  - This rotates all prior elements behind `x`, making `x` the new head of the queue.
- `pop()` and `top()` are simple $O(1)$ queue `poll()` and `peek()` operations.

**Why it belongs here:** **Canonical home for LC 225.** Demonstrates structural inversion via circular element rotation.

**Complexity:** `push`: $O(N)$ Time, `pop`/`top`: $O(1)$ Time, Space: $O(N)$.

---

## ⚡ Mastery Checklist

- [ ] Can you explain why `Min Stack` can retrieve the minimum in $O(1)$ without scanning?
- [ ] Can you prove why `Implement Queue using Stacks` has amortized $O(1)$ time complexity?
- [ ] Do you know how to implement `Implement Stack using Queues` using only a single Queue?
- [ ] Can you write clean, non-redundant helper methods (like `move()`) for dual-structure state transfers?
