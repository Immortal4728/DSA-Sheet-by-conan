# Pattern 05 — Advanced Selection & Distance

Advanced binary search extends beyond standard 1D array searches into **derived answer spaces** (such as pairwise distance counting) and **multi-array partitioning invariants**.

---

## Derived Counting Predicates & Multi-Array Partitioning

### 1. Binary Search + Sliding Window Pair Counting
When asked for the $K$-th smallest difference/distance among all pairs, sorting the array transforms distance evaluation into a sliding window operation:
- **Search Space**: Candidate distance $D \in [0, \text{nums}[N-1] - \text{nums}[0]]$.
- **Counting Predicate**: For candidate distance $D$, use two pointers (`left` and `right`) to count pairs with distance $\le D$ in $O(N)$ time.

### 2. Multi-Array Binary Search Partitioning
When searching across two sorted arrays (e.g., finding the Median of Two Sorted Arrays), binary search is performed on the **partition cut position** in the smaller array rather than values, ensuring an $O(\log(\min(M, N)))$ time complexity.

---

## Pattern Questions (1 Core + 1 Selective)

### 1. K-th Smallest Pair Distance — <a href="https://leetcode.com/problems/k-th-smallest-pair-distance/" target="_blank">LeetCode 719</a> [CORE]

#### Problem Statement
The distance of a pair of integers `a` and `b` is defined as `|a - b|`. Given an integer array `nums` and an integer `k`, return the $k$-th smallest distance among all the pairs `nums[i]` and `nums[j]` where `0 <= i < j < nums.length`.

#### Key Insight & Two-Pointer Counting Predicate
1. Sort `nums`.
2. Binary search for distance $D \in [0, \text{nums}[N-1] - \text{nums}[0]]$.
3. **Helper Function `countPairs(D)`**: For a fixed right index `right`, find the smallest `left` index such that `nums[right] - nums[left] <= D`. The number of valid pairs ending at `right` is `right - left`.
4. Total pairs with distance $\le D$ is $\sum (\text{right} - \text{left})$. If `count >= k`, candidate distance $D$ is feasible.

#### Java Implementation
```java
import java.util.Arrays;

public class KthSmallestPairDistance {
    public int smallestDistancePair(int[] nums, int k) {
        Arrays.sort(nums);
        int n = nums.length;
        int low = 0;
        int high = nums[n - 1] - nums[0];
        int ans = high;

        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (countPairsWithDistanceLessThanOrEqual(nums, mid) >= k) {
                ans = mid;       // Feasible; search left for smaller distance
                high = mid - 1;
            } else {
                low = mid + 1;   // Not enough pairs; increase distance
            }
        }

        return ans;
    }

    private int countPairsWithDistanceLessThanOrEqual(int[] nums, int maxDist) {
        int count = 0;
        int left = 0;

        for (int right = 0; right < nums.length; right++) {
            while (nums[right] - nums[left] > maxDist) {
                left++;
            }
            count += right - left; // All pairs between left and right have distance <= maxDist
        }

        return count;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(N \log N + N \log(\max(\text{nums}) - \min(\text{nums})))$ — Sorting takes $O(N \log N)$; binary search range takes $O(N)$ sliding window checks.
- **Space Complexity:** $O(1)$ constant space (or $O(\log N)$ sorting stack space).

---

### 2. Median of Two Sorted Arrays — <a href="https://leetcode.com/problems/median-of-two-sorted-arrays/" target="_blank">LeetCode 4</a> [SELECTIVE / ADVANCED]

> [!NOTE]
> **Selective/Advanced Status**: LC 4 is included as a selective stretch problem due to its multi-array partition invariant mechanics.

#### Problem Statement
Given two sorted arrays `nums1` and `nums2` of size `m` and `n` respectively, return the median of the two sorted arrays. The overall run time complexity should be $O(\log(m+n))$.

#### Partition Invariant & Binary Search Strategy
- Binary search on the partition cut in `nums1` (ensuring `nums1` is the smaller array so range is $[0, m]$).
- Partition combined arrays into Left Half and Right Half of equal size $(m + n + 1) / 2$:
  - `partition1` elements from `nums1`, `partition2 = (m + n + 1) / 2 - partition1` elements from `nums2`.
- **Partition Correctness Rule**:
  $$\text{maxLeft1} \le \text{minRight2} \quad \text{and} \quad \text{maxLeft2} \le \text{minRight1}$$

#### Java Implementation
```java
public class MedianOfTwoSortedArrays {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        // Ensure nums1 is the smaller array
        if (nums1.length > nums2.length) {
            return findMedianSortedArrays(nums2, nums1);
        }

        int m = nums1.length;
        int n = nums2.length;
        int low = 0, high = m;

        while (low <= high) {
            int partition1 = low + (high - low) / 2;
            int partition2 = (m + n + 1) / 2 - partition1;

            int maxLeft1 = (partition1 == 0) ? Integer.MIN_VALUE : nums1[partition1 - 1];
            int minRight1 = (partition1 == m) ? Integer.MAX_VALUE : nums1[partition1];

            int maxLeft2 = (partition2 == 0) ? Integer.MIN_VALUE : nums2[partition2 - 1];
            int minRight2 = (partition2 == n) ? Integer.MAX_VALUE : nums2[partition2];

            if (maxLeft1 <= minRight2 && maxLeft2 <= minRight1) {
                // Correct partition found
                if ((m + n) % 2 == 0) {
                    return (Math.max(maxLeft1, maxLeft2) + Math.min(minRight1, minRight2)) / 2.0;
                } else {
                    return Math.max(maxLeft1, maxLeft2);
                }
            } else if (maxLeft1 > minRight2) {
                high = partition1 - 1; // Cut too far right in nums1; move left
            } else {
                low = partition1 + 1;  // Cut too far left in nums1; move right
            }
        }

        throw new IllegalArgumentException("Input arrays are not sorted.");
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(\log(\min(M, N)))$ — Binary search over smaller array length.
- **Space Complexity:** $O(1)$ constant space.

---

## Pattern Summary

| Problem | Selection Target | Binary Search Domain | Predicate / Invariant Mechanism |
| :--- | :--- | :--- | :--- |
| **LC 719 (Kth Distance)** [CORE] | $K$-th smallest pair distance | $[0, \text{max\_diff}]$ | Sliding Window pair count $\sum(R - L) \ge k$ |
| **LC 4 (Median 2 Arrays)** [SELECTIVE] | Median value across 2 arrays | $[0, \min(M, N)]$ | Partition cut invariant: $\text{maxL1} \le \text{minR2}$ |
