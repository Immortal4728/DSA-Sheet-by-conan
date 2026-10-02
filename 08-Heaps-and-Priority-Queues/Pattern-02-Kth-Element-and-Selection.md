# Pattern 02: Kth Element & Selection

> Finding the $K$-th element across 1D arrays, 2D sorted matrices, or pair combinations does NOT require fully sorting all $N$ items. Min-Heaps maintain partial frontier ordering to extract the $K$-th element efficiently.

---

## Why This Pattern Exists

When elements exist across $M$ sorted rows or pair combinations:
- Fully building and sorting all pairs takes $O(N^2 \log(N^2))$ time.
- A **Min Heap Frontier** maintains only the current smallest candidates across rows/lists, expanding candidates on demand to find the $K$-th element in **$O(K \log M)$ time**.

```text
Row 0: [1, 5, 9]   ---> Min Heap initialized with first element of each row:
Row 1: [10, 11, 13]     Heap: [(1, r=0, c=0), (10, r=1, c=0), (12, r=2, c=0)]
Row 2: [12, 13, 15]     Pop min (1), offer next element from Row 0 (5).
```

### Recognition Rule: Not Every K-th Problem is a Heap Problem!

$$\begin{array}{c}
\text{K-th Element in Array (LC 215): QuickSelect } O(N) \text{ vs Heap } O(N \log K) \\
\text{K-th Smallest Pair Distance (LC 719): Binary Search on Answer } O(N \log(\max - \min))
\end{array}$$

> **Important Cross-Section Reference:** **LC 719 (K-th Smallest Pair Distance)** is NOT a Heap problem because $K$ can be $O(N^2)$. It is canonically housed in **Section 12 — Binary Search / Search on Answer**.

---

## ☕ Standard Java Templates

### 2D Sorted Matrix K-th Smallest Frontier (LC 378)
```java
public int kthSmallest(int[][] matrix, int k) {
    int n = matrix.length;
    // Min Heap storing int[]{val, row, col}
    PriorityQueue<int[]> minHeap = new PriorityQueue<>((a, b) -> a[0] - b[0]);
    
    // Push first element of each row (up to Min(n, k))
    for (int r = 0; r < Math.min(n, k); r++) {
        minHeap.offer(new int[]{matrix[r][0], r, 0});
    }
    
    int count = 0;
    while (!minHeap.isEmpty()) {
        int[] curr = minHeap.poll();
        count++;
        if (count == k) return curr[0];
        
        int r = curr[1], c = curr[2];
        if (c + 1 < n) {
            minHeap.offer(new int[]{matrix[r][c + 1], r, c + 1});
        }
    }
    return -1;
}
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **K-th Smallest Pair Distance (LC 719)** | Count of pairs within distance $D$ exceeds heap memory limits — belongs to **Binary Search / Search on Answer (Section 12)**. |
| **Kth Smallest Element in a BST** | Uses BST Inorder traversal rank tracking — belongs to **BST (Section 07)**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Medium (Core)** | 3 |
| **Medium (Selective / Stretch)** | 1 |
| **Total** | **4** |

---

## 🎯 Question Set (3 Core + 1 Selective/Stretch = 4 Questions)

### Q1. Kth Largest Element in an Array
<a href="https://leetcode.com/problems/kth-largest-element-in-an-array/" target="_blank">LeetCode 215</a> — **Medium**

**Target Skill:** Min-Heap capacity $K$ vs QuickSelect algorithm comparison.

**Core Reasoning:**
- **Heap Approach:** Maintain Min Heap of size $K$. Push element, poll if size $> K$. Time $O(N \log K)$, Space $O(K)$.
- **QuickSelect Approach:** Partition array around pivot. Average Time $O(N)$, Space $O(1)$.

**Why it belongs here:** Canonical selection problem contrasting $O(N \log K)$ Heap filtering with $O(N)$ QuickSelect.

**Complexity:** Heap: $O(N \log K)$ Time, $O(K)$ Space.

---

### Q2. Kth Smallest Element in a Sorted Matrix
<a href="https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/" target="_blank">LeetCode 378</a> — **Medium**

**Target Skill:** Matrix row-frontier expansion using Min Heap.

**Core Reasoning:**
- Each row and column of $N \times N$ matrix is sorted in ascending order.
- Push first element of each row into Min Heap: `(matrix[r][0], r, 0)`.
- Pop smallest element `curr`. If count equals $K$, return `curr.val`.
- Push next element in same row `(matrix[r][c+1], r, c+1)` into Min Heap.

**Why it belongs here:** Canonical frontier selection over multiple sorted streams.

**Complexity:** Time: $O(K \log N)$, Space: $O(N)$.

---

### Q3. Find K Pairs with Smallest Sums
<a href="https://leetcode.com/problems/find-k-pairs-with-smallest-sums/" target="_blank">LeetCode 373</a> — **Medium**

**Target Skill:** 2D pair search space frontier expansion.

**Core Reasoning:**
- Given sorted arrays `nums1` and `nums2`. Find $K$ pairs $(u, v)$ with smallest sums.
- Push initial pairs `(nums1[i] + nums2[0], i, 0)` for `i` from $0$ to $\min(\text{nums1.length}, K)$ into Min Heap.
- Poll smallest pair `(sum, i, j)`, add `[nums1[i], nums2[j]]` to result.
- If $j + 1 < \text{nums2.length}$, offer next pair `(nums1[i] + nums2[j+1], i, j+1)`.

**Why it belongs here:** Multi-stream pair selection without allocating all $N_1 \times N_2$ pair combinations.

**Complexity:** Time: $O(K \log K)$, Space: $O(K)$.

---

### Q4. K-th Smallest Prime Fraction (Selective / Stretch)
<a href="https://leetcode.com/problems/k-th-smallest-prime-fraction/" target="_blank">LeetCode 786</a> — **Medium** — ⚠️ *Selective / Stretch*

**Target Skill:** Fraction comparison using Min Heap vs Binary Search.

**Core Reasoning:**
- Given sorted array `arr` of prime numbers. Fraction $arr[i] / arr[j]$ for $i < j$.
- **Min Heap Approach:** Push smallest fractions `arr[0] / arr[j]` for each $j$ into Min Heap `(arr[i]/arr[j], i, j)`. Poll $K$ times, advancing numerator index $i \to i+1$.
- **Binary Search Interpretation:** Binary search on floating value range $[0, 1.0]$. Count fractions $\le mid$.

**Why it belongs here:** Marked explicitly as a **selective / stretch** problem because it bridges Heap frontier traversal with Binary Search on Value.

**Complexity:** Heap: $O((K + N) \log N)$ Time, $O(N)$ Space.

---

## ⚡ Mastery Checklist

- [ ] Can you implement $O(K \log N)$ matrix frontier extraction for `Kth Smallest in Sorted Matrix`?
- [ ] Why is QuickSelect $O(N)$ average time while Min Heap is $O(N \log K)$ for `Kth Largest Element`?
- [ ] Do you understand why `LC 719 (Kth Smallest Pair Distance)` is NOT a Heap problem, but a Binary Search on Answer problem?
- [ ] How do you avoid duplicate pair pushes in `Find K Pairs with Smallest Sums`?
