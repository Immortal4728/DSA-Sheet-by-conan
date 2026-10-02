# Pattern 03: Two Heaps & Dynamic Ordered State

> Partitioning a dynamic stream of numbers into two halves using a **Max-Heap** (lower half) and a **Min-Heap** (upper half) maintains the dynamic median in $O(1)$ time while supporting $O(\log N)$ insertions.

---

## Why This Pattern Exists

Finding the median of a static array takes $O(N \log N)$ sorting time.
However, in a **data stream** where numbers arrive continuously, re-sorting after every insertion takes $O(N^2)$ time.

### The Two Heaps Invariant

Partition all numbers seen so far into two balanced heaps:
1. **`maxHeap` (Lower Half):** Stores the smaller half of numbers. Root is the **maximum of the lower half**.
2. **`minHeap` (Upper Half):** Stores the larger half of numbers. Root is the **minimum of the upper half**.

```text
                  [ STREAM OF NUMBERS ]
                /                        \
      `maxHeap` (Lower Half)     `minHeap` (Upper Half)
       Max element: 4             Min element: 6
       
       Invariant 1: maxHeap.peek() <= minHeap.peek()
       Invariant 2: maxHeap.size() == minHeap.size()  OR
                    maxHeap.size() == minHeap.size() + 1
```

### Median Calculation

- **Odd Total Count:** Median = `maxHeap.peek()`.
- **Even Total Count:** Median = `(maxHeap.peek() + minHeap.peek()) / 2.0`.

---

## ☕ Standard Java Templates

### Find Median from Data Stream (LC 295)
```java
class MedianFinder {
    private final PriorityQueue<Integer> maxHeap; // Lower half
    private final PriorityQueue<Integer> minHeap; // Upper half

    public MedianFinder() {
        maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        minHeap = new PriorityQueue<>();
    }
    
    public void addNum(int num) {
        // Step 1: Add to maxHeap (lower half)
        maxHeap.offer(num);
        
        // Step 2: Balance ordering invariant (max of lower <= min of upper)
        minHeap.offer(maxHeap.poll());
        
        // Step 3: Balance size invariant (maxHeap size >= minHeap size)
        if (maxHeap.size() < minHeap.size()) {
            maxHeap.offer(minHeap.poll());
        }
    }
    
    public double findMedian() {
        if (maxHeap.size() > minHeap.size()) {
            return maxHeap.peek();
        }
        return (maxHeap.peek() + (double) minHeap.peek()) / 2.0;
    }
}
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **IPO (LC 502)** | Uses one Max Heap for capital and sorting by cost — belongs to **Greedy & Heap (Pattern 06)**. |
| **Find Right Interval** | Uses binary search or single heap interval tracking — belongs to **Binary Search / Intervals**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Hard** | 2 |
| **Total** | **2** |

---

## 🎯 Question Set (2 Unique Questions)

### Q1. Find Median from Data Stream
<a href="https://leetcode.com/problems/find-median-from-data-stream/" target="_blank">LeetCode 295</a> — **Hard**

**Target Skill:** Dual heap partition invariant maintenance.

**Core Reasoning:**
- Keep `maxHeap` for lower half and `minHeap` for upper half.
- Push new number to `maxHeap`, transfer max to `minHeap`.
- If `maxHeap.size() < minHeap.size()`, transfer min back to `maxHeap`.
- Median is `maxHeap.peek()` (if odd total size) or average of peeks (if even).

**Why it belongs here:** Canonical foundation for dynamic median partitioning.

**Complexity:** Time: `addNum` $O(\log N)$, `findMedian` $O(1)$, Space: $O(N)$.

---

### Q2. Sliding Window Median
<a href="https://leetcode.com/problems/sliding-window-median/" target="_blank">LeetCode 480</a> — **Hard**

**Target Skill:** Two heaps with window element eviction / lazy deletion.

**Core Reasoning:**
- Maintain median over a sliding window of size $K$ across array `nums`.
- Maintain `maxHeap` and `minHeap` of window elements.
- When window slides right:
  1. Add incoming element `nums[i]`.
  2. Remove outgoing element `nums[i - k]` (using lazy deletion map or `remove()` / `TreeSet`).
  3. Re-balance heaps.
  4. Record median for window `i - k + 1`.

**Why it belongs here:** Extends Two-Heaps streaming median logic to sliding window range bounds.

**Complexity:** Time: $O(N \log K)$, Space: $O(K)$.

---

## ⚡ Mastery Checklist

- [ ] Can you state the two heap invariants required for dynamic median tracking?
- [ ] Why is `addNum` $O(\log N)$ while `findMedian` is $O(1)$?
- [ ] How do you prevent 32-bit integer overflow when averaging `maxHeap.peek()` and `minHeap.peek()`?
- [ ] How do you handle element eviction when applying Two Heaps to a sliding window?
