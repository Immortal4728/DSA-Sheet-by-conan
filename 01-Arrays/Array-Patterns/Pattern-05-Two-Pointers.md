# Pattern 5: Two Pointers

> Maintain two index variables ("pointers") moving deterministically across an array to systematically eliminate search space and avoid nested loops ($O(N^2) \rightarrow O(N)$).

---

## 🔍 Beginner Audit: What Is a "Pointer" in Java/Array Patterns?

In array patterns, a **"pointer"** is NOT a memory address (like in C/C++). 

> **Definition:** A pointer is simply an **integer variable storing an array index** (such as `int left = 0` or `int right = arr.length - 1`).

---

## Core Technical Intuition — Search Space Elimination

The fundamental power of Two Pointers comes from **monotonically eliminating impossible pairs or ranges** without scanning them explicitly.

```text
Initial Array:    [ 2,  7, 11, 15 ]    Target = 9
                   ▲          ▲
                 left       right
                 (2)        (15)    Sum = 17 > 9  ──► Move right-- (15 is too big with ANY left!)

Step 1:          [ 2,  7, 11, 15 ]
                   ▲      ▲
                 left   right
                 (2)    (11)        Sum = 13 > 9  ──► Move right-- (11 is too big with ANY left!)

Step 2:          [ 2,  7, 11, 15 ]
                   ▲  ▲
                 left right
                 (2)  (7)           Sum = 9 == 9  ──► Match Found! [left, right]
```

### Why Is Moving `right--` Safe on Sorted Arrays?
If `nums[left] + nums[right] > target`, then `nums[right]` paired with *any element larger than `nums[left]`* will also exceed the target. We can safely discard `right` from consideration without checking all intermediate combinations!

---

## Pointer Variants

Two Pointers manifests in four major forms across interview problems:

### 1. Opposite-Direction Pointers (`left` at 0, `right` at N-1)
Pointers start at array boundaries and move inward. Used for sorted pair sums, palindromes, container boundaries, and trapped water logic.

### 2. Same-Direction Pointers / Reader & Writer (`slow` & `fast`)
`fast` reads every element; `slow` holds the insertion position for valid elements. Used for in-place deduplication, filtering, and array compaction.

### 3. Fast & Slow Speed Pointers (Cycle & Ratio Detection)
Pointers move forward at different relative speeds (e.g. 1 step vs 2 steps). Used to detect middle points and cycles (heavily utilized in Linked Lists).

### 4. Partitioning & Dutch National Flag (Multi-Boundary Tracking)
Pointers (`low`, `mid`, `high`) maintain active regions with known properties (e.g. `[0s | 1s | unexamined | 2s]`) to sort/rearrange in a single pass.

---

## ☕ Standard Java Code Templates

### Template 1: Opposite-Direction Pointers (Sorted Two Sum)
```java
public int[] twoSumSorted(int[] nums, int target) {
    int left = 0;
    int right = nums.length - 1;
    
    while (left < right) {
        int currentSum = nums[left] + nums[right];
        
        if (currentSum == target) {
            return new int[]{ left + 1, right + 1 }; // Match found
        } else if (currentSum > target) {
            right--; // Decrease sum by picking smaller right element
        } else {
            left++;  // Increase sum by picking larger left element
        }
    }
    
    return new int[]{-1, -1};
}
```

### Template 2: Same-Direction Reader & Writer (In-Place Filter / Deduplication)
```java
public int removeDuplicates(int[] nums) {
    if (nums.length == 0) return 0;
    
    int slow = 0; // Writer index (position for next unique element)
    
    for (int fast = 1; fast < nums.length; fast++) { // Reader index
        if (nums[fast] != nums[slow]) {
            slow++;
            nums[slow] = nums[fast]; // Overwrite with unique element
        }
    }
    
    return slow + 1; // Length of unique prefix
}
```

### Template 3: Dutch National Flag (3-Way Partitioning)
```java
public void sortColors(int[] nums) {
    int low = 0, mid = 0, high = nums.length - 1;
    
    while (mid <= high) {
        if (nums[mid] == 0) {
            swap(nums, low, mid);
            low++;
            mid++;
        } else if (nums[mid] == 1) {
            mid++;
        } else { // nums[mid] == 2
            swap(nums, mid, high);
            high--; // Do not advance mid yet; inspect swapped element
        }
    }
}
```

---

## 🔍 Step-by-Step Trace Table (Dry Run)

Tracing **Template 2 (Remove Duplicates)** on `nums = [1, 1, 2, 3]`:

Initial State: `slow = 0`, `nums = [1, 1, 2, 3]`

| Step (`fast`) | `nums[fast]` | `nums[slow]` | Condition (`nums[fast] != nums[slow]`) | Action Taken | Array State (`nums`) | `slow` position |
|---------------|--------------|--------------|-----------------------------------------|--------------|----------------------|-----------------|
| **`fast = 1`** | `1` | `1` | `1 != 1` (FALSE) | Duplicate skipped; advance `fast`. | `[1, 1, 2, 3]` | `slow = 0` |
| **`fast = 2`** | `2` | `1` | `2 != 1` (TRUE) | `slow++` (1), write `nums[1] = 2` | `[1, 2, 2, 3]` | `slow = 1` |
| **`fast = 3`** | `3` | `2` | `3 != 2` (TRUE) | `slow++` (2), write `nums[2] = 3` | `[1, 2, 3, 3]` | `slow = 2` |

**Result:** Unique length = `slow + 1 = 3`. Valid unique prefix: `[1, 2, 3]`.

---

## Pattern Recognition Layer

When facing an unfamiliar problem, walk through these decision checklists:

### For Search Space & Pair Problems
```text
Is there an ordered structure?
        ↓
Am I comparing two positions?
        ↓
Can one pointer eliminate possibilities?
        ↓
Can the pointers move monotonically?
        ↓
Can I avoid revisiting elements?
        ↓
Can I reduce O(N²) work to O(N)?
```

### For In-Place Array Modifications
```text
Do I need to modify/filter/rearrange the array in-place?
        ↓
Can one pointer READ incoming elements (fast)?
Can another pointer WRITE valid results (slow)?
```

### For Multi-Region Partitioning
```text
Can I maintain boundaries for regions with known properties?
(e.g., [0s | 1s | unexamined | 2s])
```

---

## Core Mental Models

- **Search Space Reduction:** *"Can I maintain two positions such that moving one pointer systematically eliminates a set of impossible possibilities?"*
- **In-Place Manipulation:** *"Can one pointer read incoming elements while another pointer writes valid results?"*
- **Partitioning Invariant:** *"Can I maintain explicit boundaries for regions whose properties are already known?"*
- **Pair/Triplet Optimization:** *"Can sorting + pointer movement eliminate $O(N^3)$ nested loops to $O(N^2)$?"*

---

## 🛑 Important Pattern Boundaries

Not every problem involving two index variables is a Two Pointers problem. Always identify the core reason why the optimization works:

| Problem Type / Concept | Primary Pattern | Why Two Pointers Is NOT the Primary Optimization |
|------------------------|-----------------|--------------------------------------------------|
| **Contiguous Window Traversal** | **Sliding Window** | Optimization comes from maintaining dynamic state across a moving window bounds. |
| **Logarithmic Search** | **Binary Search** | Optimization comes from repeatedly halving the search space ($O(\log N)$). |
| **Cumulative Range Query** | **Prefix Sum** | Optimization comes from precomputed cumulative array subtractions. |
| **Arbitrary Nested Index Loops** | **Brute Force / Other** | Two variables `i` and `j` in nested loops without space elimination do not constitute Two Pointers. |

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
| 1 | Two Sum II - Input Array Is Sorted | <a href="https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/" target="_blank">LeetCode 167</a> | Medium | Opposite-direction pointers on sorted array. |
| 2 | Remove Element | <a href="https://leetcode.com/problems/remove-element/" target="_blank">LeetCode 27</a> | Easy | In-place filtering using same-direction read/write pointers. |
| 3 | Valid Palindrome | <a href="https://leetcode.com/problems/valid-palindrome/" target="_blank">LeetCode 125</a> | Easy | Opposite-direction pointers with character skipping. |

---

### 2. Core (5 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 4 | Move Zeroes | <a href="https://leetcode.com/problems/move-zeroes/" target="_blank">LeetCode 283</a> | Easy | Same-direction read/write pointer compaction. |
| 5 | Remove Duplicates from Sorted Array | <a href="https://leetcode.com/problems/remove-duplicates-from-sorted-array/" target="_blank">LeetCode 26</a> | Easy | In-place deduplication with same-direction pointers. |
| 6 | Squares of a Sorted Array | <a href="https://leetcode.com/problems/squares-of-a-sorted-array/" target="_blank">LeetCode 977</a> | Easy | Two pointers at extremes comparing squared magnitudes. |
| 7 | Container With Most Water | <a href="https://leetcode.com/problems/container-with-most-water/" target="_blank">LeetCode 11</a> | Medium | Opposite-direction pointers with greedy area optimization. |
| 8 | 3Sum | <a href="https://leetcode.com/problems/3sum/" target="_blank">LeetCode 15</a> | Medium | Sorting + fixed element + opposite-direction two pointers with duplicate skip. |

---

### 3. Advanced / Combined (5 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 9 | 3Sum Closest | <a href="https://leetcode.com/problems/3sum-closest/" target="_blank">LeetCode 16</a> | Medium | Sorting + two pointers tracking closest distance. |
| 10 | Sort Colors | <a href="https://leetcode.com/problems/sort-colors/" target="_blank">LeetCode 75</a> | Medium | 3-way partitioning with `low`, `mid`, `high` pointers (Dutch National Flag). |
| 11 | Merge Sorted Array | <a href="https://leetcode.com/problems/merge-sorted-array/" target="_blank">LeetCode 88</a> | Easy | Reverse two pointers filling from back to avoid extra space. |
| 12 | Remove Duplicates from Sorted Array II | <a href="https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/" target="_blank">LeetCode 80</a> | Medium | In-place read/write pointers allowing at most 2 duplicates. |
| 13 | Trapping Rain Water | <a href="https://leetcode.com/problems/trapping-rain-water/" target="_blank">LeetCode 42</a> | Hard | Opposite-direction pointers tracking `leftMax` and `rightMax` bounds. *(Selected Hard problem for genuine two-pointer depth)*. |

---

## 🕵️ Interview Recognition — Pattern Hidden

The following problems test your ability to recognize pointer elimination and placement mechanics independently without explicit pattern labels. Analyze the requirements and derive the pointer invariants from scratch.

| # | Problem | LeetCode Link | Difficulty | Objective |
|---|---------|---------------|------------|-----------|
| 14 | 4Sum | <a href="https://leetcode.com/problems/4sum/" target="_blank">LeetCode 18</a> | Medium | Find 4 unique elements summing to target. |
| 15 | Backspace String Compare | <a href="https://leetcode.com/problems/backspace-string-compare/" target="_blank">LeetCode 844</a> | Easy | Compare strings after processing backspaces efficiently. |
| 16 | Interval List Intersections | <a href="https://leetcode.com/problems/interval-list-intersections/" target="_blank">LeetCode 986</a> | Medium | Find intersections of two sorted interval lists. |

<details>
<summary>💡 Reveal Pattern Hints (Click after attempting from a blank editor)</summary>

- **Problem 14:** Generalize 3Sum: fix 2 elements with nested loops, then run Two Pointers on the remaining search space.
- **Problem 15:** Iterate backward from the ends of both strings using two pointers, skipping backspaced characters.
- **Problem 16:** Compare the current interval from list A and list B, find intersection `[max(startA, startB), min(endA, endB)]`, and advance the pointer pointing to the earlier ending interval.
</details>

---

## 🏆 Mastery Criteria

You have mastered **Pattern #5** when you can:

- [ ] Explain why moving `left++` or `right--` is mathematically safe and discards impossible choices.
- [ ] Determine whether pointers should move in opposite directions or the same direction (read/write).
- [ ] Implement sorted pair search, 3Sum, and k-Sum search space reductions efficiently.
- [ ] Handle duplicates correctly in multi-element search loops without missing valid pairs.
- [ ] Perform in-place filtering, compaction, and deduplication using read/write pointers.
- [ ] Maintain multi-region partitioning invariants (Dutch National Flag 3-way partition).
- [ ] Recognize when sorting an input array enables a Two Pointers optimization.
- [ ] Distinguish Two Pointers from Sliding Window and Binary Search.
- [ ] Implement solutions cleanly from a blank editor.
- [ ] Recognize Two Pointers in an unlabeled interview problem.

---

## ➡️ Next Step

Once Two Pointers mechanics feel natural, move to **[Pattern 06: Sliding Window](./Pattern-06-Sliding-Window.md)** to learn how to track dynamic state across contiguous moving subsegments!
