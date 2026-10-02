# Pattern 4: Prefix Sum

> Precompute cumulative totals so range queries and subarray conditions can be computed in $O(1)$ time instead of recalculating from scratch.

---

## 🔍 Beginner Audit: Why Subtract Two Cumulative Totals?

Suppose you need to calculate the sum of elements between index $L$ and index $R$ repeatedly across multiple queries:

- ❌ **Brute Force ($O(N)$ per query):** Loop from index $L$ to $R$ and sum each element individually.
- ✅ **Prefix Sum ($O(1)$ per query):** Take the precomputed cumulative sum up to $R$, and subtract the cumulative sum before $L$ ($L-1$).

$$\text{Subarray Sum}(L \dots R) = \text{prefix}[R] - \text{prefix}[L - 1]$$

Using zero-indexed offset arrays (`prefix[i]` stores the sum of first `i` elements):

$$\text{Subarray Sum}(L \dots R) = \text{prefix}[R + 1] - \text{prefix}[L]$$

---

## Core Technical Intuition — Range Sum Subtraction

By precomputing running totals once in **$O(N)$ time**, any contiguous subarray sum query reduces to a single subtraction step in **$O(1)$ time**.

Core mental model:

> **"Can I represent the cumulative state at each index, and can two states tell me something about the range between them?"**

```text
Input Array:    nums   = [ 3,   4,  -2,   1,   3 ]
Prefix Array:   prefix = [ 0,  3,   7,   5,   6,   9 ]
                           ▲                 ▲
                       prefix[1]         prefix[4]

Subarray Sum(1...3) = prefix[4] - prefix[1] = 6 - 3 = 3  (4 + -2 + 1 = 3)
```

---

## ☕ Standard Java Code Templates

### 1. Basic 1D Prefix Sum Construction & Range Query
```java
// Precomputation - O(N) time, O(N) space
int[] prefix = new int[nums.length + 1];
for (int i = 0; i < nums.length; i++) {
    prefix[i + 1] = prefix[i] + nums[i];
}

// Range sum query for subarray nums[L...R] in O(1) time:
public int rangeSum(int L, int R) {
    return prefix[R + 1] - prefix[L];
}
```

### 2. Prefix Sum + HashMap Template (Subarray Sum Equals K)
```java
public int subarraySum(int[] nums, int k) {
    Map<Integer, Integer> map = new HashMap<>();
    map.put(0, 1); // Base case: prefix sum 0 occurs once before index 0
    
    int currSum = 0, count = 0;
    for (int num : nums) {
        currSum += num;
        
        // If (currSum - k) exists in map, a subarray summing to k ends here!
        if (map.containsKey(currSum - k)) {
            count += map.get(currSum - k);
        }
        
        map.put(currSum, map.getOrDefault(currSum, 0) + 1);
    }
    return count;
}
```

---

## 🔍 Step-by-Step Trace Table (Dry Run)

Tracing **Subarray Sum Equals K (`LeetCode 560`)** with `nums = [3, 4, -2, 1, 3]`, target `k = 5`:

Initial State: `map = {0: 1}`, `currSum = 0`, `count = 0`

| Step / Index | `num` | `currSum` | Target Prefix (`currSum - k`) | Found in `map`? | Updated `count` | `map` State after step |
|--------------|-------|-----------|-------------------------------|-----------------|-----------------|------------------------|
| **`i = 0`** | `3` | `3` | $3 - 5 = -2$ | No | `0` | `{0:1, 3:1}` |
| **`i = 1`** | `4` | `7` | $7 - 5 = 2$ | No | `0` | `{0:1, 3:1, 7:1}` |
| **`i = 2`** | `-2` | `5` | $5 - 5 = 0$ | **YES** (`0` count=1) | `0 + 1 = 1` *(Subarray [3,4,-2])* | `{0:1, 3:1, 7:1, 5:1}` |
| **`i = 3`** | `1` | `6` | $6 - 5 = 1$ | No | `1` | `{0:1, 3:1, 7:1, 5:1, 6:1}` |
| **`i = 4`** | `3` | `9` | $9 - 5 = 4$ | No | `1` | `{0:1, 3:1, 7:1, 5:1, 6:1, 9:1}` |

---

## 💡 Conceptual Depth Progression

```text
Basic cumulative state
        ↓
Range queries
        ↓
Left/right cumulative reasoning
        ↓
Subarray = relationship between prefix states
        ↓
Prefix Sum + HashMap
        ↓
Prefix Sum + frequency
        ↓
Prefix Sum + modulo
        ↓
2D Prefix Sum
        ↓
Combined / disguised problems
        ↓
UNLABELED RECOGNITION
```

---

## 🛑 Important Pattern Boundaries

Prefix Sum is NOT simply: *"Create an array called `prefix`."*

The underlying algorithmic idea is: **Store cumulative information so that repeated range/cumulative calculations become cheap.**

| Problem Statement / Requirement | Correct Primary Pattern | Why Prefix Sum Is NOT the Primary Optimization |
|---------------------------------|-------------------------|------------------------------------------------|
| **Maximum Subarray Sum** | **Kadane's Algorithm** | Requires dynamic max tracking over local restart decisions. |
| **Maximum Sum of Fixed-Size K Subarray** | **Sliding Window** | Window moves incrementally; 1 element enters and 1 leaves. |
| **Two Sum (Find 2 elements with sum K)** | **Two Pointers / HashMap** | Operates on pairs of elements, not contiguous ranges. |
| **Basic Single-Pass Running State** | **Basic Traversal** | Cumulative range subtraction is not required. |

---

## 📊 Difficulty Distribution

| Depth Tier | Count |
|------------|-------|
| **Foundation** | 3 |
| **Core** | 5 |
| **Advanced** | 2 |
| **Interview Recognition (Pattern Hidden)** | 2 |
| **Total** | **12** |

---

## 🎯 Question Progression (Curated 12-Question Set)

### 1. Foundation (3 Questions)

*Note: LeetCode 1480 (Running Sum of 1D Array) is excluded here as it was already covered in Pattern #1 as a basic state traversal.*

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 1 | Range Sum Query - Immutable | <a href="https://leetcode.com/problems/range-sum-query-immutable/" target="_blank">LeetCode 303</a> | Easy | Precompute prefix sums for repeated range queries. |
| 2 | Find Pivot Index | <a href="https://leetcode.com/problems/find-pivot-index/" target="_blank">LeetCode 724</a> | Easy | Left sum vs right sum. |
| 3 | Find the Highest Altitude | <a href="https://leetcode.com/problems/find-the-highest-altitude/" target="_blank">LeetCode 1732</a> | Easy | Running cumulative state + global result. |

---

### 2. Core (5 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 4 | Subarray Sum Equals K | <a href="https://leetcode.com/problems/subarray-sum-equals-k/" target="_blank">LeetCode 560</a> | Medium | Prefix Sum + HashMap (Major emphasis). |
| 5 | Contiguous Array | <a href="https://leetcode.com/problems/contiguous-array/" target="_blank">LeetCode 525</a> | Medium | Transforming a balance condition into prefix-state equality + HashMap. |
| 6 | Subarray Sums Divisible by K | <a href="https://leetcode.com/problems/subarray-sums-divisible-by-k/" target="_blank">LeetCode 974</a> | Medium | Prefix Sum + remainder/frequency reasoning. |
| 7 | Binary Subarrays With Sum | <a href="https://leetcode.com/problems/binary-subarrays-with-sum/" target="_blank">LeetCode 930</a> | Medium | Prefix Sum + frequency counting. |
| 8 | Continuous Subarray Sum | <a href="https://leetcode.com/problems/continuous-subarray-sum/" target="_blank">LeetCode 523</a> | Medium | Prefix Sum + modulo remainder tracking. |

---

### 3. Advanced / Combined (2 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 9 | Range Sum Query 2D - Immutable | <a href="https://leetcode.com/problems/range-sum-query-2d-immutable/" target="_blank">LeetCode 304</a> | Medium | 2D Prefix Sum. |
| 10 | Number of Submatrices That Sum to Target | <a href="https://leetcode.com/problems/number-of-submatrices-that-sum-to-target/" target="_blank">LeetCode 1074</a> | Hard | 2D Prefix Sum + HashMap / dimensional reduction. *(Selected Hard problem for genuine 2D depth)*. |

---

## 🕵️ Interview Recognition — Pattern Hidden

The following problems test your ability to recognize cumulative relationships independently without explicit pattern labels. Analyze the requirements, derive the repeated state, and choose your optimization strategy.

| # | Problem | LeetCode Link | Difficulty | Objective |
|---|---------|---------------|------------|-----------|
| 11 | Maximum Points You Can Obtain from Cards | <a href="https://leetcode.com/problems/maximum-points-you-can-obtain-from-cards/" target="_blank">LeetCode 1423</a> | Medium | Find optimal selection from array endpoints. |
| 12 | Make Sum Divisible by P | <a href="https://leetcode.com/problems/make-sum-divisible-by-p/" target="_blank">LeetCode 1590</a> | Medium | Find shortest subarray to remove so remaining sum is divisible by $P$. |

<details>
<summary>💡 Reveal Pattern Hints (Click after attempting from a blank editor)</summary>

- **Problem 11:** Selecting $K$ cards from array ends is equivalent to leaving a contiguous subarray of size $N - K$ with minimum sum.
- **Problem 12:** Requires finding the shortest subarray with prefix remainder matching `totalSum % P`.
</details>

---

## 🏆 Mastery Criteria

You have mastered **Pattern #4** when you can:

- [ ] Build and use prefix sums correctly in $O(N)$ time.
- [ ] Answer repeated range queries efficiently in $O(1)$ time.
- [ ] Recognize left/right cumulative relationships (`leftSum == totalSum - leftSum - curr`).
- [ ] Convert subarray-sum problems into prefix-state relationships ($\text{prefix}[j] - \text{prefix}[i] = K$).
- [ ] Combine Prefix Sum with HashMap to count or locate target subarrays in $O(N)$.
- [ ] Use prefix remainders / modulo arithmetic appropriately when handling divisibility conditions.
- [ ] Recognize when 2D Prefix Sum is useful for matrix subgrid queries.
- [ ] Handle negative values, zeros, and off-by-one boundary indexing carefully.

---

## ➡️ Next Step

Once Prefix Sum feels natural, move to **[Pattern 05: Two Pointers](./Pattern-05-Two-Pointers.md)** to learn how to navigate sorted arrays or binary search spaces using dual index pointers!
