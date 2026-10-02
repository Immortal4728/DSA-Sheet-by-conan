# 📦 Module 08: Heaps & Priority Queues

> Don't count repetitions. Count genuinely different reasoning. Master fixed-size Top-K filtering, $K$-th element selection frontiers, dual-heap streaming medians, $K$-way merging, OS resource scheduling, dynamic greedy eviction, and custom Comparators for SDE-1 interviews.

---

## 🎯 Section Objective & SDE-1 Scope

A Heap (Priority Queue) maintains dynamic access to the extremum (minimum or maximum element) in $O(1)$ time while supporting $O(\log N)$ insertions and deletions.

This curriculum is locked at **22 core + 1 selective (LC 786) = 23 canonical questions** organized across **8 pattern files**. It focuses on core SDE-1 interview patterns and excludes competitive-programming bloat (no Fibonacci Heaps, Pairing Heaps, or Segment Trees).

---

## 🗺️ Curriculum Architecture

| Pattern File | Focus Area | Canonical Questions | Core Mechanism / Invariant |
|--------------|------------|---------------------|----------------------------|
| **[Pattern 01](./Pattern-01-Heap-Fundamentals-and-Top-K.md)** | Heap Fundamentals & Top-K | 3 | Min Heap of capacity $K$ for Top-$K$ filtering ($O(N \log K)$) |
| **[Pattern 02](./Pattern-02-Kth-Element-and-Selection.md)** | Kth Element & Selection | 3 core + 1 stretch | Frontier candidate expansion over 2D sorted matrices/pairs |
| **[Pattern 03](./Pattern-03-Two-Heaps-and-Dynamic-Ordered-State.md)** | Two Heaps & Dynamic State | 2 | `maxHeap` (lower half) + `minHeap` (upper half) for $O(1)$ median |
| **[Pattern 04](./Pattern-04-K-Way-Merge.md)** | K-Way Merge | 1 | Min Heap of size $K$ holding active stream candidates |
| **[Pattern 05](./Pattern-05-Scheduling-and-Resource-Allocation.md)** | Scheduling & Resource Allocation | 5 | Event timeline sorting + dual heaps (`busy` vs `available`) |
| **[Pattern 06](./Pattern-06-Greedy-and-Heap.md)** | Greedy & Heap | 5 | Priority Queue dynamic candidate container for greedy regret eviction |
| **[Pattern 07](./Pattern-07-Custom-Priority-Ordering.md)** | Custom Priority Ordering | 3 | Multi-attribute `Comparator<T>` tie-breaking in Java |
| **[Pattern 08](./Pattern-08-Hidden-Heap-Recognition.md)** | Hidden Heap Recognition | *Recognition* | Unlabeled diagnostic recognition (0 new questions) |

---

## 🔗 Cross-Section Ownership & Reference Rules

To preserve single canonical ownership across the repository:
- **LC 347 (Top K Frequent Elements):** Housed in **Section 03 (Hashing)** (frequency map + heap).
- **LC 451 (Sort Characters By Frequency):** Housed in **Section 02 (Strings)**.
- **LC 23 (Merge k Sorted Lists):** Housed in **Section 04 (Linked Lists)**.
- **LC 621 (Task Scheduler):** Housed canonically in **Section 08 Pattern 05 (Scheduling)**.
- **LC 719 (K-th Smallest Pair Distance):** Housed in **Section 12 (Binary Search / Search on Answer)**. (NOT a Heap problem because $K$ can be $O(N^2)$).

---

## ⚖️ Heap Comparisons

| Feature | Sorting | Heap / Priority Queue | Binary Search Tree (BST) |
|---------|---------|-----------------------|--------------------------|
| **Ordering** | Full ordering ($O(N \log N)$) | Partial ordering ($O(1)$ peek extremum) | Full ordering ($O(H)$ search/range) |
| **Top-K Search** | $O(N \log N)$ time | **$O(N \log K)$ time** | $O(H + K)$ time |
| **Dynamic Insert** | $O(N)$ shift | **$O(\log N)$ insert** | $O(H)$ insert |
| **Primary Use** | Static array ordering | Streaming candidate selection / scheduling | Global range search & predecessor/successor |

---

## 📑 Full Question Matrix (23 Canonical Questions)

### Pattern 01: Heap Fundamentals & Top-K (3 Questions)
1. <a href="https://leetcode.com/problems/kth-largest-element-in-a-stream/" target="_blank">LeetCode 703: Kth Largest Element in a Stream</a> — **Easy**
2. <a href="https://leetcode.com/problems/k-closest-points-to-origin/" target="_blank">LeetCode 973: K Closest Points to Origin</a> — **Medium**
3. <a href="https://leetcode.com/problems/last-stone-weight/" target="_blank">LeetCode 1046: Last Stone Weight</a> — **Easy**

### Pattern 02: Kth Element & Selection (3 Core + 1 Selective/Stretch = 4 Questions)
1. <a href="https://leetcode.com/problems/kth-largest-element-in-an-array/" target="_blank">LeetCode 215: Kth Largest Element in an Array</a> — **Medium**
2. <a href="https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/" target="_blank">LeetCode 378: Kth Smallest Element in a Sorted Matrix</a> — **Medium**
3. <a href="https://leetcode.com/problems/find-k-pairs-with-smallest-sums/" target="_blank">LeetCode 373: Find K Pairs with Smallest Sums</a> — **Medium**
4. <a href="https://leetcode.com/problems/k-th-smallest-prime-fraction/" target="_blank">LeetCode 786: K-th Smallest Prime Fraction</a> — **Medium** *(⚠️ Selective / Stretch)*

### Pattern 03: Two Heaps & Dynamic Ordered State (2 Questions)
1. <a href="https://leetcode.com/problems/find-median-from-data-stream/" target="_blank">LeetCode 295: Find Median from Data Stream</a> — **Hard**
2. <a href="https://leetcode.com/problems/sliding-window-median/" target="_blank">LeetCode 480: Sliding Window Median</a> — **Hard**

### Pattern 04: K-Way Merge (1 Question)
1. <a href="https://leetcode.com/problems/smallest-range-covering-elements-from-k-lists/" target="_blank">LeetCode 632: Smallest Range Covering Elements from K Lists</a> — **Hard**

### Pattern 05: Scheduling & Resource Allocation (5 Questions)
1. <a href="https://leetcode.com/problems/meeting-rooms-ii/" target="_blank">LeetCode 253: Meeting Rooms II</a> — **Medium**
2. <a href="https://leetcode.com/problems/single-threaded-cpu/" target="_blank">LeetCode 1834: Single-Threaded CPU</a> — **Medium**
3. <a href="https://leetcode.com/problems/task-scheduler/" target="_blank">LeetCode 621: Task Scheduler</a> — **Medium**
4. <a href="https://leetcode.com/problems/process-tasks-using-servers/" target="_blank">LeetCode 1882: Process Tasks Using Servers</a> — **Medium**
5. <a href="https://leetcode.com/problems/meeting-rooms-iii/" target="_blank">LeetCode 2402: Meeting Rooms III</a> — **Hard**

### Pattern 06: Greedy & Heap (5 Questions)
1. <a href="https://leetcode.com/problems/maximum-performance-of-a-team/" target="_blank">LeetCode 1383: Maximum Performance of a Team</a> — **Hard**
2. <a href="https://leetcode.com/problems/furthest-building-you-can-reach/" target="_blank">LeetCode 1642: Furthest Building You Can Reach</a> — **Medium**
3. <a href="https://leetcode.com/problems/course-schedule-iii/" target="_blank">LeetCode 630: Course Schedule III</a> — **Hard**
4. <a href="https://leetcode.com/problems/minimum-cost-to-hire-k-workers/" target="_blank">LeetCode 857: Minimum Cost to Hire K Workers</a> — **Hard**
5. <a href="https://leetcode.com/problems/maximum-number-of-events-that-can-be-attended/" target="_blank">LeetCode 1353: Maximum Number of Events That Can Be Attended</a> — **Medium**

### Pattern 07: Custom Priority Ordering (3 Questions)
1. <a href="https://leetcode.com/problems/reorganize-string/" target="_blank">LeetCode 767: Reorganize String</a> — **Medium**
2. <a href="https://leetcode.com/problems/the-k-weakest-rows-in-a-matrix/" target="_blank">LeetCode 1337: The K Weakest Rows in a Matrix</a> — **Easy**
3. <a href="https://leetcode.com/problems/seat-reservation-manager/" target="_blank">LeetCode 1845: Seat Reservation Manager</a> — **Medium**

### Pattern 08: Hidden Heap Recognition (Recognition Layer Only — 0 New Questions)
- Diagnostic review layer referencing LC 1642, LC 630, LC 1834, LC 632, LC 1353, and LC 767.

---

## ⚡ SDE-1 Interview Mastery Checklist

- [ ] Explain why a Min Heap of size $K$ extracts the Top-$K$ largest elements in $O(N \log K)$ time.
- [ ] Implement matrix/pair candidate frontier expansion using a Min Heap (LC 378, LC 373).
- [ ] Maintain dual heaps (`maxHeap` for lower half, `minHeap` for upper half) to find dynamic stream medians in $O(1)$ time.
- [ ] Execute K-Way Merge over multiple sorted streams in $O(N \log K)$ time.
- [ ] Design OS resource allocation loops using dual heaps (`busy` vs `available`) with custom Comparators.
- [ ] Use a Heap as a dynamic greedy candidate container to perform regret evictions (LC 630, LC 1642).
- [ ] Write multi-attribute Java Comparators (`a[0] != b[0] ? a[0] - b[0] : a[1] - b[1]`) without integer overflow bugs.
