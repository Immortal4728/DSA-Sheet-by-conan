# Searching: Search Space Reduction

> Stop checking every element. If the data has structure, order, or a monotonic property, you can systematically eliminate half the remaining possibilities at every step.

---

## 🔍 Beginner Audit: Linear vs. Binary Search

- **Linear Search:** Scanning elements one-by-one from left to right. ($O(N)$). You must use this when data is completely unsorted and has no exploitable structure.
- **Binary Search:** Comparing the middle element to a target and eliminating half the remaining search space. ($O(\log N)$). Used when data is sorted or a condition is monotonic (always transitions from `false` to `true` at a specific point).

> **Key Intuition:** Binary Search is not just for sorted arrays. It is for any problem where asking a Yes/No question allows you to definitively rule out half of the search space.

---

## Core Technical Intuition — Eliminating Halves

```text
Target = 7

Initial Space:   [ 1,  3,  5,  7,  9, 11, 15 ]
                   ↑           ↑           ↑
                  low         mid         high

Step 1: mid = 7. Target == 7. Found!

What if Target = 10?
Step 1: mid = 7. 7 < 10.
        Eliminate left half (including mid).
        New Space: [ 9, 11, 15 ]
                     ↑   ↑   ↑
                    low mid high
Step 2: mid = 11. 11 > 10.
        Eliminate right half.
        New Space: [ 9 ] -> Target not found.
```

---

## ☕ Standard Java Code Templates

### Template 1: Basic Binary Search (Exact Match)
```java
// Use when: finding the exact index of a target in a sorted array
public int binarySearch(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2; // Prevents integer overflow
        if (nums[mid] == target) return mid;
        else if (nums[mid] < target) left = mid + 1; // Search right half
        else right = mid - 1; // Search left half
    }
    return -1;
}
```

### Template 2: Boundary Searching (First/Last Occurrence)
```java
// Use when: finding the FIRST occurrence of a target (or insertion point)
public int findFirst(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    int result = -1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] >= target) {
            if (nums[mid] == target) result = mid; // Record, but keep searching left
            right = mid - 1; 
        } else {
            left = mid + 1;
        }
    }
    return result;
}
```

### Template 3: Binary Search on Answer (Monotonic Function)
```java
// Use when: "What is the minimum capacity/speed to achieve X?"
public int minEatingSpeed(int[] piles, int h) {
    int left = 1, right = maxElement(piles);
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (canFinish(piles, mid, h)) {
            right = mid; // Mid is possible, but can we do slower?
        } else {
            left = mid + 1; // Mid is too slow, must eat faster
        }
    }
    return left; // Returns the exact boundary where false becomes true
}
```

---

## 🔍 Step-by-Step Trace Table (Dry Run)

Tracing **Search Insert Position (`LeetCode 35`)** on `nums = [1, 3, 5, 6]`, `target = 2`:

| Step | `left` | `right` | `mid` | `nums[mid]` | Compare to Target (2) | Action |
|------|--------|---------|-------|-------------|-----------------------|--------|
| 1 | 0 | 3 | 1 | `3` | `3 > 2` (too big) | `right = mid - 1 = 0` |
| 2 | 0 | 0 | 0 | `1` | `1 < 2` (too small)| `left = mid + 1 = 1` |
| End | 1 | 0 | — | — | `left > right` (Stop) | Return `left` (**1**) |

**Result:** Insert at index `1`.

---

## Pattern Recognition Layer

```text
Is the data completely unsorted with no constraints?
        ↓ Yes → Linear Search O(N)

Is the array sorted?
        ↓ Yes → Binary Search O(log N)

Am I looking for the first or last occurrence of a duplicate element?
        ↓ Yes → Boundary Binary Search (Template 2)

Is the array sorted but rotated?
        ↓ Yes → Rotated Binary Search (find sorted half first)

Am I asked to find a "minimum capacity", "maximum distance", or "minimum time"?
        ↓ Yes → Binary Search on Answer (Template 3)
```

---

## Core Mental Models

- **The Overflow Trap:** *"Never use `(left + right) / 2`. Always use `left + (right - left) / 2` to prevent integer overflow when bounds are large."*
- **Left is the Insertion Point:** *"When a standard binary search loop (`left <= right`) terminates without finding the target, `left` always points to the exact index where the target *should* be inserted."*
- **The Monotonic Boundary:** *"For Binary Search on Answer, you are searching a virtual array of booleans: `[F, F, F, T, T, T]`. You are finding the first `T`."*

---

## 🛑 Pattern Boundaries

**Searching vs Array Patterns:**
- If the problem asks: **sorted structure + find an element / boundary / feasible answer** → Consider **Searching**.
- If the problem asks: **contiguous subarray** → Consider **Sliding Window / Prefix Sum / Kadane** (depending on the state).

---

## 📊 Difficulty Distribution

| Depth Tier | Count |
|------------|-------|
| **Foundation** | 3 |
| **Core** | 4 |
| **Advanced** | 4 |
| **Interview Recognition** | 3 |
| **Total** | **14** |

---

## 🎯 Question Progression (Curated 14-Question Set)

### 1. Foundation (3 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 1 | Binary Search | <a href="https://leetcode.com/problems/binary-search/" target="_blank">LeetCode 704</a> | Easy | Standard exact-match binary search on a sorted array. Halving the search space. |
| 2 | Search Insert Position | <a href="https://leetcode.com/problems/search-insert-position/" target="_blank">LeetCode 35</a> | Easy | Insertion boundary: understand that `left` holds the insertion point when the loop exits. |
| 3 | Find First and Last Position of Element in Sorted Array | <a href="https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/" target="_blank">LeetCode 34</a> | Medium | Boundary searching: run binary search twice to find the lower and upper bounds of duplicates. |

---

### 2. Core (4 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 4 | Valid Perfect Square | <a href="https://leetcode.com/problems/valid-perfect-square/" target="_blank">LeetCode 367</a> | Easy | Search space reasoning: binary search over the virtual sorted array of numbers from 1 to `num`. |
| 5 | Find Smallest Letter Greater Than Target | <a href="https://leetcode.com/problems/find-smallest-letter-greater-than-target/" target="_blank">LeetCode 744</a> | Easy | Upper-bound style reasoning with a circular wraparound edge case. |
| 6 | Search in Rotated Sorted Array | <a href="https://leetcode.com/problems/search-in-rotated-sorted-array/" target="_blank">LeetCode 33</a> | Medium | Rotated search: identify which half is strictly sorted, and eliminate the impossible half. |
| 7 | Find Minimum in Rotated Sorted Array | <a href="https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/" target="_blank">LeetCode 153</a> | Medium | Rotated search: locate the rotation pivot using the monotonic structure by comparing `mid` to `right`. |

---

### 3. Advanced (4 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 8 | Find Peak Element | <a href="https://leetcode.com/problems/find-peak-element/" target="_blank">LeetCode 162</a> | Medium | Gradient search: using local slopes to walk towards a peak without knowing its exact location. |
| 9 | Search a 2D Matrix | <a href="https://leetcode.com/problems/search-a-2d-matrix/" target="_blank">LeetCode 74</a> | Medium | 2D structure: map a conceptual 1D search space into 2D coordinates `row = mid / cols`, `col = mid % cols`. |
| 10 | Koko Eating Bananas | <a href="https://leetcode.com/problems/koko-eating-bananas/" target="_blank">LeetCode 875</a> | Medium | Binary Search on Answer: define a monotonic `canFinish()` function to find the minimum feasible speed. |
| 11 | Capacity To Ship Packages Within D Days | <a href="https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/" target="_blank">LeetCode 1011</a> | Medium | Binary Search on Answer: find the minimum feasible capacity by recognizing the monotonic answer space. |

---

### 4. Interview Recognition — Pattern Hidden (3 Questions)

| # | Problem | LeetCode Link | Difficulty | Objective |
|---|---------|---------------|------------|-----------|
| 12 | Split Array Largest Sum | <a href="https://leetcode.com/problems/split-array-largest-sum/" target="_blank">LeetCode 410</a> | Hard | Minimize the maximum sum among `m` subarrays. |
| 13 | Magnetic Force Between Two Balls | <a href="https://leetcode.com/problems/magnetic-force-between-two-balls/" target="_blank">LeetCode 1552</a> | Medium | Maximize the minimum magnetic force between any two balls. |
| 14 | Find the Smallest Divisor Given a Threshold | <a href="https://leetcode.com/problems/find-the-smallest-divisor-given-a-threshold/" target="_blank">LeetCode 1283</a> | Medium | Find the smallest divisor such that the result sum is less than or equal to a threshold. |

<details>
<summary>💡 Reveal Pattern Hints (Click after attempting from a blank editor)</summary>

- **Problem 12:** "Minimize the maximum" is the classic signature of **Binary Search on Answer**. The search space is `[max(nums) ... sum(nums)]`. For a given capacity `mid`, count how many subarrays are needed. If `count > m`, capacity is too small (`left = mid + 1`). 
- **Problem 13:** "Maximize the minimum" is another strong indicator. The search space for the distance is `[1 ... max(position) - min(position)]`. Write a greedy feasibility check to see if you can place all balls with at least `mid` distance between them.
- **Problem 14:** Monotonic feasibility: As the divisor increases, the sum decreases. Search the divisor space `[1 ... max(nums)]` to find the smallest one that satisfies the threshold condition.
</details>

---

## 🏆 Mastery Criteria

You have mastered the **Searching** curriculum when you can:

- [ ] Write a bug-free exact-match Binary Search using `left + (right - left) / 2` from memory.
- [ ] Explain why binary search requires a usable ordering/monotonic structure.
- [ ] Implement lower-bound / upper-bound style searches.
- [ ] Handle duplicates correctly.
- [ ] Search rotated sorted arrays confidently.
- [ ] Identify monotonic feasibility and write a correct `canFinish()` feasibility function.
- [ ] Recognize both "minimize the maximum" and "maximize the minimum" structures.
- [ ] Understand binary search on an answer space completely.
- [ ] Explain the time and space complexity of these algorithms.
- [ ] Recognize when binary search is NOT appropriate.
- [ ] Implement all 14 solutions from a blank editor without tutorial dependence.

---

## ➡️ Next Step

Once Searching mechanics are automatic, move to **[Sorting](../Sorting/Sorting-Problems.md)** to learn how forcing order onto chaotic data unlocks new algorithmic structures.
