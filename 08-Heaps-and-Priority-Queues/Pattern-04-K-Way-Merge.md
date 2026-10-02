# Pattern 04: K-Way Merge

> Merging $K$ sorted arrays, lists, or streams requires maintaining a **Min Heap of capacity $K$** containing one active candidate from each sorted source. Extracted min elements advance their respective source pointers.

---

## Why This Pattern Exists

When merging $K$ sorted streams with $N$ total elements:
- Comparing all $K$ stream heads naively takes $O(K)$ per element $\to O(N \cdot K)$ total.
- Maintaining a Min Heap of size $K$ reduces min-finding to $O(\log K)$ $\to$ **$O(N \log K)$ total time**.

```text
Stream 1: [4, 10, 15, 24, 26]
Stream 2: [0,  9, 12, 20]
Stream 3: [5, 18, 22, 30]

Min Heap (Capacity K=3): [(0, S2), (4, S1), (5, S3)]
- Extract Min: 0 (from S2).
- Update Window / Result.
- Advance Stream 2 pointer -> Push 9 to Heap.
```

---

## 🔗 Cross-Section Reference

> **Canonical Ownership:** **Merge k Sorted Lists (<a href="https://leetcode.com/problems/merge-k-sorted-lists/" target="_blank">LeetCode 23</a>)** is a quintessential K-Way Merge application, but its physical canonical home is **Section 04 — Linked Lists**. Do NOT duplicate its file here.

---

## ☕ Standard Java Template

### K-Way Range Processing Pattern (LC 632)
```java
class Element {
    int val, listIdx, elemIdx;
    Element(int val, int listIdx, int elemIdx) {
        this.val = val;
        this.listIdx = listIdx;
        this.elemIdx = elemIdx;
    }
}

public int[] smallestRange(List<List<Integer>> nums) {
    PriorityQueue<Element> minHeap = new PriorityQueue<>((a, b) -> a.val - b.val);
    int maxVal = Integer.MIN_VALUE;
    
    // Step 1: Push first element of each of the K lists
    for (int i = 0; i < nums.size(); i++) {
        int val = nums.get(i).get(0);
        minHeap.offer(new Element(val, i, 0));
        maxVal = Math.max(maxVal, val);
    }
    
    int rangeStart = 0, rangeEnd = Integer.MAX_VALUE;
    
    // Step 2: Process heap and advance pointers
    while (minHeap.size() == nums.size()) {
        Element minElem = minHeap.poll();
        
        // Update smallest range if current (maxVal - minElem.val) is smaller
        if (maxVal - minElem.val < rangeEnd - rangeStart) {
            rangeStart = minElem.val;
            rangeEnd = maxVal;
        }
        
        // Advance list pointer
        if (minElem.elemIdx + 1 < nums.get(minElem.listIdx).size()) {
            int nextVal = nums.get(minElem.listIdx).get(minElem.elemIdx + 1);
            minHeap.offer(new Element(nextVal, minElem.listIdx, minElem.elemIdx + 1));
            maxVal = Math.max(maxVal, nextVal);
        }
    }
    
    return new int[]{rangeStart, rangeEnd};
}
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Merge k Sorted Lists (LC 23)** | Housed canonically in **Section 04 (Linked Lists)**. |
| **Kth Smallest Element in Sorted Matrix** | Housed in **Kth Selection (Pattern 02)**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Hard** | 1 |
| **Total** | **1** |

---

## 🎯 Question Set (1 Unique Question)

### Q1. Smallest Range Covering Elements from K Lists
<a href="https://leetcode.com/problems/smallest-range-covering-elements-from-k-lists/" target="_blank">LeetCode 632</a> — **Hard**

**Target Skill:** K-Way Merge window tracking with Min Heap and running max tracking.

**Core Reasoning:**
- Find smallest range $[a, b]$ that includes at least one number from each of the $K$ sorted lists.
- Initialize Min Heap with 1st element from each list. Track running `maxVal` among current heap elements.
- At each step: `minVal = minHeap.peek().val`. Current range is `[minVal, maxVal]`.
- Update global minimum range if `maxVal - minVal` is smaller.
- Poll `minHeap` and offer next element from the polled element's list (updating `maxVal`).
- Stop when any list runs out of elements (cannot cover all $K$ lists anymore).

**Why it belongs here:** Quintessential K-Way Merge problem combining Min Heap extraction with dynamic range optimization.

**Complexity:** Time: $O(N \log K)$ (where $N$ is total elements across all lists), Space: $O(K)$.

---

## ⚡ Mastery Checklist

- [ ] Can you explain why K-Way Merge takes $O(N \log K)$ time instead of $O(N \cdot K)$?
- [ ] How does `Smallest Range` track the current max value while using a Min Heap for min value extraction?
- [ ] Why must K-Way Merge stop as soon as any one list is completely exhausted?
