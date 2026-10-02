# Pattern 5: String Sliding Window

> Maintain a contiguous active range `[left ... right]` across a string and update character state incrementally in $O(1)$ per step, instead of rescanning the substring in $O(N^2)$.

---

## 🔍 Beginner Audit: What Makes a String Window Different from an Array Window?

- **Array Sliding Window (Pattern #6 of Arrays):** You tracked a numerical state — sum, product, or count of numbers inside `[left...right]`.
- **String Sliding Window:** You track a **character state** — frequency counts, distinct character counts, or a match condition against a target string.

> **Key Upgrade:** The window's validity condition is no longer `"sum > target"`. It becomes character-based: `"does this window contain all required characters?"` or `"does this window have at most K distinct characters?"`. The `left`/`right` expansion mechanics are identical.

---

## Core Technical Intuition — Incremental Character State

```text
Window [left ... right] over string s:

Step 1:   s[0] s[1] s[2] s[3] s[4] s[5]
          [  a   b   c ]  d    e    f
            ↑           ↑
          left         right
          windowFreq: {a:1, b:1, c:1}

Expand right (add s[3]='d'):
Step 2:   [  a   b   c   d ]  e    f
                               ↑
                             right
          windowFreq: {a:1, b:1, c:1, d:1}

Shrink left (window invalid — too many distinct):
Step 3:    a   [  b   c   d ]  e    f
                ↑
              left
          windowFreq: {b:1, c:1, d:1}
```

> **Key Performance:** Each character enters the window exactly once and leaves exactly once → $O(N)$ total, not $O(N^2)$.

---

## Window Variants in String Problems

### 1. Fixed-Size Window (size = `|p|`)
The window size equals the pattern length. Used for anagram and permutation matching.

### 2. Variable-Size Window — Longest Valid Substring
Expand `right`. When condition breaks (duplicate found, too many distinct), shrink `left`. Record `maxLen`.

### 3. Variable-Size Window — Shortest Valid Substring
Expand `right` until all requirements are met. Then shrink `left` while still valid. Record `minLen`.

### 4. At-Most K Distinct → Exactly K Count
Counting substrings with **EXACTLY K** distinct characters:
$$\text{Exactly}(K) = \text{AtMost}(K) - \text{AtMost}(K - 1)$$

---

## ☕ Standard Java Code Templates

### Template 1: Fixed-Size Window (Anagram / Permutation Match)
```java
// Use when: checking if any window of fixed size matches a target frequency
public List<Integer> findAnagrams(String s, String p) {
    List<Integer> result = new ArrayList<>();
    int[] pFreq = new int[26], wFreq = new int[26];
    for (char c : p.toCharArray()) pFreq[c - 'a']++;

    for (int right = 0; right < s.length(); right++) {
        wFreq[s.charAt(right) - 'a']++;              // Add incoming char
        if (right >= p.length()) {
            wFreq[s.charAt(right - p.length()) - 'a']--; // Remove outgoing char
        }
        if (Arrays.equals(wFreq, pFreq)) result.add(right - p.length() + 1);
    }
    return result;
}
```

### Template 2: Variable-Size Window — Longest (No-Repeat Constraint)
```java
// Use when: longest substring satisfying a condition (no duplicates, at most K distinct)
public int lengthOfLongestSubstring(String s) {
    int[] freq = new int[128];
    int left = 0, maxLen = 0;
    for (int right = 0; right < s.length(); right++) {
        freq[s.charAt(right)]++;                          // Expand
        while (freq[s.charAt(right)] > 1) {               // Condition violated
            freq[s.charAt(left)]--;                        // Shrink
            left++;
        }
        maxLen = Math.max(maxLen, right - left + 1);       // Record
    }
    return maxLen;
}
```

### Template 3: Variable-Size Window — Shortest (Minimum Cover)
```java
// Use when: minimum window containing all required characters
public String minWindow(String s, String t) {
    int[] need = new int[128];
    for (char c : t.toCharArray()) need[c]++;
    int left = 0, have = 0, required = t.length();
    int minLen = Integer.MAX_VALUE, minStart = 0;

    for (int right = 0; right < s.length(); right++) {
        if (need[s.charAt(right)]-- > 0) have++;    // Expand
        while (have == required) {                   // Window is valid → shrink
            if (right - left + 1 < minLen) { minLen = right - left + 1; minStart = left; }
            if (need[s.charAt(left)]++ == 0) have--;
            left++;
        }
    }
    return minLen == Integer.MAX_VALUE ? "" : s.substring(minStart, minStart + minLen);
}
```

---

## 🔍 Step-by-Step Trace Table (Dry Run)

Tracing **Longest Substring Without Repeating Characters (`LeetCode 3`)** on `s = "abcba"`:

| `right` | `s[right]` | freq after add | Condition: freq > 1? | Action | Window `[left...right]` | `maxLen` |
|---------|------------|----------------|----------------------|--------|------------------------|----------|
| 0 | `'a'` | a:1 | No | — | `[0..0]` = `"a"` | 1 |
| 1 | `'b'` | a:1,b:1 | No | — | `[0..1]` = `"ab"` | 2 |
| 2 | `'c'` | a:1,b:1,c:1 | No | — | `[0..2]` = `"abc"` | 3 |
| 3 | `'b'` | a:1,b:**2**,c:1 | **Yes (b)** | shrink: remove `s[0]='a'`, `left=1` → still b:2, shrink: remove `s[1]='b'`, `left=2` | `[2..3]` = `"cb"` | 3 |
| 4 | `'a'` | a:1,b:1,c:1 | No | — | `[2..4]` = `"cba"` | **3** |

**Result:** `3` (substrings `"abc"` or `"cba"`).

---

## Pattern Recognition Layer

```text
Is the target about a contiguous substring / subarray?
        ↓ Yes
Can I define a clear window VALIDITY condition?
        ↓ Yes
Can I add the incoming character (right) and remove the outgoing (left) in O(1)?
        ↓ Yes → Sliding Window applies

Fixed size window (window = pattern length)?
        ↓ Yes → Template 1: Fixed window

Variable size: find LONGEST substring satisfying condition?
        ↓ Yes → Template 2: Expand right, shrink left on violation

Variable size: find SHORTEST substring satisfying condition?
        ↓ Yes → Template 3: Expand right until valid, then shrink left

Count substrings with EXACTLY K distinct?
        ↓ → AtMost(K) - AtMost(K-1) transformation
```

---

## Core Mental Models

- **Monotonic Window:** *"Expanding `right` adds character state. Shrinking `left` removes it. The window only invalidates in one direction at a time — this is what makes O(N) possible."*
- **Replacement Budget:** *"A window is valid as long as `(window_length - max_freq_char) ≤ K`. This formula avoids needing to track which specific characters need to change."*
- **AtMost Transformation:** *"When counting subarrays with EXACTLY K, the direct approach double-counts. `AtMost(K) - AtMost(K-1)` isolates the exact count cleanly."*

---

## 🛑 Pattern Boundary (When Sliding Window Does NOT Apply)

| Problem Constraint | Can I Use Sliding Window? | What to Use Instead |
|--------------------|--------------------------|---------------------|
| Positive character counts only | ✅ **YES** | Expanding right strictly increases count. |
| Subarray sum with **negative numbers** | ❌ **NO** | Prefix Sum + HashMap |
| **Non-contiguous** subsequences | ❌ **NO** | Two Pointers / DP |
| Pattern matching (find exact occurrence) | ❌ **NO** | KMP / Pattern #11 |

---

## 📊 Difficulty Distribution

| Depth Tier | Count |
|------------|-------|
| **Foundation** | 3 |
| **Core** | 5 |
| **Advanced** | 5 |
| **Interview Recognition** | 3 |
| **Total** | **16** |

---

## 🎯 Question Progression (Curated 16-Question Set)

### 1. Foundation (3 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 1 | Maximum Average Subarray I | <a href="https://leetcode.com/problems/maximum-average-subarray-i/" target="_blank">LeetCode 643</a> | Easy | Fixed-size window: numerical sum maintenance (bridging from Array Window). |
| 2 | Find All Anagrams in a String | <a href="https://leetcode.com/problems/find-all-anagrams-in-a-string/" target="_blank">LeetCode 438</a> | Medium | Fixed-size frequency matching: compare `int[26]` window array against target. |
| 3 | Permutation in String | <a href="https://leetcode.com/problems/permutation-in-string/" target="_blank">LeetCode 567</a> | Medium | Same as above but return true on first match — introduces early exit optimization. |

---

### 2. Core (5 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 4 | Longest Substring Without Repeating Characters | <a href="https://leetcode.com/problems/longest-substring-without-repeating-characters/" target="_blank">LeetCode 3</a> | Medium | Variable-size: shrink `left` aggressively when any character appears more than once. |
| 5 | Longest Repeating Character Replacement | <a href="https://leetcode.com/problems/longest-repeating-character-replacement/" target="_blank">LeetCode 424</a> | Medium | Variable-size: replacement budget formula — `(window_length - maxFreq) ≤ K`. |
| 6 | Max Consecutive Ones III | <a href="https://leetcode.com/problems/max-consecutive-ones-iii/" target="_blank">LeetCode 1004</a> | Medium | Variable-size: track zero count as a budget; shrink when zeroes exceed `K`. |
| 7 | Fruit Into Baskets | <a href="https://leetcode.com/problems/fruit-into-baskets/" target="_blank">LeetCode 904</a> | Medium | Variable-size: at-most 2 distinct elements; shrink when HashMap size exceeds limit. |
| 8 | Minimum Size Subarray Sum | <a href="https://leetcode.com/problems/minimum-size-subarray-sum/" target="_blank">LeetCode 209</a> | Medium | Variable-size shortest: record minimum length while aggressively shrinking the valid window. |

---

### 3. Advanced (5 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 9 | Minimum Window Substring | <a href="https://leetcode.com/problems/minimum-window-substring/" target="_blank">LeetCode 76</a> | Hard | Two frequency maps: track `required` satisfaction count; expand then shrink aggressively. |
| 10 | Substring with Concatenation of All Words | <a href="https://leetcode.com/problems/substring-with-concatenation-of-all-words/" target="_blank">LeetCode 30</a> | Hard | Word-level window: frequency map over fixed-length word tokens instead of characters. |
| 11 | Longest Substring with At Most K Distinct Characters | <a href="https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/" target="_blank">LeetCode 340</a> | Medium | At-most K generalization of Fruit Into Baskets with a dynamic K parameter. |
| 12 | Subarrays with K Different Integers | <a href="https://leetcode.com/problems/subarrays-with-k-different-integers/" target="_blank">LeetCode 992</a> | Hard | Exactly K distinct: `AtMost(K) - AtMost(K-1)` transformation. |
| 13 | Count Number of Nice Subarrays | <a href="https://leetcode.com/problems/count-number-of-nice-subarrays/" target="_blank">LeetCode 1248</a> | Medium | Exactly K odd numbers: same `AtMost(K) - AtMost(K-1)` counting trick. |

---

### 4. Interview Recognition — Pattern Hidden

| # | Problem | LeetCode Link | Difficulty | Objective |
|---|---------|---------------|------------|-----------|
| 14 | Longest Substring with At Most Two Distinct Characters | <a href="https://leetcode.com/problems/longest-substring-with-at-most-two-distinct-characters/" target="_blank">LeetCode 159</a> | Medium | Find longest substring with at most 2 distinct characters. |
| 15 | Number of Substrings Containing All Three Characters | <a href="https://leetcode.com/problems/number-of-substrings-containing-all-three-characters/" target="_blank">LeetCode 1358</a> | Medium | Count substrings containing all of `'a'`, `'b'`, `'c'`. |
| 16 | Minimum Window Subsequence | <a href="https://leetcode.com/problems/minimum-window-subsequence/" target="_blank">LeetCode 727</a> | Hard | Find minimum window in `s1` such that `s2` appears as a subsequence. |

<details>
<summary>💡 Reveal Pattern Hints (Click after attempting from a blank editor)</summary>

- **Problem 14:** Identical to Fruit Into Baskets with K=2. Use a HashMap; shrink `left` whenever distinct count exceeds 2. Classification: **Sliding Window + At-most K Distinct**.
- **Problem 15:** Once a valid window is found, all substrings ending at `right` from `left` down to index `0` are also valid. Add `left + 1` to result instead of just counting 1. Classification: **Sliding Window + Counting from Valid State**.
- **Problem 16:** `s2` must be a subsequence, not a substring — window validity requires order-preserved matching, not frequency matching. Expand `right` with Two Pointers to find forward match, then compress `left` backwards. Classification: **Two Pointers Expansion + Reverse Compression (Advanced Simulation)**.
</details>

---

## 🏆 Mastery Criteria

You have mastered **Pattern #5** when you can:

- [ ] Implement a fixed-size character frequency window with `int[26]` comparison.
- [ ] Implement a variable-size longest window: expand right, shrink left on violation.
- [ ] Implement a variable-size shortest window: expand right until valid, shrink left while valid.
- [ ] Apply the replacement budget formula `(window_length - maxFreq) ≤ K`.
- [ ] Apply the `AtMost(K) - AtMost(K-1)` transformation for exact counting.
- [ ] Know when Sliding Window fails (negative numbers, non-contiguous targets).
- [ ] Distinguish String Sliding Window from Two Pointers and Pattern Matching.
- [ ] Implement all 16 solutions from a blank editor without tutorial dependence.

---

## ➡️ Next Step

Once String Sliding Window mechanics are solid, move to **[Pattern 06: Anagram & Rearrangement Patterns](./Pattern-06-Anagram-and-Rearrangement-Patterns.md)** to master frequency invariant maintenance and canonical state representation across grouping and structural mapping problems.
