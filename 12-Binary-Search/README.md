# Section 12 — Binary Search / Search on Answer

Binary Search is far more than a algorithm for finding an element in a sorted array. In technical software engineering interviews, Binary Search is a foundational **logarithmic reduction paradigm** applied to search space boundaries, monotonic feasibility predicates, rotated structures, derived distance counting, and multi-array partitioning.

---

## Final Curriculum Architecture

This module contains **15 canonical core questions + 1 selective/advanced question**, organized across 6 structured pattern files:

```text
12-Binary-Search/
├── Pattern-01-Boundary-and-Search-Invariants.md
├── Pattern-02-Modified-Sorted-Structures.md
├── Pattern-03-Binary-Search-on-Answer.md
├── Pattern-04-Monotonic-Predicate-and-Feasibility.md
├── Pattern-05-Advanced-Selection-and-Distance.md
├── Pattern-06-Hidden-Binary-Search-Recognition.md
└── README.md
```

---

## 1. Core Principles & Search Invariants

### 1. Binary-Search Invariants
A binary search maintains a search space `[low, high]` such that the target/boundary is guaranteed to lie within `[low, high]`. In every step, evaluating `mid` halves the search space ($O(\log N)$).

### 2. Boundary Search
Instead of checking for exact equality (`nums[mid] == target`), boundary search evaluates boolean predicates (`isFeasible(mid)`).

### 3. First-Valid / Last-Valid Reasoning
- **First-Valid (First True)**: Record candidate `ans = mid` and move left (`high = mid - 1`).
- **Last-Valid (Last True)**: Record candidate `ans = mid` and move right (`low = mid + 1`).

---

## 2. Structural Discontinuities & Modifications

### 4. Modified Sorted Structures
When arrays are shifted, rotated, or contains parity breaks, standard global monotonicity is disrupted.

### 5. Rotated Arrays
At least one half of a rotated sorted array (`[low, mid]` or `[mid, high]`) is guaranteed to be strictly sorted.

### 6. Duplicate Complications
When `nums[low] == nums[mid] == nums[high]`, it is impossible to determine which half is sorted. Resolution: shrink boundaries linearly (`low++`, `high--`), giving $O(N)$ worst-case and $O(\log N)$ average-case.

---

## 3. Search on Answer & Monotonic Feasibility

### 7. Binary Search on Answer
Applied to optimization problems asking for the minimum or maximum parameter value satisfying a constraint. Convert optimization into a boolean decision problem over range `[low, high]`.

### 8. Search-Space Construction
Determine tight lower bounds (`low`) and upper bounds (`high`). For example, in speed problems, `low = 1` and `high = 10^7`.

### 9. Monotonic Predicates & 10. Feasibility Functions
A decision function `feasible(x)` must be monotonic: $x_1 \le x_2 \implies \text{feasible}(x_1) \le \text{feasible}(x_2)$.
- **P03 vs P04 Distinction**:
  - **P03**: Focuses on defining and searching the numerical answer space.
  - **P04**: Focuses on designing and mathematically proving the monotonic feasibility predicate.

### 11. First-True / Last-True Reasoning
Mapping decision functions to step-function boolean sequences: `[false, false, true, true, true]`.

---

## 4. Advanced Partitioning & Counting

### 12. Counting Predicates
Combining binary search over a candidate value $D$ with $O(N)$ sliding windows or two-pointers to count elements/pairs satisfying a distance constraint.

### 13. Advanced Partition-Based Binary Search
Binary searching on partition cuts across two sorted arrays simultaneously (<a href="https://leetcode.com/problems/median-of-two-sorted-arrays/" target="_blank">LeetCode 4</a>) ensuring $O(\log(\min(M, N)))$ time.

### 14. Hidden Binary-Search Recognition
Detecting binary search in unlabeled problems by recognizing mountain slope changes, threshold conditions, or sorted potion/spell lookups.

---

## 5. Paradigm Comparisons & Complexities

### 15. Binary Search vs Sorting, Heaps, and Other Approaches
- **Binary Search**: Applies when candidate space is monotonic or index-accessible ($O(\log N)$ time, $O(1)$ space).
- **Sorting**: Preprocessing step to unlock binary search ($O(N \log N)$ time).
- **Heaps**: Used for dynamic top-K streaming without global monotonicity ($O(N \log K)$ time).

### 16. Java Implementation Templates

#### Overflow-Safe Midpoint Calculation
```java
// Prevent 32-bit signed integer overflow when low + high > 2^31 - 1
int mid = low + (high - low) / 2;
```

#### Standard First-True Binary Search Template
```java
public int binarySearchFirstTrue(int low, int high) {
    int ans = -1;
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (isFeasible(mid)) {
            ans = mid;        // Record candidate
            high = mid - 1;   // Search left half
        } else {
            low = mid + 1;    // Search right half
        }
    }
    return ans;
}
```

### 18. Time and Space Complexity Summary

| Pattern | Time Complexity | Space Complexity | Core Application |
| :--- | :--- | :--- | :--- |
| **Boundary Invariants** | $O(\log N)$ | $O(1)$ | First/last position, H-index |
| **Modified Structures** | $O(\log N)$ avg / $O(N)$ worst | $O(1)$ | Rotated duplicates, single element |
| **Search on Answer** | $O(N \log(\text{Range}))$ | $O(1)$ | Minimum/maximum parameter optimization |
| **Monotonic Feasibility** | $O(N \log(\text{Range}))$ | $O(1)$ | Capacity, bouquet bloom, trip timers |
| **Advanced Distance** | $O(N \log N + N \log D)$ | $O(1)$ | $K$-th smallest pair distance, medians |
| **Hidden Recognition** | $O(\log N)$ or $O(N \log M)$ | $O(1)$ | Mountain peak, spell/potion thresholds |

---

## 🔒 Section Ownership Rules

- **Section 01 Canonical Ownership**: <a href="https://leetcode.com/problems/search-in-rotated-sorted-array/" target="_blank">LeetCode 33</a>, <a href="https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/" target="_blank">LeetCode 34</a>, <a href="https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/" target="_blank">LeetCode 153</a>, <a href="https://leetcode.com/problems/find-peak-element/" target="_blank">LeetCode 162</a>, <a href="https://leetcode.com/problems/koko-eating-bananas/" target="_blank">LeetCode 875</a>, <a href="https://leetcode.com/problems/split-array-largest-sum/" target="_blank">LeetCode 410</a>, <a href="https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/" target="_blank">LeetCode 1011</a>.
- **Section 08 Canonical Ownership**: <a href="https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/" target="_blank">LeetCode 378 (Kth Smallest Matrix)</a> belongs canonically to Section 08 Heaps.
- **Excluded**: <a href="https://leetcode.com/problems/search-a-2d-matrix-ii/" target="_blank">LeetCode 240</a> is excluded as its dominant identity is staircase search.

---

## Mastery Standard

Upon completing Section 12, the learner will be able to:
- Write bug-free, overflow-safe binary search algorithms from a blank editor.
- Formulate precise loop invariants for first-true and last-true boundaries.
- Handle rotated arrays with duplicate elements without infinite loops.
- Construct tight numerical answer spaces `[low, high]` for optimization problems.
- Design and mathematically prove the monotonicity of `feasible(x)` decision functions.
- Combine binary search with two-pointer sliding window pair counting.
- Perform partition binary search across multiple sorted arrays.
- Recognize hidden binary search opportunities in unlabeled problem statements.
- Explain complexity trade-offs between Binary Search, Greedy, Heaps, and Sorting.
