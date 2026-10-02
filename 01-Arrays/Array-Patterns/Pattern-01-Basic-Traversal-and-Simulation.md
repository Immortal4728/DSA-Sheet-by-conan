# Pattern 1: Basic Traversal & Simulation

> Visit every element in an array sequentially to inspect data, update running state, or execute step-by-step algorithmic rules.

---

## 🔍 Beginner Audit: What Is "Traversal" vs "Simulation"?

- **Traversal:** Iterating through an array sequentially from start to finish (or reverse), inspecting each element `arr[i]` exactly once.
- **Simulation:** Executing step-by-step state transformations or rules defined by the problem statement directly on the array values.

> **Key Intuition:** A single `for` loop is usually sufficient. Avoid writing nested loops when each element can be processed independently or in sequence.

---

## Core Technical Intuition — Single-Pass Array Inspection

In contiguous memory, array elements are laid out sequentially. Accessing elements sequentially using an index `i` allows $O(1)$ element access per step.

```text
Memory Layout:   [ nums[0] ] ──► [ nums[1] ] ──► [ nums[2] ] ──► [ nums[3] ]
Index Pointer:       i = 0           i = 1           i = 2           i = 3
```

By maintaining state variables (such as `currentSum` or `maxSeen`) during iteration, you can compute results in a single **$O(N)$ pass**, eliminating redundant $O(N^2)$ iterations.

---

## ☕ Standard Java Code Templates

### 1. Single-Pass Traversal & State Accumulation
```java
public int[] runningSum(int[] nums) {
    int[] result = new int[nums.length];
    int currentSum = 0;
    
    for (int i = 0; i < nums.length; i++) {
        currentSum += nums[i]; // Accumulate running state
        result[i] = currentSum; // Write to output array
    }
    
    return result;
}
```

### 2. Basic Step-by-Step Simulation
```java
public int[] applyOperations(int[] nums) {
    // Sequential state transformation based on problem rules
    for (int i = 0; i < nums.length - 1; i++) {
        if (nums[i] == nums[i + 1]) {
            nums[i] *= 2;
            nums[i + 1] = 0;
        }
    }
    return nums;
}
```

---

## 🔍 Step-by-Step Trace Table (Dry Run)

Tracing **Running Sum (`LeetCode 1480`)** with `nums = [1, 2, 3, 4]`:

| Step / Index | Current Element (`nums[i]`) | Running Total (`currentSum`) | Output State (`result`) |
|--------------|-----------------------------|------------------------------|-------------------------|
| **`i = 0`** | `1` | $0 + 1 = 1$ | `[1, _, _, _]` |
| **`i = 1`** | `2` | $1 + 2 = 3$ | `[1, 3, _, _]` |
| **`i = 2`** | `3` | $3 + 3 = 6$ | `[1, 3, 6, _]` |
| **`i = 3`** | `4` | $6 + 4 = 10$ | `[1, 3, 6, 10]` |

---

## 💡 How to Identify It (Recognition Checklist)

Consider **Basic Traversal & Simulation** when the problem requires:

- [ ] Inspecting **every element** in an array at least once.
- [ ] Constructing a new array where each element depends on its index or current value.
- [ ] Maintaining a running total, accumulated sum, or state variable from left to right.
- [ ] Executing step-by-step state updates without requiring lookup tables or sorting.

---

## 🛑 Pattern Boundary (When to Move Beyond Traversal)

> If a problem contains exploitable structure—such as sorted order, frequency relationships, range sum subtractions, or dual pointers—basic single-pass traversal may only serve as a brute-force baseline. Recognizing when to transition to a specialized pattern is key to mastery.

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 7 |
| **Medium** | 1 |
| **Hard** | 0 |
| **Total** | **8** |

---

## 🎯 Question Progression (Curated 8-Question Set)

### 1. Foundation

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 1 | Running Sum of 1D Array | <a href="https://leetcode.com/problems/running-sum-of-1d-array/" target="_blank">LeetCode 1480</a> | Easy | Maintain state while traversing. |
| 2 | Concatenation of Array | <a href="https://leetcode.com/problems/concatenation-of-array/" target="_blank">LeetCode 1929</a> | Easy | Traverse and construct an output array. |
| 3 | Build Array from Permutation | <a href="https://leetcode.com/problems/build-array-from-permutation/" target="_blank">LeetCode 1920</a> | Easy | Index-based traversal. |
| 4 | Find Numbers with Even Number of Digits | <a href="https://leetcode.com/problems/find-numbers-with-even-number-of-digits/" target="_blank">LeetCode 1295</a> | Easy | Process each element independently. |

---

### 2. Core Traversal

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 5 | Richest Customer Wealth | <a href="https://leetcode.com/problems/richest-customer-wealth/" target="_blank">LeetCode 1672</a> | Easy | Nested traversal + maintaining a local maximum. |
| 6 | Kids With the Greatest Number of Candies | <a href="https://leetcode.com/problems/kids-with-the-greatest-number-of-candies/" target="_blank">LeetCode 1431</a> | Easy | Traversal + condition based on existing state. |

---

### 3. Simulation

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 7 | Apply Operations to an Array | <a href="https://leetcode.com/problems/apply-operations-to-an-array/" target="_blank">LeetCode 2460</a> | Easy | Sequential state changes + careful simulation. |

---

### 4. Interview Transition

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 8 | Find the Winner of an Array Game | <a href="https://leetcode.com/problems/find-the-winner-of-an-array-game/" target="_blank">LeetCode 1535</a> | Medium | Traversal + persistent state + recognizing when literal simulation is unnecessary. |

---

## 🏆 Mastery Criteria

A learner has mastered **Pattern #1** when they can:

- [ ] Recognize a basic traversal/simulation problem quickly.
- [ ] Determine what state variables need to be maintained during traversal.
- [ ] Implement a clean single-pass $O(N)$ solution from a blank editor.
- [ ] Handle empty arrays, single-element arrays, and edge cases cleanly.
- [ ] Distinguish simple traversal from problems requiring advanced patterns.

---

## ➡️ Next Step

Once basic traversal feels automatic, move to **[Pattern 02: Min/Max Tracking](./Pattern-02-Min-Max-Tracking.md)** to learn how to track running extremes and optimal historical state dynamically.
