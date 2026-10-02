# Pattern 05: Queue & Deque Fundamentals

> The core property of a Queue is FIFO (First In, First Out). Queues model real-world arrival ordering, stream window eviction, turn-based round-robin processing, and circular buffer management.

---

## Why This Pattern Exists

While stacks process the *most recent* element (LIFO), queues process elements in exact *order of arrival* (FIFO).

Queues are essential when:
- **Order of Arrival Matters:** Elements must be processed in the precise order they were submitted.
- **State Expires Over Time:** Elements outside a moving time window must be evicted from the front.
- **Round-Robin Processing:** Processed elements re-enter the back of the line for subsequent rounds.
- **Fixed Memory Buffers:** Fixed-capacity array buffers re-use freed spaces using circular modulo indexing.

> **Scope Note:** This pattern focuses on fundamental FIFO queue state, stream filtering, and circular data structure mechanics. **BFS (Breadth-First Search)** algorithms are intentionally omitted here and will receive dedicated coverage in Trees and Graphs.

---

## Key Queue Variations

1. **Standard Queue (FIFO):** `offer()` adds to tail, `poll()` removes from head.
2. **Circular Queue:** Fixed-size array utilizing `head`, `tail`, and count variables with modulo indexing `(index + 1) % capacity`.
3. **Double-Ended Queue (Deque):** Supports $O(1)$ insertions and removals at both head and tail (`offerFirst`, `offerLast`, `pollFirst`, `pollLast`).

---

## ☕ Standard Java Templates

### 1. Circular Queue Array Implementation
```java
class MyCircularQueue {
    private final int[] data;
    private int head = 0;
    private int tail = -1;
    private int size = 0;
    private final int capacity;

    public MyCircularQueue(int k) {
        this.capacity = k;
        this.data = new int[k];
    }

    public boolean enQueue(int value) {
        if (isFull()) return false;
        tail = (tail + 1) % capacity;
        data[tail] = value;
        size++;
        return true;
    }

    public boolean deQueue() {
        if (isEmpty()) return false;
        head = (head + 1) % capacity;
        size--;
        return true;
    }
}
```

### 2. Expiring Window Queue
```java
class RecentCounter {
    private final Queue<Integer> queue = new ArrayDeque<>();

    public int ping(int t) {
        queue.offer(t);
        // Evict requests older than (t - 3000)
        while (!queue.isEmpty() && queue.peek() < t - 3000) {
            queue.poll();
        }
        return queue.size();
    }
}
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Sliding Window Maximum** | Requires monotonic ordering and eviction from both ends — belongs to **Monotonic Deque (Pattern 06)**. |
| **Implement Queue using Stacks** | Simulating FIFO behavior with two stacks — belongs to **Stack/Queue Design (Pattern 07)**. |
| **Binary Tree Level Order Traversal** | Structural BFS traversal — belongs to **Trees/Graphs**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 2 |
| **Medium** | 2 |
| **Total** | **4** |

---

## 🎯 Question Set (4 Questions)

### Q1. Design Circular Queue
<a href="https://leetcode.com/problems/design-circular-queue/" target="_blank">LeetCode 622</a> — **Medium**

**Target Skill:** Fixed-buffer circular pointer mechanics using modulo arithmetic.

**Core Reasoning:**
- A standard array-based queue requires shifting elements on dequeue $O(N)$ or grows indefinitely.
- Circular Queue re-uses vacant slots created by dequeue operations.
- Maintain `head`, `tail`, `capacity`, and `size`.
- `enQueue`: increment `tail = (tail + 1) % capacity`, set `data[tail] = value`.
- `deQueue`: increment `head = (head + 1) % capacity`.

**Why it belongs here:** Canonical structural foundation for FIFO ring buffers and memory-efficient queue state.

**Complexity:** All operations $O(1)$ Time, $O(K)$ Space.

---

### Q2. Number of Recent Calls
<a href="https://leetcode.com/problems/number-of-recent-calls/" target="_blank">LeetCode 933</a> — **Easy**

**Target Skill:** Expiring state management in sliding time windows.

**Core Reasoning:**
- Count calls in the time frame `[t - 3000, t]`. Timestamps are strictly increasing.
- Add `t` to the queue tail.
- While queue head element `< t - 3000`, poll the head (stale call expired from window).
- Return current queue size.

**Why it belongs here:** Demonstrates stream filtering where old entries naturally expire from the front of a FIFO queue.

**Complexity:** Time: Amortized $O(1)$ per ping, Space: $O(W)$ (where $W$ is max calls in 3000ms).

---

### Q3. Time Needed to Buy Tickets
<a href="https://leetcode.com/problems/time-needed-to-buy-tickets/" target="_blank">LeetCode 2073</a> — **Easy**

**Target Skill:** FIFO round-robin queue simulation vs direct mathematical calculation.

**Core Reasoning:**
- Person at index `k` wants `tickets[k]` tickets. Each ticket takes 1 second.
- **Simulation approach:** Queue of indices `[0, 1, ..., N-1]`. Poll front person, decrement ticket count, re-enqueue at tail if tickets remaining. Stop when person `k` reaches 0 tickets.
- **One-pass $O(N)$ math insight:** Person `i <= k` contributes `min(tickets[i], tickets[k])` seconds. Person `i > k` contributes `min(tickets[i], tickets[k] - 1)` seconds.

**Why it belongs here:** Provides an intuitive queue simulation baseline that leads directly to closed-form analytical optimization.

**Complexity:** Simulation: $O(\sum \text{tickets})$, Direct Math: $O(N)$ Time, $O(1)$ Space.

---

### Q4. Dota2 Senate
<a href="https://leetcode.com/problems/dota2-senate/" target="_blank">LeetCode 649</a> — **Medium**

**Target Skill:** Round-robin state simulation with greedy right elimination.

**Core Reasoning:**
- Two parties: Radiant ('R') and Dire ('D'). Each senator bans the next opposing senator who has a turn to vote.
- Use two queues storing turn indices for Radiant (`radiantQ`) and Dire (`direQ`).
- In each round, pop front indices $r$ and $d$:
  - If $r < d$: Radiant senator votes first and bans Dire senator $d$. Re-enqueue $r$ with index $r + N$ (next round turn).
  - If $d < r$: Dire senator votes first and bans Radiant senator $r$. Re-enqueue $d$ with index $d + N$.
- Repeat until one queue becomes empty.

**Why it belongs here:** Advanced FIFO simulation modeling multi-round turn systems where surviving candidates re-enter the back of the processing queue.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

## ⚡ Mastery Checklist

- [ ] Do you know how to write modulo arithmetic for array circular wrapping without overflow?
- [ ] Can you identify when elements in a stream naturally expire from the front of a FIFO queue?
- [ ] Can you simulate multi-round turn games cleanly using index offset re-queueing (`index + N`)?
- [ ] Do you recognize when a queue simulation can be simplified into a direct $O(N)$ pass?
