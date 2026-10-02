# Pattern 6: Sliding Window

> Maintain a dynamic, contiguous active range `[left ... right]` across an array or string to update state incrementally in $O(1)$ time per step instead of recomputing subarrays in $O(N^2)$.

---

## 🔍 Beginner Audit: What Is a "Sliding Window"?

A sliding window maintains state over a **contiguous range of elements** specified by two pointers (`left` and `right`).

```text
Step 1:   [ nums[0]  nums[1]  nums[2] ]  nums[3]  nums[4]
          Window range: [0 ... 2]  ──► State = sum(nums[0...2])

As the window slides right by 1 index:

Step 2:     nums[0]  [ nums[1]  nums[2]  nums[3] ]  nums[4]
          Window range: [1 ... 3]  ──► New State = Old State - nums[0] + nums[3]
```

> **Key Performance Advantage:** Instead of re-summing all elements inside the window ($O(K)$ time), you perform **1 subtraction (outgoing element `nums[left]`) and 1 addition (incoming element `nums[right]`) in $O(1)$ time.**

---

## Core Technical Intuition — Monotonic Window Expansion & Contraction

Sliding Window relies on **monotonicity**—expanding `right` adds to the window state, while shrinking `left` reduces the window state.

```text
expand right
      ↓
condition violated?
      ↓
shrink left
      ↓
restore validity
      ↓
record answer
```

---

## Window Variants & State Management

### 1. Fixed-Size Windows (Window length $K$ is constant)
The distance `right - left + 1 == K` remains fixed. Once the window reaches size $K$, both `left` and `right` advance together.

### 2. Variable-Size Windows — Longest Valid Subarray
Expand `right` to include elements. When a condition is violated (e.g., sum exceeds limit or distinct count exceeds $K$), shrink `left` until validity is restored. Record `maxLen = max(maxLen, right - left + 1)`.

### 3. Variable-Size Windows — Shortest Valid Subarray
Expand `right` until the condition is met. Once valid, shrink `left` repeatedly while maintaining validity to find the minimum length. Record `minLen = min(minLen, right - left + 1)`.

### 4. Frequency & HashMap Windows
A HashMap or frequency array (`int[26]` / `int[128]`) maintains element or character counts *specifically inside the active window*.

### 5. At-Most K to Exactly K Transformation
Counting subarrays with **EXACTLY $K$** items is difficult directly because shrinking `left` loses valid starting bounds. Mathematically transform the count:

$$\text{Exactly}(K) = \text{AtMost}(K) - \text{AtMost}(K - 1)$$

---

## ☕ Standard Java Code Templates

### Template 1: Fixed-Size Window (Length = $K$)
```java
public double findMaxAverage(int[] nums, int k) {
    int windowSum = 0;
    
    // 1. Build initial window of size K
    for (int i = 0; i < k; i++) {
        windowSum += nums[i];
    }
    
    int maxSum = windowSum;
    
    // 2. Slide the window across remaining array
    for (int right = k; right < nums.length; right++) {
        windowSum += nums[right];       // Add incoming element on right
        windowSum -= nums[right - k];   // Subtract outgoing element on left
        maxSum = Math.max(maxSum, windowSum);
    }
    
    return (double) maxSum / k;
}
```

### Template 2: Variable-Size Window — Longest Valid Subarray
```java
public int longestSubarray(int[] nums, int k) {
    int left = 0, currentSum = 0, maxLength = 0;
    
    for (int right = 0; right < nums.length; right++) {
        currentSum += nums[right]; // 1. Expand right
        
        // 2. Shrink left if condition is violated
        while (currentSum > k) {
            currentSum -= nums[left];
            left++;
        }
        
        // 3. Record answer
        maxLength = Math.max(maxLength, right - left + 1);
    }
    
    return maxLength;
}
```

### Template 3: Variable-Size Window — Shortest Valid Subarray
```java
public int minSubArrayLen(int target, int[] nums) {
    int left = 0, currentSum = 0, minLength = Integer.MAX_VALUE;
    
    for (int right = 0; right < nums.length; right++) {
        currentSum += nums[right]; // Expand right
        
        // Shrink left repeatedly while condition remains VALID
        while (currentSum >= target) {
            minLength = Math.min(minLength, right - left + 1);
            currentSum -= nums[left];
            left++;
        }
    }
    
    return minLength == Integer.MAX_VALUE ? 0 : minLength;
}
```

---

## 🔍 Step-by-Step Trace Table (Dry Run)

Tracing **Template 3 (Minimum Size Subarray Sum $\ge 7$)** on `nums = [2, 3, 1, 2, 4, 3]`:

| Step (`right`) | `nums[right]` | `currentSum` | Valid? (`currentSum >= 7`) | Active Window `[left...right]` | `minLength` |
|----------------|---------------|--------------|----------------------------|--------------------------------|-------------|
| **`right = 0`** | `2` | `2` | No | `[2]` | $\infty$ |
| **`right = 1`** | `3` | `5` | No | `[2, 3]` | $\infty$ |
| **`right = 2`** | `1` | `6` | No | `[2, 3, 1]` | $\infty$ |
| **`right = 3`** | `2` | `8` | **YES** | `[2, 3, 1, 2]` (len=4). Shrink `left`: remove 2 $\rightarrow$ sum=6 | `4` |
| **`right = 4`** | `4` | `10` | **YES** | `[3, 1, 2, 4]` (len=4). Shrink `left`: remove 3 $\rightarrow$ sum=7. **YES**! Shrink `left`: remove 1 $\rightarrow$ sum=6 | `3` |
| **`right = 5`** | `3` | `9` | **YES** | `[2, 4, 3]` (len=3). Shrink `left`: remove 2 $\rightarrow$ sum=7. **YES**! Window `[4, 3]` (len=2). Shrink `left`: remove 4 $\rightarrow$ sum=3 | **`2`** |

**Result:** `minLength = 2` (Subarray `[4, 3]`).

---

## Pattern Recognition Layer

When facing an unfamiliar problem, walk through this mental checklist:

```text
Is the target a contiguous subarray / substring?
        ↓
Can I maintain information about the current range?
        ↓
Can I expand one boundary (right)?
        ↓
When the condition becomes invalid, can I shrink the other boundary (left)?
        ↓
Can elements enter/leave without recomputing everything?
        ↓
Can the window move monotonically?
```

---

## 🔀 Two Pointers vs. Sliding Window

While both patterns utilize two indices (`left` and `right`), their underlying optimizations are distinct:

> **Two Pointers:** Manipulates two indices (often starting at opposite ends of a sorted array) to systematically eliminate pairs or perform in-place partitioning without tracking intermediate range state.

> **Sliding Window:** Specifically maintains a **contiguous active range `[left ... right]`** and tracks accumulated state (sum, frequencies, distinct counts) *inside that active window*.

---

## 🚨 When Sliding Window Does NOT Apply

Sliding Window relies strictly on **Monotonicity**. If adding an element does not consistently increase/modify state predictably, the window logic breaks:

| Problem Constraint | Can I Use Sliding Window? | Why / What to Use Instead |
|--------------------|---------------------------|---------------------------|
| Subarray Sum = $K$ with **positive numbers only** | ✅ **YES** | Expanding `right` strictly increases sum; shrinking `left` strictly decreases sum. |
| Subarray Sum = $K$ with **negative numbers present** | ❌ **NO!** | Adding negative values can decrease the sum, breaking monotonic expansion/shrink decisions. Use **Prefix Sum + HashMap**! |
| Non-contiguous subsequence targets | ❌ **NO!** | Sliding Window requires **contiguous** subarrays or substrings. |

---

## 📊 Difficulty Distribution

| Depth Tier | Count |
|------------|-------|
| **Foundation** | 3 |
| **Core** | 5 |
| **Advanced / Combined** | 5 |
| **Interview Recognition (Pattern Hidden)** | 3 |
| **Total** | **16** |

---

## 🎯 Question Progression (Curated 16-Question Set)

### 1. Foundation (3 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 1 | Maximum Average Subarray I | <a href="https://leetcode.com/problems/maximum-average-subarray-i/" target="_blank">LeetCode 643</a> | Easy | Fixed-size sliding window sum. |
| 2 | Minimum Size Subarray Sum | <a href="https://leetcode.com/problems/minimum-size-subarray-sum/" target="_blank">LeetCode 209</a> | Medium | Variable-size sliding window (shortest valid window). |
| 3 | Longest Substring Without Repeating Characters | <a href="https://leetcode.com/problems/longest-substring-without-repeating-characters/" target="_blank">LeetCode 3</a> | Medium | Variable-size window with HashSet/HashMap. |

---

### 2. Core (5 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 4 | Max Consecutive Ones III | <a href="https://leetcode.com/problems/max-consecutive-ones-iii/" target="_blank">LeetCode 1004</a> | Medium | Variable-size window allowing at most $K$ zero flips. |
| 5 | Longest Repeating Character Replacement | <a href="https://leetcode.com/problems/longest-repeating-character-replacement/" target="_blank">LeetCode 424</a> | Medium | Variable-size window tracking max character frequency. |
| 6 | Permutation in String | <a href="https://leetcode.com/problems/permutation-in-string/" target="_blank">LeetCode 567</a> | Medium | Fixed-size window character frequency matching. |
| 7 | Find All Anagrams in a String | <a href="https://leetcode.com/problems/find-all-anagrams-in-a-string/" target="_blank">LeetCode 438</a> | Medium | Fixed-size window frequency map matching. |
| 8 | Fruit Into Baskets | <a href="https://leetcode.com/problems/fruit-into-baskets/" target="_blank">LeetCode 904</a> | Medium | Variable-size window with at most 2 distinct elements. |

---

### 3. Advanced / Combined (5 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 9 | Maximum Points You Can Obtain from Cards | <a href="https://leetcode.com/problems/maximum-points-you-can-obtain-from-cards/" target="_blank">LeetCode 1423</a> | Medium | Fixed-size sliding window for inverse sum optimization. |
| 10 | Subarray Product Less Than K | <a href="https://leetcode.com/problems/subarray-product-less-than-k/" target="_blank">LeetCode 713</a> | Medium | Variable-size window counting valid contiguous subarrays (`right - left + 1`). |
| 11 | Count Number of Nice Subarrays | <a href="https://leetcode.com/problems/count-number-of-nice-subarrays/" target="_blank">LeetCode 1248</a> | Medium | At-most $K$ transformation (`atMost(K) - atMost(K - 1)`). |
| 12 | Frequency of the Most Frequent Element | <a href="https://leetcode.com/problems/frequency-of-the-most-frequent-element/" target="_blank">LeetCode 1838</a> | Medium | Sorting + variable-size window maintaining increment cost. |
| 13 | Minimum Window Substring | <a href="https://leetcode.com/problems/minimum-window-substring/" target="_blank">LeetCode 76</a> | Hard | Variable-size window with required character frequency matching. *(Selected Hard problem for genuine minimum window depth)*. |

---

## 🕵️ Interview Recognition — Pattern Hidden

The following problems test your ability to recognize contiguous window maintenance and variable expansion/contraction mechanics independently without explicit pattern labels. Analyze the requirements and derive the window invariants from scratch.

| # | Problem | LeetCode Link | Difficulty | Objective |
|---|---------|---------------|------------|-----------|
| 14 | Grumpy Bookstore Owner | <a href="https://leetcode.com/problems/grumpy-bookstore-owner/" target="_blank">LeetCode 1052</a> | Medium | Find maximum satisfied customers using a fixed secret window. |
| 15 | Maximum Number of Vowels in a Substring of Given Length | <a href="https://leetcode.com/problems/maximum-number-of-vowels-in-a-substring-of-given-length/" target="_blank">LeetCode 1456</a> | Medium | Find max vowels in a fixed window of length $K$. |
| 16 | Minimum Operations to Reduce X to Zero | <a href="https://leetcode.com/problems/minimum-operations-to-reduce-x-to-zero/" target="_blank">LeetCode 1658</a> | Medium | Find maximum length subarray with target sum equal to `totalSum - X`. |

<details>
<summary>💡 Reveal Pattern Hints (Click after attempting from a blank editor)</summary>

- **Problem 14:** Calculate baseline satisfied customers first, then run a fixed-size window of size `minutes` to maximize additional satisfied customers when keeping the owner non-grumpy.
- **Problem 15:** Build a fixed window of size $K$, counting vowels. Slide right, adding 1 if incoming char is a vowel and subtracting 1 if outgoing char was a vowel.
- **Problem 16:** Removing elements from array endpoints to reach sum $X$ is equivalent to finding the **longest contiguous subarray in the middle** with sum equal to `totalSum - X`.
</details>

---

## 🏆 Mastery Criteria

You have mastered **Pattern #6** when you can:

- [ ] Implement fixed-size windows cleanly in $O(N)$ time.
- [ ] Implement variable-size windows (longest and shortest valid variations).
- [ ] Maintain frequency arrays or HashMaps efficiently inside the window state.
- [ ] Determine when it is valid to expand `right` versus shrink `left`.
- [ ] Apply the $\text{Exactly}(K) = \text{atMost}(K) - \text{atMost}(K-1)$ transformation when counting exact subarray occurrences.
- [ ] Handle distinct-element constraints and string character windows.
- [ ] Explain why Sliding Window fails when negative numbers break sum monotonicity.
- [ ] Distinguish Sliding Window from Two Pointers and Prefix Sum.
- [ ] Implement solutions cleanly from a blank editor.
- [ ] Recognize Sliding Window in an unlabeled interview problem.

---

## ➡️ Next Step

Once Sliding Window mechanics feel natural, move to **[Pattern 07: Kadane's Algorithm](./Pattern-07-Kadanes-Algorithm.md)** to learn how to solve maximum contiguous subarray sum problems with local extend vs. restart decisions!
