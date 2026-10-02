# Pattern 7: Kadane's Algorithm

> At every index, make a local dynamic decision: **Extend the current contiguous subarray sum OR discard it and start fresh.**

---

## 🔍 Beginner Audit: What Is Kadane's Algorithm?

Kadane's Algorithm solves the **Maximum Subarray Sum** problem in $O(N)$ time and $O(1)$ auxiliary space.

- **Brute Force ($O(N^2)$):** Evaluate all $\frac{N(N+1)}{2}$ contiguous subarray pairs $(i, j)$ and sum them.
- **Kadane's Optimization ($O(N)$):** Iterate through the array once while maintaining a running local state (`currentSum`) and a global optimum (`maxSum`).

> **1-Line Decision Rule:**
> $$\text{currentSum} = \max(\text{nums}[i], \text{currentSum} + \text{nums}[i])$$

---

## Core Technical Intuition — Local Extend vs. Restart Decision

At index `i`, you face a simple choice:
1. **Extend:** Add `nums[i]` to `currentSum` if `currentSum` was positive and helpful.
2. **Restart:** Discard `currentSum` and start a new contiguous subarray beginning at `nums[i]` if `currentSum` was negative and dragging down the total.

```text
Array:   [ -2,   1,  -3,   4,  -1,   2,   1,  -5,   4 ]
            ▲    ▲
          Reset  Start Fresh
          (-2)   from 1
```

```text
At index i:
        Is currentSum + nums[i] > nums[i] ?
               │
       ┌───────┴───────┐
      YES             NO
       │               │
  Extend Subarray   Start Fresh from nums[i]
```

---

## Local vs. Global State

- **Local State (`currentSum`):** Stores the optimal contiguous subarray sum **ending strictly at index `i`**.
- **Global State (`maxSum`):** Stores the maximum contiguous subarray sum found **anywhere in `nums[0...i]`**.

> **Key Rule:** The global optimum is updated continuously: `maxSum = max(maxSum, currentSum)`.

---

## ☕ Standard Java Code Templates

### 1. Basic Kadane's Algorithm (Max Subarray Sum)
```java
public int maxSubArray(int[] nums) {
    int currentSum = nums[0];
    int maxSum = nums[0]; // Avoid initializing to 0 to handle all-negative arrays!
    
    for (int i = 1; i < nums.length; i++) {
        // Decision: Extend previous subarray sum OR start fresh from nums[i]
        currentSum = Math.max(nums[i], currentSum + nums[i]);
        maxSum = Math.max(maxSum, currentSum);
    }
    
    return maxSum;
}
```

### 2. Kadane with Index Reconstruction (Tracking Subarray Range)
```java
public int[] maxSubArrayWithRange(int[] nums) {
    int currentSum = nums[0], maxSum = nums[0];
    int bestStart = 0, bestEnd = 0, candidateStart = 0;
    
    for (int i = 1; i < nums.length; i++) {
        if (currentSum < 0) {
            currentSum = nums[i]; // Start fresh
            candidateStart = i;
        } else {
            currentSum += nums[i]; // Extend
        }
        
        if (currentSum > maxSum) {
            maxSum = currentSum;
            bestStart = candidateStart;
            bestEnd = i;
        }
    }
    
    return new int[]{ maxSum, bestStart, bestEnd };
}
```

### 3. Maximum Product Subarray State Extension
```java
public int maxProduct(int[] nums) {
    int currentMax = nums[0], currentMin = nums[0], globalMax = nums[0];
    
    for (int i = 1; i < nums.length; i++) {
        int num = nums[i];
        if (num < 0) {
            // Negative number flips min and max!
            int temp = currentMax;
            currentMax = currentMin;
            currentMin = temp;
        }
        
        currentMax = Math.max(num, currentMax * num);
        currentMin = Math.min(num, currentMin * num);
        globalMax = Math.max(globalMax, currentMax);
    }
    
    return globalMax;
}
```

---

## 🔍 Step-by-Step Trace Table (Dry Run)

Tracing **Basic Kadane** on `nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]`:

Initial State: `currentSum = -2`, `maxSum = -2`

| Step / Index | `nums[i]` | Choice: `Math.max(nums[i], currentSum + nums[i])` | New `currentSum` | `maxSum` Updated | Active Subarray Range |
|--------------|-----------|---------------------------------------------------|------------------|------------------|-----------------------|
| **`i = 1`** | `1` | `Math.max(1, -2 + 1)` $\rightarrow$ **1** | `1` | `1` | `[1]` |
| **`i = 2`** | `-3` | `Math.max(-3, 1 + -3)` $\rightarrow$ **-2** | `-2` | `1` | `[1, -3]` |
| **`i = 3`** | `4` | `Math.max(4, -2 + 4)` $\rightarrow$ **4** | `4` | `4` | `[4]` |
| **`i = 4`** | `-1` | `Math.max(-1, 4 + -1)` $\rightarrow$ **3** | `3` | `4` | `[4, -1]` |
| **`i = 5`** | `2` | `Math.max(2, 3 + 2)` $\rightarrow$ **5** | `5` | `5` | `[4, -1, 2]` |
| **`i = 6`** | `1` | `Math.max(1, 5 + 1)` $\rightarrow$ **6** | `6` | **`6`** | `[4, -1, 2, 1]` |
| **`i = 7`** | `-5` | `Math.max(-5, 6 + -5)` $\rightarrow$ **1** | `1` | `6` | `[4, -1, 2, 1, -5]` |
| **`i = 8`** | `4` | `Math.max(4, 1 + 4)` $\rightarrow$ **5** | `5` | `6` | `[4, -1, 2, 1, -5, 4]` |

**Result:** `maxSum = 6` (Optimal Subarray: `[4, -1, 2, 1]`).

---

## 🚨 Edge Case Warning: All-Negative Arrays

> **Common Bug:** Initializing `maxSum = 0` or `currentSum = 0`.  
> For `nums = [-3, -1, -2]`, initializing to zero incorrectly outputs `0` instead of `-1`!  
> **Rule:** Always initialize `currentSum` and `maxSum` to `nums[0]`.

---

## 💡 State Extensions: Product & Circular Subarrays

### 1. Maximum Product Subarray (LeetCode 152)
Multiplying by a negative number swaps the local maximum and local minimum. Therefore, local state must maintain **both** `currentMax` and `currentMin` simultaneously.

### 2. Circular Maximum Subarray (LeetCode 918)
Subarrays can wrap around array ends. The optimal circular sum is:
$$\text{maxCircular} = \max(\text{maxSubarraySum}, \text{totalSum} - \text{minSubarraySum})$$
*Edge Case:* If all numbers are negative, `totalSum - minSubarraySum == 0`. Guard this case by returning `maxSubarraySum`.

---

## Pattern Recognition Layer

When facing an unfamiliar problem, walk through this mental checklist:

```text
What exactly am I optimizing?
        ↓
Is the answer contiguous?
        ↓
Can I define the best state ending at position i?
        ↓
What information from i - 1 matters?
        ↓
Should I extend or restart?
        ↓
Do I need more than one state?
        ↓
How do I maintain the global optimum?
```

---

## 🔀 Kadane vs. Prefix Sum / Sliding Window / Min-Max Tracking

| Pattern | Core Focus | Optimization Mechanism |
|---------|------------|------------------------|
| **Kadane's Algorithm** | Optimal contiguous subsegment decision | Local state transition (`extend` vs `restart`). |
| **Prefix Sum** | Cumulative range queries | Precomputed cumulative array subtractions ($O(1)$). |
| **Sliding Window** | Moving contiguous subsegment bounds | Monotonic expand/shrink on window state. |
| **Min/Max Tracking** | Element-level numerical extremes | Global tracking of individual element extrema. |

---

## 🔗 Kadane and Dynamic Programming (1D DP)

Kadane's Algorithm is fundamentally a **space-optimized 1D Dynamic Programming** technique.

Standard DP recurrence:
$$dp[i] = \max(\text{nums}[i], dp[i-1] + \text{nums}[i])$$

Because $dp[i]$ depends *only* on the immediately preceding value $dp[i-1]$, space reduces from $O(N)$ array memory to $O(1)$ scalar variables (`currentSum`).

---

## 📊 Difficulty Distribution

| Depth Tier | Count |
|------------|-------|
| **Foundation** | 3 |
| **Core** | 4 |
| **Advanced** | 3 |
| **Interview Recognition (Pattern Hidden)** | 2 |
| **Total** | **12** |

---

## 🎯 Question Progression (Curated 12-Question Set)

### 1. Foundation (3 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 1 | Maximum Subarray | <a href="https://leetcode.com/problems/maximum-subarray/" target="_blank">LeetCode 53</a> | Medium | Core Kadane's algorithm (local extend vs. restart decision). |
| 2 | Maximum Absolute Sum of Any Subarray | <a href="https://leetcode.com/problems/maximum-absolute-sum-of-any-subarray/" target="_blank">LeetCode 1749</a> | Medium | Dual Kadane state tracking (max subarray sum & min subarray sum). |
| 3 | Find the Substring With Maximum Cost | <a href="https://leetcode.com/problems/find-the-substring-with-maximum-cost/" target="_blank">LeetCode 2606</a> | Medium | Range reconstruction & mapping string values to integer costs before Kadane. |

---

### 2. Core (4 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 4 | Maximum Product Subarray | <a href="https://leetcode.com/problems/maximum-product-subarray/" target="_blank">LeetCode 152</a> | Medium | Extended local state tracking (`maxProd` & `minProd` sign swaps). |
| 5 | Maximum Sum Circular Subarray | <a href="https://leetcode.com/problems/maximum-sum-circular-subarray/" target="_blank">LeetCode 918</a> | Medium | Circular wrap-around via `max(normalMax, totalSum - minSubarraySum)` with all-negative handling. |
| 6 | Maximum Subarray Sum with One Deletion | <a href="https://leetcode.com/problems/maximum-subarray-sum-with-one-deletion/" target="_blank">LeetCode 1186</a> | Medium | State expansion (`noDeletion` vs `oneDeletion` state transitions). |
| 7 | Maximum Subarray Sum After One Operation | <a href="https://leetcode.com/problems/maximum-subarray-sum-after-one-operation/" target="_blank">LeetCode 1746</a> | Medium | State expansion (`noSquare` vs `oneSquare` state transitions). |

---

### 3. Advanced (3 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 8 | K-Concatenation Maximum Sum | <a href="https://leetcode.com/problems/k-concatenation-maximum-sum/" target="_blank">LeetCode 1191</a> | Medium | Combining Kadane with array repetition & prefix/suffix sum properties ($K=1, K=2, K>2$). |
| 9 | Maximum Score Of Spliced Array | <a href="https://leetcode.com/problems/maximum-score-of-spliced-array/" target="_blank">LeetCode 2321</a> | Medium | Kadane on element difference array `(B[i] - A[i])` for optimal swap gain. |
| 10 | Number of Sub-arrays With Odd Sum | <a href="https://leetcode.com/problems/number-of-sub-arrays-with-odd-sum/" target="_blank">LeetCode 1524</a> | Medium | State tracking of even and odd prefix parity counts. |

---

## 🕵️ Interview Recognition — Pattern Hidden

The following problems test your ability to recognize local state transition and optimal subsegment accumulation mechanics independently without explicit pattern labels. Analyze the requirements and derive the state recurrence from scratch.

| # | Problem | LeetCode Link | Difficulty | Objective |
|---|---------|---------------|------------|-----------|
| 11 | Best Sightseeing Pair | <a href="https://leetcode.com/problems/best-sightseeing-pair/" target="_blank">LeetCode 1014</a> | Medium | Maximize `values[i] + values[j] + i - j` for $i < j$ efficiently. |
| 12 | Maximum Erasure Value | <a href="https://leetcode.com/problems/maximum-erasure-value/" target="_blank">LeetCode 1695</a> | Medium | Find maximum sum of a contiguous subarray containing all unique elements. |

<details>
<summary>💡 Reveal Pattern Hints (Click after attempting from a blank editor)</summary>

- **Problem 11:** Rewrite `values[i] + values[j] + i - j` as `(values[i] + i) + (values[j] - j)`. Maintain running local maximum of `(values[i] + i)` as you traverse $j$ from left to right.
- **Problem 12:** Combine contiguous window state with a set/map to track unique elements while accumulating local contiguous subarray sums.
</details>

---

## 🏆 Mastery Criteria

You have mastered **Pattern #7** when you can:

- [ ] Implement basic Kadane's algorithm cleanly from a blank editor.
- [ ] Explain the local extend vs. restart decision clearly (`Math.max(num, currentSum + num)`).
- [ ] Handle all-negative arrays correctly by initializing `currentSum = nums[0]`.
- [ ] Reconstruct the exact start and end indices of the maximum subarray.
- [ ] Adapt Kadane's state to track both maximum and minimum values (Maximum Product Subarray).
- [ ] Solve Circular Maximum Subarray problems including all-negative edge cases.
- [ ] Recognize Kadane as a space-optimized 1D Dynamic Programming recurrence.
- [ ] Distinguish Kadane from Prefix Sum, Sliding Window, and Min-Max Tracking.
- [ ] Implement solutions cleanly from a blank editor.
- [ ] Solve an unfamiliar Kadane-style variation without being told the intended pattern.

---

## ➡️ Next Step

Once Kadane's Algorithm mechanics feel natural, move to **[Pattern 08: Matrix / 2D Array Traversal](./Pattern-08-Matrix-2D-Array-Traversal.md)** to learn how to navigate 2D grid coordinates, boundaries, and matrix transformations!
