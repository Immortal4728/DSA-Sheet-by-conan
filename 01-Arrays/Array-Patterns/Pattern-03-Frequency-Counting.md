# Pattern 3: Frequency Counting & Hashing

> Trade $O(N)$ auxiliary space for instant $O(1)$ lookup speed by maintaining a record of counts, indices, or states while traversing.

---

## 🔍 Beginner Audit: HashSet vs HashMap vs Frequency Array — Which One to Use?

Select the appropriate data structure based on the required information and data constraints:

```text
Do you only need to verify if an element HAS BEEN SEEN?  ──► Use HashSet
Do you need to associate a KEY with a VALUE (Count/Index)? ──► Use HashMap
Is the domain bounded to small ranges (e.g. ASCII 'a'-'z')? ──► Use int[] Frequency Array
```

| Structure | Best Used For | Memory Cost | Lookup Speed |
|-----------|---------------|-------------|--------------|
| **`HashSet`** | Membership & duplicate checking (`seen.contains(x)`) | $O(N)$ | $O(1)$ average |
| **`HashMap`** | Key $\rightarrow$ Value mapping (counts, indices, or transformed states) | $O(N)$ | $O(1)$ average |
| **`int[26]` Frequency Array** | Bounded character sets or small integer ranges | $O(\text{Range})$ | $O(1)$ direct index access |

---

## Core Technical Intuition — Hash Table Space-Time Tradeoff

In brute-force implementations, searching for a target element or complement requires scanning the array repeatedly ($O(N^2)$ time).

By storing visited elements or counts in a Hash Table, subsequent lookups execute in **$O(1)$ average time**.

```text
Input Element: nums[i]
Lookup Target: complement = target - nums[i]

Check Hash Table:
  ├── If complement exists  ──► Instant match found in O(1) time
  └── If complement missing ──► Store nums[i] in Hash Table and proceed
```

> **Core Technical Question:** *"What information about previously visited elements can be stored to eliminate repeated lookups?"*

---

## ☕ Standard Java Code Templates

### 1. Fixed Frequency Array Template (for ASCII / Bounded Range)
```java
public boolean isAnagram(String s, String t) {
    if (s.length() != t.length()) return false;
    
    int[] freq = new int[26];
    for (char c : s.toCharArray()) freq[c - 'a']++;
    for (char c : t.toCharArray()) {
        freq[c - 'a']--;
        if (freq[c - 'a'] < 0) return false; // Frequency mismatch
    }
    return true;
}
```

### 2. HashMap Complement Lookup Template (Two Sum)
```java
public int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> seen = new HashMap<>(); // Value -> Index
    
    for (int i = 0; i < nums.length; i++) {
        int complement = target - nums[i];
        if (seen.containsKey(complement)) {
            return new int[]{ seen.get(complement), i }; // Match found
        }
        seen.put(nums[i], i);
    }
    
    return new int[0];
}
```

---

## 🔍 Step-by-Step Trace Table (Dry Run)

Tracing **Two Sum (`LeetCode 1`)** with `nums = [2, 7, 11, 15]`, `target = 9`:

Initial State: `seen = {}`

| Step / Index | Current Num (`nums[i]`) | Target Complement (`target - nums[i]`) | Found in `seen` Map? | Action / Result | `seen` Map State after step |
|--------------|-------------------------|-----------------------------------------|----------------------|-----------------|-----------------------------|
| **`i = 0`** | `2` | $9 - 2 = 7$ | No | Store `{2: 0}` | `{2: 0}` |
| **`i = 1`** | `7` | $9 - 7 = 2$ | **YES** (Key `2` at index `0`) | Return `[0, 1]` | `{2: 0, 7: 1}` |

---

## 💡 Information-Management Progression

```text
Set membership
      ↓
Frequency
      ↓
Map lookup
      ↓
Complement lookup
      ↓
Grouping
      ↓
Frequency + selection
      ↓
HashMap + Prefix Sum
      ↓
HashSet optimization
      ↓
Multiple structures
      ↓
HashMap + Sliding Window
      ↓
Hard combined pattern
```

*Note: This progression represents increasing **information-management complexity**, not simply LeetCode difficulty tags.*

---

## 🛑 Important Pattern Boundaries (Supporting vs Primary Pattern)

The presence of a `HashMap` or `HashSet` in code does **NOT** automatically make a problem a pure Frequency Counting problem.

Hashing is frequently used as a **supporting data structure** for other primary algorithmic ideas:

| Combination | Primary Pattern | Role of Hashing |
|-------------|-----------------|-----------------|
| **Subarray Sum Equals K** | **Prefix Sum** | Provides $O(1)$ lookup for previously seen prefix totals. |
| **Find All Anagrams in a String** | **Sliding Window** | Tracks character frequencies inside the active moving window. |
| **Graph Clone / Graph Traversal** | **Graph (BFS/DFS)** | Maps original nodes to cloned nodes or tracks visited states. |

> **Key Rule:** Always identify the **algorithmic idea** that creates the core optimization (e.g., Prefix Sum, Sliding Window), not just the data structure used to implement it.

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 6 |
| **Medium** | 7 |
| **Hard** | 1 |
| **Total** | **14** |

---

## 🎯 Question Progression (Curated 14-Question Set)

### 1. Foundation (4 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 1 | Contains Duplicate | <a href="https://leetcode.com/problems/contains-duplicate/" target="_blank">LeetCode 217</a> | Easy | Basic membership tracking with HashSet. |
| 2 | Two Sum | <a href="https://leetcode.com/problems/two-sum/" target="_blank">LeetCode 1</a> | Easy | Complement lookup using HashMap. |
| 3 | Valid Anagram | <a href="https://leetcode.com/problems/valid-anagram/" target="_blank">LeetCode 242</a> | Easy | Frequency counting. |
| 4 | First Unique Character in a String | <a href="https://leetcode.com/problems/first-unique-character-in-a-string/" target="_blank">LeetCode 387</a> | Easy | Frequency table + second traversal. |

---

### 2. Core (5 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 5 | Intersection of Two Arrays | <a href="https://leetcode.com/problems/intersection-of-two-arrays/" target="_blank">LeetCode 349</a> | Easy | Membership + set-based lookup. |
| 6 | Top K Frequent Elements | <a href="https://leetcode.com/problems/top-k-frequent-elements/" target="_blank">LeetCode 347</a> | Medium | Frequency map $\rightarrow$ frequency-based selection. |
| 7 | Group Anagrams | <a href="https://leetcode.com/problems/group-anagrams/" target="_blank">LeetCode 49</a> | Medium | Converting equivalent values into a common hashable representation. |
| 8 | Subarray Sum Equals K | <a href="https://leetcode.com/problems/subarray-sum-equals-k/" target="_blank">LeetCode 560</a> | Medium | Prefix Sum + HashMap. |
| 9 | Contiguous Array | <a href="https://leetcode.com/problems/contiguous-array/" target="_blank">LeetCode 525</a> | Medium | Prefix-state transformation + HashMap. |

---

### 3. Advanced / Combined (5 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 10 | Longest Consecutive Sequence | <a href="https://leetcode.com/problems/longest-consecutive-sequence/" target="_blank">LeetCode 128</a> | Medium | HashSet + intelligent membership checks to avoid unnecessary traversal. |
| 11 | Valid Sudoku | <a href="https://leetcode.com/problems/valid-sudoku/" target="_blank">LeetCode 36</a> | Easy | Multiple frequency/membership structures simultaneously. |
| 12 | Insert Delete GetRandom O(1) | <a href="https://leetcode.com/problems/insert-delete-getrandom-o1/" target="_blank">LeetCode 380</a> | Medium | HashMap + ArrayList working together. |
| 13 | Find All Anagrams in a String | <a href="https://leetcode.com/problems/find-all-anagrams-in-a-string/" target="_blank">LeetCode 438</a> | Medium | Frequency Counting + Sliding Window. |
| 14 | Subarrays with K Different Integers | <a href="https://leetcode.com/problems/subarrays-with-k-different-integers/" target="_blank">LeetCode 992</a> | Hard | HashMap + Sliding Window + exactly-K reasoning. |

---

## 🏆 Mastery Criteria

A learner has mastered **Pattern #3** when they can:

- [ ] Choose appropriately between HashSet, HashMap, and direct Frequency Array.
- [ ] Perform complement lookup ($O(1)$) to eliminate nested loops.
- [ ] Store `value -> index` or `value -> state` efficiently.
- [ ] Recognize when hashing can reduce $O(N^2)$ brute force to $O(N)$ time.
- [ ] Combine hashing with **Prefix Sum** or **Sliding Window**.
- [ ] Recognize when hashing is merely a supporting data structure for another primary pattern.
- [ ] Explain why the stored information is sufficient to make the next decision.

---

## ➡️ Next Step

Once Frequency Counting & Hashing feels natural, move to **[Pattern 04: Prefix Sum](./Pattern-04-Prefix-Sum.md)** to learn how to precompute cumulative totals for instant $O(1)$ range queries.
