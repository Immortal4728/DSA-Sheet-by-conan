# Pattern 01 — Boundary & Search Invariants

Binary search is often taught as finding an exact target in a sorted array. However, in technical interviews, binary search is most frequently used to find **boundaries**: the first element satisfying a condition, the last element satisfying a condition, or the exact transition point where a boolean predicate flips from `false` to `true`.

---

## Core Concept & Loop Invariants

To avoid off-by-one errors and infinite loops, every binary search must maintain a strict **Loop Invariant**.

### Template 1: First True / Left Boundary (`[low, high]`)

```java
int low = 0, high = n - 1;
int ans = -1; // Or default boundary

while (low <= high) {
    int mid = low + (high - low) / 2; // Overflow-safe midpoint
    if (isFeasible(mid)) {
        ans = mid;        // Candidate found; record and search left half
        high = mid - 1;
    } else {
        low = mid + 1;    // Search right half
    }
}
return ans;
```

### Key Rules for Boundary Binary Search
1. **Overflow-Safe Midpoint**: Use `mid = low + (high - low) / 2` instead of `(low + high) / 2` to prevent 32-bit integer overflow.
2. **Search Space Shrinking**: Every iteration MUST reduce the search range `high - low`.
3. **Cross-Section Foundations**: Section 01 owns standard exact-match and boundary foundations (<a href="https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/" target="_blank">LeetCode 34</a>, <a href="https://leetcode.com/problems/search-in-rotated-sorted-array/" target="_blank">LeetCode 33</a>, <a href="https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/" target="_blank">LeetCode 153</a>, <a href="https://leetcode.com/problems/find-peak-element/" target="_blank">LeetCode 162</a>). Pattern 01 builds on these to formalize monotonic boundary reasoning.

---

## Pattern Questions (2 Canonical)

### 1. First Bad Version — <a href="https://leetcode.com/problems/first-bad-version/" target="_blank">LeetCode 278</a>

#### Problem Statement
You are a product manager and currently leading a team to develop a new product. Since the latest version fails the quality check, all the versions after a bad version are also bad. Suppose you have `n` versions `[1, 2, ..., n]` and you want to find out the first bad one. You are given an API `bool isBadVersion(version)`.

#### Key Insight & Monotonic Transition
The version validity forms a monotonic boolean sequence:
`[false, false, false, true, true, true]`
We need to find the **First True** boundary (the smallest index `i` such that `isBadVersion(i) == true`).

#### Java Implementation
```java
/* The isBadVersion API is defined in the parent class.
   boolean isBadVersion(int version); */

public class FirstBadVersion extends VersionControl {
    public int firstBadVersion(int n) {
        int low = 1, high = n;
        int firstBad = n;

        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (isBadVersion(mid)) {
                firstBad = mid;   // Potential answer; search left for earlier bad version
                high = mid - 1;
            } else {
                low = mid + 1;    // mid is good; search right
            }
        }

        return firstBad;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(\log N)$ — Halves the search space in each step.
- **Space Complexity:** $O(1)$ — Constant iterative variables.

---

### 2. H-Index II — <a href="https://leetcode.com/problems/h-index-ii/" target="_blank">LeetCode 275</a>

#### Problem Statement
Given an array of citations **sorted in ascending order**, return the researcher's h-index. The h-index is defined as the maximum value $h$ such that the researcher has published at least $h$ papers that have each been cited at least $h$ times.

#### Key Insight & Monotonic Index Invariant
For a paper at index `mid` in a sorted citations array of length `N`:
- The number of papers with citation count $\ge \text{citations}[mid]$ is $N - mid$.
- If $\text{citations}[mid] \ge N - mid$, then $h = N - mid$ is a valid candidate h-index, and we can check if a larger h-index exists by searching further left (smaller `mid`).
- If $\text{citations}[mid] < N - mid$, the citation count is too small; we must search right (larger `mid`).

```text
Citations:  [0,  1,  3,  5,  6]   (N = 5)
Index:       0   1   2   3   4
N - mid:     5   4   3   2   1
Valid?      No  No  Yes Yes Yes   -> First True at index 2 (H-Index = 5 - 2 = 3)
```

#### Java Implementation
```java
public class HIndexII {
    public int hIndex(int[] citations) {
        int n = citations.length;
        int low = 0, high = n - 1;
        int hIndex = 0;

        while (low <= high) {
            int mid = low + (high - low) / 2;
            int count = n - mid; // Number of papers with >= citations[mid] citations

            if (citations[mid] >= count) {
                hIndex = count;   // Valid h-index candidate; try searching left for a larger h-index
                high = mid - 1;
            } else {
                low = mid + 1;    // Citations too small; search right
            }
        }

        return hIndex;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(\log N)$ — Binary search over array length $N$.
- **Space Complexity:** $O(1)$ — Auxiliary variables only.

---

## Pattern Summary

| Problem | Target Boundary Type | Monotonic Predicate `isFeasible(mid)` | Boundary Update |
| :--- | :--- | :--- | :--- |
| **LC 278 (First Bad Version)** | First `true` in `[F, F, T, T]` | `isBadVersion(mid)` | `high = mid - 1` |
| **LC 275 (H-Index II)** | First `mid` where `cit[mid] >= N - mid` | `citations[mid] >= N - mid` | `high = mid - 1` |
