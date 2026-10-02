# Pattern 07: Custom Priority Ordering

> Real-world priority queues rarely store simple integers. They manage complex objects requiring multi-attribute comparators, frequency-based priorities, or custom tie-breaking rules.

---

## Why This Pattern Exists

Java's `PriorityQueue<T>` defaults to natural ascending order for primitives and `Comparable` objects.

In interview problems, priority depends on custom domain rules:
- **Frequency First:** Character with highest frequency $\to$ Max Heap (`count` desc $\to$ `char` asc).
- **Multi-Attribute Ranking:** Row with fewest soldiers $\to$ Min Heap (`soldiers` asc $\to$ `rowIndex` asc).
- **Smallest Identifier:** Available seat with lowest number $\to$ Min Heap (`seatNumber` asc).

---

## ☕ Standard Java Comparator Guide

### 1. Multi-Field Tie-Breaking Comparator
```java
// Sort by soldier count ASCENDING; if equal, sort by row index ASCENDING
PriorityQueue<int[]> minHeap = new PriorityQueue<>((a, b) -> {
    if (a[0] != b[0]) {
        return Integer.compare(a[0], b[0]); // Primary: Soldier count
    }
    return Integer.compare(a[1], b[1]);     // Secondary: Row index
});
```

### 2. Frequency Pair Max-Heap (Reorganize String)
```java
// Store pair int[]{char, count} ordered by count DESCENDING
PriorityQueue<int[]> maxHeap = new PriorityQueue<>((a, b) -> Integer.compare(b[1], a[1]));
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Task Scheduler (LC 621)** | Housed canonically in **Scheduling (Pattern 05)** due to cooling interval simulation. |
| **K Closest Points to Origin** | Fixed-size distance heap — belongs to **Heap Fundamentals (Pattern 01)**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 1 |
| **Medium** | 2 |
| **Total** | **3** |

---

## 🎯 Question Set (3 Questions)

### Q1. Reorganize String
<a href="https://leetcode.com/problems/reorganize-string/" target="_blank">LeetCode 767</a> — **Medium**

**Target Skill:** Max Heap frequency depletion with alternating candidate holding.

**Core Reasoning:**
- Rearrange string so no two adjacent characters are identical.
- Build frequency map of characters.
- Push `int[]{char, count}` into Max Heap ordered by `count` descending.
- If max frequency $> (N + 1) / 2$, reorganization is impossible $\to$ return `""`.
- Loop: poll most frequent character `curr`, append to result, decrement count. Hold `curr` in `blocker` variable so it cannot be chosen immediately in the next step. Push `blocker` back into Max Heap after picking the next character.

**Why it belongs here:** Canonical custom priority problem enforcing non-adjacent placement via Max Heap.

**Complexity:** Time: $O(N \log \Sigma)$ (where $\Sigma \le 26$), Space: $O(\Sigma)$.

---

### Q2. The K Weakest Rows in a Matrix
<a href="https://leetcode.com/problems/the-k-weakest-rows-in-a-matrix/" target="_blank">LeetCode 1337</a> — **Easy**

**Target Skill:** Multi-attribute Min/Max Heap comparator (`soldierCount` then `rowIndex`).

**Core Reasoning:**
- Count soldiers (`1`s) in each row using Binary Search ($O(\log C)$ per row).
- Maintain a **Max Heap** of capacity $K$ storing `int[]{soldierCount, rowIndex}`.
- Comparator: `(a, b) -> a[0] != b[0] ? b[0] - a[0] : b[1] - a[1]`.
- Push rows into Max Heap; poll if size $> K$.
- Extract $K$ elements from Max Heap in reverse into result array.

**Why it belongs here:** Demonstrates multi-attribute tie-breaking comparators on 2D data structures.

**Complexity:** Time: $O(R \log C + R \log K)$, Space: $O(K)$.

---

### Q3. Seat Reservation Manager
<a href="https://leetcode.com/problems/seat-reservation-manager/" target="_blank">LeetCode 1845</a> — **Medium**

**Target Skill:** Dynamic pool management with automatic minimum index selection.

**Core Reasoning:**
- System manages seats $1 \dots N$.
- `reserve()` returns the unreserved seat with the **smallest number**.
- `unreserve(seatNumber)` frees up a previously reserved seat.
- Maintain `PriorityQueue<Integer> minHeap` initialized with available seat numbers $1 \dots N$ (or track `marker` integer to defer heap pushes).
- `reserve()` $\to$ `minHeap.poll()`.
- `unreserve(seatNumber)` $\to$ `minHeap.offer(seatNumber)`.

**Why it belongs here:** Clean design problem showcasing custom priority seat allocation.

**Complexity:** All operations $O(\log N)$ Time, Space $O(N)$.

---

## ⚡ Mastery Checklist

- [ ] How do you write a Java `Comparator` that sorts by primary field ASCENDING and secondary field DESCENDING?
- [ ] Why does `Reorganize String` use a `blocker` variable to prevent adjacent character collision?
- [ ] How do you prevent integer overflow when writing custom subtraction comparators `(a, b) -> a - b`? (Use `Integer.compare(a, b)` instead!).
