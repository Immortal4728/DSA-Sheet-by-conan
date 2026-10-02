# Sorting: Ordering as an Algorithmic Tool

> Sorting transforms chaotic data into a structured format where relationships (duplicates, proximity, boundaries) become obvious. It is often the $O(N \log N)$ setup step that unlocks an $O(N)$ solution.

---

## 🔍 Beginner Audit: Why Not Just Use `Arrays.sort()`?

In the real world, you will almost always use your language's built-in sorting method (e.g., `Arrays.sort(nums)`). 

However, interviewers test your understanding of sorting algorithms for three reasons:
1. **Algorithmic thinking:** Partitioning (Quick Sort) and merging (Merge Sort) are fundamental problem-solving techniques used outside of sorting.
2. **Stability:** Does the sort preserve the relative order of equal elements? (Merge Sort is stable; Quick Sort is not).
3. **Space constraints:** Do you sort in-place ($O(1)$ space) or need extra arrays ($O(N)$ space)?

> **Key Intuition:** Do not memorize sorting code just to pass a test. Learn *why* they work so you can apply their mechanics (like the Two Pointer merge or the Pivot partition) to entirely different problems.

---

## Core Technical Intuition — Sorting Algorithms

```text
1. O(N^2) Fundamental Sorts (Compare & Swap)
   - Bubble Sort: Heaviest elements "bubble" to the right.
   - Selection Sort: Find the minimum, swap it to the front.
   - Insertion Sort: Build a sorted prefix, insert next element into place.

2. O(N log N) Efficient Sorts (Divide & Conquer)
   - Merge Sort: Split in half, sort halves, merge two sorted arrays. (Stable, O(N) space)
   - Quick Sort: Pick a pivot, partition smaller to left, larger to right. (In-place, O(1) space)

3. O(N) Selection
   - Quickselect: Partition like Quick Sort, but only recurse on the half containing the Kth element.

4. O(N) Index-Placement (Cyclic Sort)
   - When numbers are strictly in the range [1...N], place each number `x` at index `x-1`.
   - NOTE: This is an index-placement technique, not a conventional comparison sorting algorithm.
```

---

## ☕ Standard Java Code Templates

### Template 1: Merge Sort (Divide, Sort, Merge)
```java
// Divide -> Sort Left -> Sort Right -> Merge
public void mergeSort(int[] arr, int left, int right) {
    if (left >= right) return;
    int mid = left + (right - left) / 2;
    mergeSort(arr, left, mid);
    mergeSort(arr, mid + 1, right);
    merge(arr, left, mid, right);
}

private void merge(int[] arr, int left, int mid, int right) {
    int[] temp = new int[right - left + 1];
    int i = left, j = mid + 1, k = 0;
    while (i <= mid && j <= right) {
        if (arr[i] <= arr[j]) temp[k++] = arr[i++]; // Stable: keep left on equality
        else temp[k++] = arr[j++];
    }
    while (i <= mid) temp[k++] = arr[i++];
    while (j <= right) temp[k++] = arr[j++];
    System.arraycopy(temp, 0, arr, left, temp.length);
}
```

### Template 2: Quick Sort (Choose Pivot, Partition, Recurse)
```java
// Choose Pivot -> Partition -> Recurse on both sides
public void quickSort(int[] arr, int low, int high) {
    if (low >= high) return;
    int pivotIndex = partition(arr, low, high);
    quickSort(arr, low, pivotIndex - 1);
    quickSort(arr, pivotIndex + 1, high);
}

private int partition(int[] arr, int low, int high) {
    int pivot = arr[high];
    int i = low; // boundary for smaller elements
    for (int j = low; j < high; j++) {
        if (arr[j] < pivot) {
            swap(arr, i, j);
            i++;
        }
    }
    swap(arr, i, high);
    return i; // return final pivot position
}
```

### Template 3: Quickselect (Find Kth Element in O(N))
```java
// Partition -> Which side has Kth element? -> Continue only there
public int quickSelect(int[] arr, int low, int high, int k) {
    if (low == high) return arr[low];
    int pivotIndex = partition(arr, low, high); // Reuses the Quick Sort partition method
    
    if (pivotIndex == k) return arr[pivotIndex];
    else if (pivotIndex > k) return quickSelect(arr, low, pivotIndex - 1, k);
    else return quickSelect(arr, pivotIndex + 1, high, k);
}
```

### Template 4: Dutch National Flag (3-Way Partitioning)
```java
// low | middle | unknown | high
public void sortColors(int[] nums) {
    int low = 0, mid = 0, high = nums.length - 1;
    while (mid <= high) {
        if (nums[mid] == 0) { // 0 belongs in the low section
            swap(nums, low++, mid++);
        } else if (nums[mid] == 1) { // 1 belongs in the middle section
            mid++;
        } else { // 2 belongs in the high section
            swap(nums, mid, high--);
        }
    }
}
```

---

## 🔍 Step-by-Step Trace Table (Dry Run)

Tracing **Dutch National Flag** on `nums = [2, 0, 2, 1, 1, 0]`:

| `low` | `mid` | `high` | `nums[mid]` | Action | Array State |
|-------|-------|--------|-------------|--------|-------------|
| 0 | 0 | 5 | `2` | Swap mid and high, `high--` | `[0, 0, 2, 1, 1, 2]` |
| 0 | 0 | 4 | `0` | Swap low and mid, `low++`, `mid++`| `[0, 0, 2, 1, 1, 2]` |
| 1 | 1 | 4 | `0` | Swap low and mid, `low++`, `mid++`| `[0, 0, 2, 1, 1, 2]` |
| 2 | 2 | 4 | `2` | Swap mid and high, `high--` | `[0, 0, 1, 1, 2, 2]` |
| 2 | 2 | 3 | `1` | `mid++` | `[0, 0, 1, 1, 2, 2]` |
| 2 | 3 | 3 | `1` | `mid++` (loop ends) | `[0, 0, 1, 1, 2, 2]` |

**Invariant Maintained:** `[0..low-1]` are 0s. `[low..mid-1]` are 1s. `[high+1..end]` are 2s.

---

## Pattern Recognition Layer

```text
Are you asked to implement a sort or sorting mechanic explicitly?
        ↓ Yes → Write Merge Sort or Quick Sort

Are the numbers specifically constrained in a continuous range like [1...N]?
        ↓ Yes → Cyclic Sort / Index Placement (O(N) time)

Do you need to find the K-th largest/smallest element?
        ↓ Yes → Quickselect (partitioning) or Heap

Are you looking for optimal pairs, closest distances, or merging overlapping ranges?
        ↓ Yes → Sort first, then apply Greedy, Two Pointers, or Interval logic

Do elements need a non-standard ordering (e.g., forming the largest number)?
        ↓ Yes → Custom Comparator Sorting
```

---

## Core Mental Models

- **Sorting Exposes Structure:** *"In an unsorted array, duplicates can be anywhere. In a sorted array, they are adjacent. Sorting transforms global properties into local adjacencies."*
- **Quickselect is Half Quick Sort:** *"If you only partition the half where the K-th element must lie, you drop the average time complexity to $O(N)$ instead of $O(N \log N)$."*
- **Sorting as a Preprocessing Step:** *"Always ask: Does $O(N \log N)$ sorting allow me to solve the rest of the problem in $O(N)$? If the naive approach is $O(N^2)$, sorting is usually the right path."*

---

## 🛑 Pattern Boundaries

**Sorting vs Array Patterns:**
- Sorting can be a preprocessing tool rather than the main algorithm. The dominant reasoning determines classification.
- **Sort + two pointers:** May still primarily be a Two Pointers problem.
- **Sort + interval merging:** Is Sorting as a tool.
- **Sort + custom ordering:** Is sorting/comparator reasoning.
- *Do not classify every problem containing `Arrays.sort()` as a Sorting problem.*

---

## 📊 Difficulty Distribution

| Depth Tier | Count |
|------------|-------|
| **Foundation** | 3 |
| **Efficient Sorting / Selection** | 1 |
| **Index Placement** | 2 |
| **Sorting as a Tool** | 4 |
| **Interview Recognition** | 1 |
| **Total** | **11** |

---

## 🎯 Question Progression (Curated 11-Question Set)

### 1. Foundation (3 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 1 | Sort Colors | <a href="https://leetcode.com/problems/sort-colors/" target="_blank">LeetCode 75</a> | Medium | Dutch National Flag: 3-way partitioning and in-place swaps. |
| 2 | Sort an Array | <a href="https://leetcode.com/problems/sort-an-array/" target="_blank">LeetCode 912</a> | Medium | Implement Merge Sort or Quick Sort from scratch without `Arrays.sort()`. Understand $O(N \log N)$. |
| 3 | Implement Insertion Sort | (Conceptual Exercise) | Easy | Build a sorted prefix, insert element into place. Understand comparison vs swaps. |

---

### 2. Efficient Sorting / Selection (1 Question)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 4 | Kth Largest Element in an Array | <a href="https://leetcode.com/problems/kth-largest-element-in-an-array/" target="_blank">LeetCode 215</a> | Medium | Quickselect: use partitioning to find the Kth element in $O(N)$ average time. Understand why full sorting is unnecessary. |

---

### 3. Index Placement (2 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 5 | Missing Number | <a href="https://leetcode.com/problems/missing-number/" target="_blank">LeetCode 268</a> | Easy | Index placement: index/value relationship. Cyclic-placement reasoning or XOR alternative. |
| 6 | Find All Numbers Disappeared in an Array | <a href="https://leetcode.com/problems/find-all-numbers-disappeared-in-an-array/" target="_blank">LeetCode 448</a> | Easy | Index marking / cyclic-placement relationship. Reinforce structural placement reasoning. |

---

### 4. Sorting as a Tool (4 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 7 | Merge Intervals | <a href="https://leetcode.com/problems/merge-intervals/" target="_blank">LeetCode 56</a> | Medium | Sorting as preprocessing: sort by start time to make overlapping intervals strictly adjacent, then merge. |
| 8 | Maximum Product of Three Numbers | <a href="https://leetcode.com/problems/maximum-product-of-three-numbers/" target="_blank">LeetCode 628</a> | Easy | Sorting exposes extremes: understand why only the relevant extremes matter. |
| 9 | Minimum Difference Between Highest and Lowest of K Scores | <a href="https://leetcode.com/problems/minimum-difference-between-highest-and-lowest-of-k-scores/" target="_blank">LeetCode 1984</a> | Easy | Sorting + fixed-size window: recognize when sorting converts the problem into adjacent comparisons. |
| 10 | Largest Number | <a href="https://leetcode.com/problems/largest-number/" target="_blank">LeetCode 179</a> | Medium | Custom Comparator: `a + b` vs `b + a`. Learn that "sorted order" does not always mean numerical order. |

---

### 5. Interview Recognition — Pattern Hidden (1 Question)

| # | Problem | LeetCode Link | Difficulty | Objective |
|---|---------|---------------|------------|-----------|
| 11 | H-Index | <a href="https://leetcode.com/problems/h-index/" target="_blank">LeetCode 274</a> | Medium | Find the maximum $h$ such that the researcher has at least $h$ papers with $h$ citations. |

<details>
<summary>💡 Reveal Pattern Hint (Click after attempting from a blank editor)</summary>

- **Problem 11:** The problem is hard to evaluate on unsorted data. If you sort the array descending, the `i`-th paper has `citations[i]`. If `citations[i] > i`, then you have at least `i + 1` papers with that many citations. The H-index is the number of papers that satisfy this. Focus on reasoning rather than memorizing one implementation. Classification: **Sorting to Expose Monotonic Structure**.
</details>

---

## 🏆 Mastery Criteria

You have mastered the **Sorting** curriculum when you can:

- [ ] Write Merge Sort from scratch, clearly understanding the divide and merge steps.
- [ ] Write the partitioning step of Quick Sort and use it to implement Quickselect.
- [ ] Understand the Dutch National Flag invariant regions.
- [ ] Differentiate between actual sorting algorithms, index placement, and sorting-as-a-tool.
- [ ] Apply Cyclic Sort to place numbers from $1$ to $N$ in $O(N)$ time.
- [ ] Confidently write Custom Comparators in Java (`Arrays.sort(arr, (a, b) -> ...)`).
- [ ] Recognize when sorting converts an $O(N^2)$ brute force problem into an $O(N \log N)$ elegant solution.
- [ ] Implement all 11 solutions from a blank editor without tutorial dependence.

---

## ➡️ Next Step

With all standard Array patterns, Searching, and Sorting completed, you have a master-level grasp of linear data structures. You are ready to transition to **Strings**!
