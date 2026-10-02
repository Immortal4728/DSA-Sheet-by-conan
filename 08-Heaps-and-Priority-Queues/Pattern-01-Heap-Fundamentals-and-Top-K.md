# Pattern 01: Heap Fundamentals & Top-K

> A Heap (Priority Queue) is a complete binary tree that satisfies the heap property: in a Min Heap, parent $\le$ children; in a Max Heap, parent $\ge$ children. It provides $O(1)$ access to the extreme element and $O(\log N)$ insertions and deletions.

---

## Why This Pattern Exists

When finding the $K$ largest (or smallest) elements in a dataset of size $N$:
- **Full Sorting:** Takes $O(N \log N)$ time and $O(N)$ space.
- **Fixed-Size Min Heap of Capacity $K$:** Takes **$O(N \log K)$ time** and **$O(K)$ space**.

### The Fixed-Size Min Heap Invariant

To keep the **Top-$K$ Largest** elements:
1. Use a **Min Heap** of size $K$.
2. Push incoming elements into the heap.
3. If `heap.size() > K`, pop the smallest element (`heap.poll()`).

> **Core Insight:** The Min Heap eviction policy continuously discards smaller elements, leaving only the $K$ largest elements. The root of the Min Heap `heap.peek()` **always** holds the $K$-th largest element seen so far!

```text
Incoming Elements: [3, 2, 1, 5, 6, 4], K = 3

Min Heap (Capacity 3):
[3, 5, 6]  <-- Root (3) is the 3rd largest element!
```

---

## ☕ Standard Java Templates

### Fixed-Size Min Heap for Top-K Largest
```java
// Keeps top K largest elements; root is K-th largest
PriorityQueue<Integer> minHeap = new PriorityQueue<>(k);

for (int num : nums) {
    minHeap.offer(num);
    if (minHeap.size() > k) {
        minHeap.poll(); // Evict smallest among top candidates
    }
}
return minHeap.peek();
```

---

## 🔗 Cross-Pattern & Cross-Section References

Do NOT physically duplicate these questions here; their canonical homes are:
- **Top K Frequent Elements** (<a href="https://leetcode.com/problems/top-k-frequent-elements/" target="_blank">LeetCode 347</a>) $\longrightarrow$ Housed in **Section 03 — Hashing** (frequency map + heap).
- **Sort Characters By Frequency** (<a href="https://leetcode.com/problems/sort-characters-by-frequency/" target="_blank">LeetCode 451</a>) $\longrightarrow$ Housed in **Section 02 — Strings**.

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Kth Largest Element in an Array** | Teaches QuickSelect ($O(N)$ avg) vs Heap ($O(N \log K)$) — belongs to **Pattern 02**. |
| **Top K Frequent Elements (LC 347)** | Frequency map lookup + Heap — canonical home is **Section 03 (Hashing)**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 2 |
| **Medium** | 1 |
| **Total** | **3** |

---

## 🎯 Question Set (3 Unique Questions)

### Q1. Kth Largest Element in a Stream
<a href="https://leetcode.com/problems/kth-largest-element-in-a-stream/" target="_blank">LeetCode 703</a> — **Easy**

**Target Skill:** Maintaining a fixed-size Min Heap for streaming data.

**Core Reasoning:**
- Initialize `PriorityQueue<Integer> minHeap` of size $K$.
- Add all initial elements using `add(val)`. If size exceeds $K$, poll min.
- Method `add(val)` offers new value, polls if size $> K$, and returns `minHeap.peek()`.

**Why it belongs here:** Canonical entry problem establishing $O(N \log K)$ stream filtering.

**Complexity:** Time: $O(\log K)$ per stream `add()`, Space: $O(K)$.

---

### Q2. K Closest Points to Origin
<a href="https://leetcode.com/problems/k-closest-points-to-origin/" target="_blank">LeetCode 973</a> — **Medium**

**Target Skill:** Max Heap of size $K$ with custom Euclidean distance comparator.

**Core Reasoning:**
- To find $K$ **closest** points (smallest distances), use a **Max Heap** of capacity $K$.
- Distance metric: $d^2 = x^2 + y^2$.
- Max Heap comparator: `(a, b) -> (b[0]^2 + b[1]^2) - (a[0]^2 + a[1]^2)`.
- If `heap.size() > K`, pop the farthest point (`heap.poll()`).

**Why it belongs here:** Demonstrates using a Max Heap to keep the $K$ smallest distance metrics in $O(N \log K)$ time.

**Complexity:** Time: $O(N \log K)$, Space: $O(K)$.

---

### Q3. Last Stone Weight
<a href="https://leetcode.com/problems/last-stone-weight/" target="_blank">LeetCode 1046</a> — **Easy**

**Target Skill:** Max Heap simulation of pairwise reduction.

**Core Reasoning:**
- Smash two heaviest stones $y$ and $x$ ($x \le y$) in each round.
- Use a **Max Heap** (`Collections.reverseOrder()`). Offer all stones.
- While `maxHeap.size() > 1`: poll two heaviest $y$ and $x$. If $y > x$, offer $y - x$.
- Return `maxHeap.isEmpty() ? 0 : maxHeap.peek()`.

**Why it belongs here:** Direct Max Heap simulation for repeated largest-element extraction.

**Complexity:** Time: $O(N \log N)$, Space: $O(N)$.

---

## ⚡ Mastery Checklist

- [ ] Can you explain why keeping the $K$ largest elements requires a **Min Heap**, not a Max Heap?
- [ ] What is the time complexity of `Last Stone Weight` and why does a Max Heap optimize it?
- [ ] Why does fixed-size heap filtering achieve $O(N \log K)$ time compared to full sorting $O(N \log N)$?
