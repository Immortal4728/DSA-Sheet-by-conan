# Pattern 2: Min/Max Tracking

> Maintain extreme values (largest, smallest, or top $K$ elements) dynamically while traversing data, avoiding unnecessary full-array sorting or rescanning.

---

## 🔍 Beginner Audit: Why Avoid `Arrays.sort()` for Extremes?

Sorting an array to find extreme values introduces unnecessary computational overhead:

```java
Arrays.sort(nums); // O(N log N) time complexity
return nums[nums.length - 1];
```

> **Performance Trade-off:** Full array sorting requires **$O(N \log N)$ time**. If your objective is strictly to find extreme elements, updating state variables during a single linear traversal reduces time complexity to **$O(N)$** with **$O(1)$ auxiliary space**.

---

## Core Technical Intuition — Dynamic Extreme Accumulation

Instead of retaining the entire historical list of values, maintain minimal state variables (`maxSoFar`, `minPrice`, `topTwo`) representing the extremes observed up to the current index `i`.

```text
State Variable:  maxVal = nums[0]

Iteration i=1:   Compare nums[1] with maxVal  ──► Update maxVal if nums[1] > maxVal
Iteration i=2:   Compare nums[2] with maxVal  ──► Update maxVal if nums[2] > maxVal
```

At index `i`, the state variable `maxVal` contains the maximum element in `nums[0...i]`.

---

## ☕ Standard Java Code Templates

### 1. Single Running Extreme (Max / Min)
```java
public int findMax(int[] nums) {
    int maxVal = nums[0]; // Initialize with first element
    
    for (int i = 1; i < nums.length; i++) {
        if (nums[i] > maxVal) {
            maxVal = nums[i]; // Update current maximum
        }
    }
    
    return maxVal;
}
```

### 2. Tracking Top-2 Extremes Without Sorting
```java
public int maxProduct(int[] nums) {
    int firstMax = 0, secondMax = 0;
    
    for (int num : nums) {
        if (num > firstMax) {
            secondMax = firstMax; // Shift previous maximum to second
            firstMax = num;       // Assign new global maximum
        } else if (num > secondMax) {
            secondMax = num;      // Update second maximum
        }
    }
    
    return (firstMax - 1) * (secondMax - 1);
}
```

---

## 🔍 Step-by-Step Trace Table (Dry Run)

Tracing **Best Time to Buy & Sell Stock (`LeetCode 121`)** with `prices = [7, 1, 5, 3, 6, 4]`:

Initial State: `minPrice = 7`, `maxProfit = 0`

| Step / Index | Price (`price`) | Lowest Price Seen (`minPrice`) | Potential Profit (`price - minPrice`) | Best Profit (`maxProfit`) |
|--------------|-----------------|--------------------------------|----------------------------------------|---------------------------|
| **`i = 0`** | `7` | `7` | $7 - 7 = 0$ | `0` |
| **`i = 1`** | `1` | `1` | $1 - 1 = 0$ | `0` |
| **`i = 2`** | `5` | `1` | $5 - 1 = 4$ | `4` |
| **`i = 3`** | `3` | `1` | $3 - 1 = 2$ | `4` |
| **`i = 4`** | `6` | `1` | $6 - 1 = 5$ | `5` |
| **`i = 5`** | `4` | `1` | $4 - 1 = 3$ | `5` |

---

## 💡 Core Mental Model

> **"What minimal information about previous elements is sufficient to make the optimal choice for the current element?"**

Rather than storing all past elements, determine what minimal state (e.g., `minSoFar`, `maxRight`, `topTwo`) is required to process `nums[i]`.

---

## 🛑 Pattern Boundaries (What Exists Elsewhere)

Do not assume a problem belongs to Min/Max Tracking solely because the word *"maximum"* or *"minimum"* appears in the title:

| Problem Type / Concept | Proper Pattern | Reason Min/Max Tracking Is Insufficient |
|------------------------|----------------|-----------------------------------------|
| **Majority Element** | Frequency Counting / Boyer-Moore Voting | Depends on frequency count, not numerical value. |
| **Maximum Subarray** | Kadane's Algorithm | Requires contiguous sum reset decisions at each index. |
| **Maximum Product Subarray** | Dynamic Programming / Kadane State | Requires tracking both min and max due to negative sign flips. |
| **Maximum Swap** | Greedy Algorithm | Requires position-indexed digit mapping. |
| **Stock Trading with Transaction Fee** | Dynamic Programming | State transitions depend on hold/sell states across multiple days. |
| **Sliding Window Maximum** | Sliding Window + Deque | Requires dynamic maximum over a moving window of size $K$. |
| **Minimum Size Subarray Sum** | Sliding Window / Two Pointers | Requires expanding/contracting contiguous window bounds. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 4 |
| **Medium** | 2 |
| **Hard** | 0 |
| **Total** | **6** |

---

## 🎯 Question Progression (Curated 6-Question Set)

### 1. Foundation

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 1 | Replace Elements with Greatest Element on Right Side | <a href="https://leetcode.com/problems/replace-elements-with-greatest-element-on-right-side/" target="_blank">LeetCode 1299</a> | Easy | Right-to-left traversal + running maximum. |
| 2 | Third Maximum Number | <a href="https://leetcode.com/problems/third-maximum-number/" target="_blank">LeetCode 414</a> | Easy | Tracking multiple distinct extrema without sorting. |
| 3 | Best Time to Buy and Sell Stock | <a href="https://leetcode.com/problems/best-time-to-buy-and-sell-stock/" target="_blank">LeetCode 121</a> | Easy | Running minimum + global best. |
| 4 | Maximum Product Difference Between Two Pairs | <a href="https://leetcode.com/problems/maximum-product-difference-between-two-pairs/" target="_blank">LeetCode 1913</a> | Easy | Tracking two minima and two maxima simultaneously. |

---

### 2. Core Variation

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 5 | Largest Number At Least Twice of Others | <a href="https://leetcode.com/problems/largest-number-at-least-twice-of-others/" target="_blank">LeetCode 747</a> | Medium | Largest + second-largest tracking. |
| 6 | Maximum Product of Two Elements in an Array | <a href="https://leetcode.com/problems/maximum-product-of-two-elements-in-an-array/" target="_blank">LeetCode 1464</a> | Medium | Tracking the top two values. |

---

## 🏆 Mastery Criteria

A learner has mastered **Pattern #2** when they can:

- [ ] Track a running minimum or maximum in a single $O(N)$ pass.
- [ ] Track top-2 or top-3 extreme values without sorting or extra space.
- [ ] Identify the minimum state required to make dynamic decisions.
- [ ] Distinguish genuine Min/Max Tracking from problems where "maximum" is merely the final objective (e.g., Kadane, Sliding Window).
- [ ] Handle negative numbers, duplicates, and initial values (`Integer.MIN_VALUE`) safely.

---

## ➡️ Next Step

Once Min/Max Tracking feels natural, move to **[Pattern 03: Frequency Counting](./Pattern-03-Frequency-Counting.md)** to learn how to trade space for $O(1)$ lookup speed when tracking occurrences and frequencies.
