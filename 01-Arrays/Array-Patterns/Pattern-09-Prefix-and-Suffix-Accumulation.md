# Pattern 9: Prefix & Suffix Accumulation

Precompute accumulated state from the left side (prefix) and/or right side (suffix) of an array so that each position $i$ can query its left and right context in $O(1)$ time, eliminating repeated scans and optimizing total execution time from $O(N^2)$ to $O(N)$.

---

## 📌 Core Idea & Mental Model

In many array problems, evaluating the answer at index $i$ requires knowledge about all elements before $i$ and all elements after $i$. Scanning left and right repeatedly for every index results in an inefficient $O(N^2)$ algorithm.

By precomputing left-to-right state and right-to-left state into auxiliary structures or maintaining them during array passes, we isolate the answer derivation at index $i$ to a simple $O(1)$ lookup:

```text
left information (prefix state)
               ↓
           current i
               ↑
right information (suffix state)
```

### The Transformation Workflow

```text
Repeated scanning over left/right subsegments
                    ↓
Precompute prefix and suffix state arrays
                    ↓
Combine states at index i in O(1) time
                    ↓
Optimized O(N) linear-time execution
```

---

## ⚡ Prefix Sum vs. Prefix & Suffix Accumulation

It is crucial to distinguish this pattern from standard Prefix Sum (Pattern 4):

| Dimension | Prefix Sum (Pattern 4) | Prefix & Suffix Accumulation (Pattern 9) |
|---|---|---|
| **Primary Operation** | Strictly cumulative addition (`+`). | Any binary aggregate: Product (`*`), Min (`min`), Max (`max`), Count, Boolean state, or Index mapping. |
| **Directionality** | Single pass from left to right. | Dual passes: Left-to-Right (Prefix) AND Right-to-Left (Suffix). |
| **Core Query Target** | Subarray sum over range `[L, R]`. | Local position answer at index `i` depending on elements before `i` and after `i`. |
| **Output Goal** | Range sum queries in $O(1)$ or subarray matching via Hash Maps. | State combination per index without mutating surrounding elements. |

---

## 🚫 Pattern Boundaries

Do not apply Prefix & Suffix Accumulation merely because a solution uses an array pass. Classify by the dominant algorithmic structure:

- **Prefix Sum ($\rightarrow$ Pattern 4):** Use when the problem strictly requires range sum queries or zero-sum subarray lookups.
- **HashMap + Prefix Sum ($\rightarrow$ Pattern 3 / Pattern 4):** Use when matching running prefix totals with frequency tables.
- **Two Pointers ($\rightarrow$ Pattern 5):** Use when searching for pairs/triplets or shrinking boundaries on sorted arrays without precalculating full directional state arrays.
- **Sliding Window ($\rightarrow$ Pattern 6):** Use when maintaining contiguous subarray properties over dynamic boundaries.
- **Kadane's Algorithm ($\rightarrow$ Pattern 7):** Use when finding maximum/minimum contiguous subarray sums in a single pass using continuous state reset.
- **Matrix Traversal ($\rightarrow$ Pattern 8):** Use when operating on 2D grid coordinates and directional vectors.

---

## 💾 Space Optimization: $O(N) \rightarrow O(1)$ Auxiliary Space

While storing both `prefixState[]` and `suffixState[]` arrays requires $O(N)$ extra space, many problems allow space optimization down to $O(1)$ auxiliary space (excluding the output array):

1. **Pass 1 (Left to Right):** Compute and populate the prefix state directly inside the `result[]` array.
2. **Pass 2 (Right to Left):** Maintain a single running variable (`rightState`) while traversing backward, multiplying or combining it directly into `result[i]`.

This reduces memory footprint while preserving linear time complexity.

---

## ☕ Standard Java Implementation Templates

### Template 1: Explicit Dual Auxiliary Arrays ($O(N)$ Auxiliary Space)

Used when combining complex left and right bounds (e.g., Left Max and Right Max).

```java
public int[] evaluateDualState(int[] nums) {
    int n = nums.length;
    int[] prefixState = new int[n];
    int[] suffixState = new int[n];
    
    // 1. Build Prefix State (Left to Right)
    prefixState[0] = nums[0];
    for (int i = 1; i < n; i++) {
        prefixState[i] = Math.max(prefixState[i - 1], nums[i]);
    }
    
    // 2. Build Suffix State (Right to Left)
    suffixState[n - 1] = nums[n - 1];
    for (int i = n - 2; i >= 0; i--) {
        suffixState[i] = Math.max(suffixState[i + 1], nums[i]);
    }
    
    // 3. Combine States at each index i
    int[] result = new int[n];
    for (int i = 0; i < n; i++) {
        result[i] = Math.min(prefixState[i], suffixState[i]) - nums[i];
    }
    
    return result;
}
```

### Template 2: Space-Optimized Output Accumulation ($O(1)$ Auxiliary Space)

Used when one direction can be accumulated on-the-fly during a reverse traversal.

```java
public int[] productExceptSelf(int[] nums) {
    int n = nums.length;
    int[] result = new int[n];
    
    // 1. Pass 1: Compute Prefix Products (Left side) into output array
    result[0] = 1;
    for (int i = 1; i < n; i++) {
        result[i] = result[i - 1] * nums[i - 1];
    }
    
    // 2. Pass 2: Accumulate Suffix Products on-the-fly from right to left
    int rightAccumulator = 1;
    for (int i = n - 1; i >= 0; i--) {
        result[i] = result[i] * rightAccumulator;
        rightAccumulator *= nums[i];
    }
    
    return result;
}
```

---

## 🔍 Execution Trace Table: Product of Array Except Self

Tracing `nums = [1, 2, 3, 4]`:

| Index `i` | `nums[i]` | Pass 1: `result[i]` (Prefix Product) | Pass 2: `rightAccumulator` | Pass 2: Final `result[i]` |
|---|---|---|---|---|
| **0** | `1` | `1` | `4 * 3 * 2 = 24` | `1 * 24 = 24` |
| **1** | `2` | `1` | `4 * 3 = 12` | `1 * 12 = 12` |
| **2** | `3` | `1 * 2 = 2` | `4` | `2 * 4 = 8` |
| **3** | `4` | `1 * 2 * 3 = 6` | `1` | `6 * 1 = 6` |

**Final Output:** `[24, 12, 8, 6]`.

---

## 🏋️ Curated 10-Question Progression

```text
Foundation (2) ──► Core (4) ──► Advanced (2) ──► Interview Recognition (2)
```

---

### Foundation (2 Questions)

Establish the core mechanics of tracking left-side state and right-side state across an array.

#### 1. Minimum Sum of Mountain Triplets II
- **LeetCode Link:** <a href="https://leetcode.com/problems/minimum-sum-of-mountain-triplets-ii/" target="_blank">LeetCode 2909 — Minimum Sum of Mountain Triplets II</a>
- **Difficulty:** Medium
- **Core Concept:** For each peak position $i$, find the minimum element strictly to its left and strictly to its right. Precomputing `leftMin[]` and `rightMin[]` allows evaluating all mountain triplets $(i, j, k)$ in $O(N)$ time.

#### 2. Partition Array into Disjoint Intervals
- **LeetCode Link:** <a href="https://leetcode.com/problems/partition-array-into-disjoint-intervals/" target="_blank">LeetCode 915 — Partition Array into Disjoint Intervals</a>
- **Difficulty:** Medium
- **Core Concept:** Partition an array into two contiguous subarrays `left` and `right` such that every element in `left` is $\le$ every element in `right`. Precompute `prefixMax[]` and `suffixMin[]` to identify the smallest valid split index where `prefixMax[i] <= suffixMin[i + 1]`.

---

### Core (4 Questions)

Cover product accumulation, left/right maximum tracking, streak optimization, and non-trivial state combinations.

#### 3. Product of Array Except Self
- **LeetCode Link:** <a href="https://leetcode.com/problems/product-of-array-except-self/" target="_blank">LeetCode 238 — Product of Array Except Self</a>
- **Difficulty:** Medium
- **Core Concept:** Compute the product of all elements except `nums[i]` without using division. Combine prefix products of elements before $i$ with suffix products of elements after $i$. Optimize auxiliary space to $O(1)$ using the output array.

#### 4. Trapping Rain Water
- **LeetCode Link:** <a href="https://leetcode.com/problems/trapping-rain-water/" target="_blank">LeetCode 42 — Trapping Rain Water</a>
- **Difficulty:** Hard
- **Core Concept:** Water trapped at index $i$ is bounded by $\min(\text{maxLeft}[i], \text{maxRight}[i]) - \text{height}[i]$. Precompute `leftMax[]` and `rightMax[]` arrays to eliminate repeated outward boundary scans.

#### 5. Find Good Days to Rob the Bank
- **LeetCode Link:** <a href="https://leetcode.com/problems/find-good-days-to-rob-the-bank/" target="_blank">LeetCode 2100 — Find Good Days to Rob the Bank</a>
- **Difficulty:** Medium
- **Core Concept:** Precompute non-increasing streak lengths from left-to-right (`nonIncreasing[i]`) and non-decreasing streak lengths from right-to-left (`nonDecreasing[i]`). A day $i$ is valid if both streaks are $\ge time$.

#### 6. Number of Good Ways to Split a String
- **LeetCode Link:** <a href="https://leetcode.com/problems/number-of-good-ways-to-split-a-string/" target="_blank">LeetCode 1525 — Number of Good Ways to Split a String</a>
- **Difficulty:** Medium
- **Core Concept:** State combination using distinct element counts. Precompute the number of unique characters in prefix `s[0..i]` and suffix `s[i+1..n-1]` to count valid split indices in $O(N)$ time.

---

### Advanced (2 Questions)

Solve multi-state precomputation, range query combinations, and space optimization challenges.

#### 7. Plates Between Candles
- **LeetCode Link:** <a href="https://leetcode.com/problems/plates-between-candles/" target="_blank">LeetCode 2055 — Plates Between Candles</a>
- **Difficulty:** Medium
- **Core Concept:** Precompute three states: prefix count of plates, nearest candle index to the right (`nearestRightCandle[]`), and nearest candle index to the left (`nearestLeftCandle[]`). Combine directional index maps with prefix sums to answer subsegment queries in $O(1)$ time per query.

#### 8. Minimum Average Difference
- **LeetCode Link:** <a href="https://leetcode.com/problems/minimum-average-difference/" target="_blank">LeetCode 2256 — Minimum Average Difference</a>
- **Difficulty:** Medium
- **Core Concept:** Calculate absolute difference between the average of the first $i + 1$ elements and the average of the remaining elements. Use total sum precomputation to maintain running prefix sum and derive suffix sum in $O(1)$ space without auxiliary arrays.

---

### Interview Recognition (2 Unlabeled Questions)

> **Challenge:** These problems do not explicitly mention directional accumulators or precomputation. You must analyze the repeated work in brute force, identify left/right position dependence, and derive the linear-time precomputation strategy.

#### 9. Customer Penalty Minimization
- **LeetCode Link:** <a href="https://leetcode.com/problems/minimum-penalty-for-a-shop/" target="_blank">LeetCode 2483 — Minimum Penalty for a Shop</a>
- **Difficulty:** Medium
- **Problem Statement:** A shop logs customer activity as `'Y'` (customers present) or `'N'` (no customers). Closing at hour $j$ incurs a penalty equal to the count of `'N'` during open hours ($0$ to $j-1$) plus the count of `'Y'` during closed hours ($j$ to $N$). Find the earliest closing hour that minimizes penalty.
- **Brute Force Analysis:** Checking penalty for each closing hour $j$ requires scanning left for `'N'` and right for `'Y'`, yielding $O(N^2)$ time.
- **Deriving Linear Time:** Every index $j$ requires left count of `'N'` and right count of `'Y'`. Precompute left `'N'` counts and right `'Y'` counts, or compute total `'Y'` up front to track both values in a single pass.
- **Revealed Classification:** Dual-directional count state accumulation.

#### 10. Array Partitioning into Sorted Chunks
- **LeetCode Link:** <a href="https://leetcode.com/problems/max-chunks-to-make-sorted/" target="_blank">LeetCode 769 — Max Chunks To Make Sorted</a>
- **Difficulty:** Medium
- **Problem Statement:** Given an array `arr` of length $N$ representing a permutation of integers from $0$ to $N-1$, split the array into the maximum number of contiguous chunks such that sorting each chunk individually produces a fully sorted array.
- **Brute Force Analysis:** Validating whether a chunk boundary at index $i$ allows correct sorting requires verifying that all elements to the left are $\le$ elements to the right, which takes $O(N^2)$ time if checked naively.
- **Deriving Linear Time:** A split after index $i$ is valid if the maximum element encountered from $0$ to $i$ equals $i$. By tracking the accumulated prefix maximum, chunk boundaries can be identified in $O(N)$ time.
- **Revealed Classification:** Prefix maximum boundary tracking.

---

## 🎯 Mastery Criteria

Before moving forward, confirm you can:

1. **Define the Pattern:** Explain how precomputing directional state eliminates repeated inner array scans.
2. **Identify Left/Right Dependency:** Recognize when evaluating index $i$ depends on aggregate properties of elements before $i$ and after $i$.
3. **Select Appropriate Accumulation Operations:** Implement prefix/suffix products, minimums, maximums, streak lengths, and frequency counts.
4. **Optimize Auxiliary Space:** Transition from explicit $O(N)$ `prefix[]` / `suffix[]` arrays to $O(1)$ space using output arrays or running accumulators.
5. **Distinguish from Prefix Sum:** Contrast position-based left/right state combination with standard subarray sum range queries.
6. **Solve Unlabeled Problems:** Identify hidden prefix/suffix dependencies in complex interview scenarios without explicit hint keywords.

---

## 🏁 Arrays Section Completion & Transition

Congratulations on completing **Pattern 9: Prefix & Suffix Accumulation**! This completes the core Array Patterns series.

You have mastered fundamental array mechanics, state tracking, windowing, partitioning, matrix manipulation, and directional precomputation. Next, apply these foundational state-tracking principles to logarithmic search structures in **02-Searching**.
