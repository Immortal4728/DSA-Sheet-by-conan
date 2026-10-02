# Pattern 02 — Modified Sorted Structures

Standard binary search relies on global monotonicity across a fully sorted array. When arrays are modified—by **rotation**, **duplicate elements**, or **structural parity shifts**—the global sorted property is disrupted. 

The goal of this pattern is to identify local invariants or parity rules that allow halving the search space even when global monotonicity is broken.

---

## Core Concept & Discontinuity Reasoning

### 1. Rotated Arrays with Duplicates
In a rotated sorted array without duplicates (<a href="https://leetcode.com/problems/search-in-rotated-sorted-array/" target="_blank">LeetCode 33</a>), at least one half (`[low, mid]` or `[mid, high]`) is guaranteed to be strictly sorted.

However, when **duplicates** are present (e.g., `nums = [1, 0, 1, 1, 1]` or `nums = [2, 2, 2, 0, 1, 2]`):
- It is possible that `nums[low] == nums[mid] == nums[high]`.
- In this ambiguous edge case, it is impossible to determine which half is sorted!
- **Resolution**: Shrink the boundary linearly (`low++` or `high--`). This degrades worst-case performance to $O(N)$, but preserves binary search correctness for distinct segments ($O(\log N)$ average).

### 2. Parity-Based Boundary Shifts
In an array where every element appears twice except one single element (<a href="https://leetcode.com/problems/single-element-in-a-sorted-array/" target="_blank">LeetCode 540</a>):
- Before the single element: Paired elements start at **even** indices and end at **odd** indices (`(even, odd)`).
- After the single element: Paired elements start at **odd** indices and end at **even** indices (`(odd, even)`).

---

## Pattern Questions (3 Canonical)

### 1. Search in Rotated Sorted Array II — <a href="https://leetcode.com/problems/search-in-rotated-sorted-array-ii/" target="_blank">LeetCode 81</a>

#### Problem Statement
Given a rotated sorted integer array `nums` that may contain **duplicates**, and a `target`, return `true` if `target` is in `nums`, or `false` otherwise.

#### Key Insight & Handling Duplicate Ambiguity
1. Compare `nums[low]`, `nums[mid]`, and `nums[high]`.
2. If `nums[low] == nums[mid] && nums[mid] == nums[high]`, increment `low++` and decrement `high--` to skip ambiguous duplicates.
3. Otherwise, check which half is strictly sorted (`nums[low] <= nums[mid]` vs `nums[mid] <= nums[high]`) and check if `target` lies within that half's range.

#### Java Implementation
```java
public class SearchInRotatedSortedArrayII {
    public boolean search(int[] nums, int target) {
        int low = 0, high = nums.length - 1;

        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (nums[mid] == target) return true;

            // Handle ambiguous duplicates
            if (nums[low] == nums[mid] && nums[mid] == nums[high]) {
                low++;
                high--;
            }
            // Left half is sorted
            else if (nums[low] <= nums[mid]) {
                if (nums[low] <= target && target < nums[mid]) {
                    high = mid - 1; // Target lies in left sorted half
                } else {
                    low = mid + 1;  // Target lies in right half
                }
            }
            // Right half is sorted
            else {
                if (nums[mid] < target && target <= nums[high]) {
                    low = mid + 1;  // Target lies in right sorted half
                } else {
                    high = mid - 1; // Target lies in left half
                }
            }
        }

        return false;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** Average $O(\log N)$, Worst-case $O(N)$ (when all elements are identical duplicates).
- **Space Complexity:** $O(1)$ constant space.

---

### 2. Find Minimum in Rotated Sorted Array II — <a href="https://leetcode.com/problems/find-minimum-in-rotated-sorted-array-ii/" target="_blank">LeetCode 154</a>

#### Problem Statement
Given a rotated sorted array `nums` of length `n` that may contain **duplicates**, return the minimum element of this array.

#### Key Insight & Right-Boundary Comparison
Compare `nums[mid]` with `nums[high]`:
- If `nums[mid] > nums[high]`: Minimum must lie strictly in the right half (`low = mid + 1`).
- If `nums[mid] < nums[high]`: Minimum lies in the left half or at `mid` (`high = mid`).
- If `nums[mid] == nums[high]`: We cannot determine which side holds the minimum. Safely decrement `high--`.

#### Java Implementation
```java
public class FindMinimumInRotatedSortedArrayII {
    public int findMin(int[] nums) {
        int low = 0, high = nums.length - 1;

        while (low < high) {
            int mid = low + (high - low) / 2;

            if (nums[mid] > nums[high]) {
                low = mid + 1;   // Minimum is to the right
            } else if (nums[mid] < nums[high]) {
                high = mid;      // Minimum is at mid or to the left
            } else {
                high--;          // Duplicate element; safely shrink right bound
            }
        }

        return nums[low];
    }
}
```

#### Complexity Analysis
- **Time Complexity:** Average $O(\log N)$, Worst-case $O(N)$ (e.g., `[1, 1, 1, 1, 1]`).
- **Space Complexity:** $O(1)$ constant auxiliary space.

---

### 3. Single Element in a Sorted Array — <a href="https://leetcode.com/problems/single-element-in-a-sorted-array/" target="_blank">LeetCode 540</a>

#### Problem Statement
Given a sorted array `nums` consisting of only integers where every element appears exactly twice, except for one element which appears exactly once. Return the single element. Solution must run in $O(\log N)$ time and $O(1)$ space.

#### Key Insight & Index Parity Invariant
Ensure `mid` is always an **even index**:
- If `mid` is odd, decrement `mid--` so `mid` points to an even index.
- If `nums[mid] == nums[mid + 1]`: The pair `(even, odd)` is intact! The single element must lie to the **right** of `mid + 1` (`low = mid + 2`).
- If `nums[mid] != nums[mid + 1]`: The pair pattern is disrupted! The single element lies at `mid` or to the **left** (`high = mid`).

#### Java Implementation
```java
public class SingleElementInSortedArray {
    public int singleNonDuplicate(int[] nums) {
        int low = 0, high = nums.length - 1;

        while (low < high) {
            int mid = low + (high - low) / 2;

            // Ensure mid is even
            if (mid % 2 == 1) {
                mid--;
            }

            // Check if pair starts at even index mid
            if (nums[mid] == nums[mid + 1]) {
                low = mid + 2; // Pair is intact; single element is on the right
            } else {
                high = mid;    // Disruption detected; single element is on the left (or mid)
            }
        }

        return nums[low];
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(\log N)$ — Strict halving of even indices.
- **Space Complexity:** $O(1)$ — No extra memory allocated.

---

## Pattern Summary

| Problem | Structural Disruption | Key Invariant / Disambiguation | Worst-Case Time |
| :--- | :--- | :--- | :--- |
| **LC 81 (Rotated Search II)** | Rotation + Duplicates | `nums[low] == mid == high` $\Rightarrow$ `low++, high--` | $O(N)$ |
| **LC 154 (Find Min II)** | Rotation + Duplicates | `nums[mid] == nums[high]` $\Rightarrow$ `high--` | $O(N)$ |
| **LC 540 (Single Element)**| Missing Pair Discontinuity | Even-index `mid` pair match `nums[mid] == nums[mid+1]` | $O(\log N)$ |
