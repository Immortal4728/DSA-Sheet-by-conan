# Pattern 6: Anagram & Rearrangement Patterns

> When character ordering is irrelevant and only character frequencies define structural equality, you are in an anagram / rearrangement problem.

---

## 🔍 Beginner Audit: What Is an Anagram?

An **anagram** is a rearrangement of characters such that every character's frequency is preserved exactly.

- `"listen"` and `"silent"` are anagrams — same frequency, different order.
- `"rat"` and `"car"` are NOT anagrams — `'t'` and `'c'` have no counterpart.

> **Key Insight:** Because ordering is ignored, the entire problem collapses to: *"Do both strings have identical character frequency distributions?"* This single insight drives every problem in this section.

---

## Core Technical Intuition — Frequency Invariant Maintenance

Anagram problems are a spectrum of the same core mechanic applied in different contexts:

```text
╔════════════════════════════════════════════════════════════════════╗
║  Frequency Equality   → Are count(S) == count(T)?               ║
║  Frequency Subset     → Does count(source) ≥ count(target)?     ║
║  Canonical Key        → Map equal-frequency strings to same key  ║
║  Windowed Anagram     → Check equality as the window slides      ║
║  Word-Level Window    → Scale chars to fixed-length word tokens  ║
║  Index Mapping        → Use values as structural array indices   ║
║  Bottleneck Capacity  → How many times can target be built?      ║
╚════════════════════════════════════════════════════════════════════╝
```

---

## ☕ Standard Java Code Templates

### Template 1: Frequency Equality (Valid Anagram)
```java
// Use when: checking if two strings have identical character compositions
int[] freq = new int[26];
for (char c : s.toCharArray()) freq[c - 'a']++;
for (char c : t.toCharArray()) freq[c - 'a']--;
for (int count : freq) if (count != 0) return false;
return true;
```

### Template 2: Frequency Subset (Ransom Note)
```java
// Use when: checking if source contains enough of each required character
int[] magazine = new int[26];
for (char c : magazine_str.toCharArray()) magazine[c - 'a']++;
for (char c : ransomNote.toCharArray()) {
    if (--magazine[c - 'a'] < 0) return false; // ran out
}
return true;
```

### Template 3: Canonical Key for Grouping (Group Anagrams)
```java
// Use when: grouping strings that are anagrams of each other
Map<String, List<String>> map = new HashMap<>();
for (String word : strs) {
    char[] arr = word.toCharArray();
    Arrays.sort(arr);
    String key = new String(arr); // sorted string = canonical anagram key
    map.computeIfAbsent(key, k -> new ArrayList<>()).add(word);
}
return new ArrayList<>(map.values());
```

### Template 4: Bottleneck Capacity (How Many Times Can Target Be Built)
```java
// Use when: count how many complete copies of target can be formed from source chars
int[] sFreq = new int[26], tFreq = new int[26];
for (char c : s.toCharArray()) sFreq[c - 'a']++;
for (char c : target.toCharArray()) tFreq[c - 'a']++;
int result = Integer.MAX_VALUE;
for (int i = 0; i < 26; i++) {
    if (tFreq[i] > 0) {
        result = Math.min(result, sFreq[i] / tFreq[i]); // bottleneck: minimum ratio
    }
}
return result == Integer.MAX_VALUE ? 0 : result;
```

---

## 🔍 Step-by-Step Trace Table (Dry Run)

Tracing **Group Anagrams (`LeetCode 49`)** on `strs = ["eat", "tea", "tan", "ate", "nat", "bat"]`:

| Word | Sorted Key | Map Entry |
|------|-----------|-----------|
| `"eat"` | `"aet"` | `{"aet": ["eat"]}` |
| `"tea"` | `"aet"` | `{"aet": ["eat", "tea"]}` |
| `"tan"` | `"ant"` | `{"aet": [...], "ant": ["tan"]}` |
| `"ate"` | `"aet"` | `{"aet": ["eat", "tea", "ate"]}` |
| `"nat"` | `"ant"` | `{"ant": ["tan", "nat"]}` |
| `"bat"` | `"abt"` | `{"abt": ["bat"]}` |

**Result:** `[["eat","tea","ate"], ["tan","nat"], ["bat"]]`

---

## Pattern Recognition Layer

```text
Does character ORDER matter in this problem?
        ↓ No → Anagram / Rearrangement pattern likely applies

Do I need to check if two strings have identical character compositions?
        ↓ Yes → Frequency Equality (Template 1)

Does one string need to "supply" characters for another?
        ↓ Yes → Frequency Subset / Availability (Template 2)

Do I need to group strings with the same character set?
        ↓ Yes → Canonical Key / Sorted String as HashMap key (Template 3)

Do I need to check if an anagram exists as a contiguous window?
        ↓ Yes → Sliding Window Frequency Match (Pattern #5 bridge)

How many complete copies of a target can be assembled?
        ↓ Yes → Bottleneck Capacity: min(sFreq[i] / tFreq[i]) (Template 4)
```

---

## Core Mental Models

- **Order Independence:** *"If character frequencies completely define equality, physical position is irrelevant. Stop thinking about positions — think in terms of counts."*
- **Canonical Representation:** *"Two anagrams produce the same sorted string. This sorted string is a perfect hash key: any two strings mapping to the same key belong to the same anagram group."*
- **Bottleneck Capacity:** *"The maximum number of complete target copies you can build is constrained by the character with the worst supply-to-demand ratio — the minimum frequency ratio across all characters."*
- **Sliding Window Bridge:** *"Finding anagram windows in a larger string is a Sliding Window problem (Pattern #5). Hashing is the frequency state; Window is the driver."*

---

## 🛑 Pattern Boundary

| Problem Requirement | Move To Pattern |
|---------------------|-----------------|
| Anagram exists in a sliding window of a larger string | **Pattern #5 — String Sliding Window** |
| Character order must be preserved (subsequences) | **Pattern #8 — Subsequence Reasoning / DP** |
| Characters can be inserted, deleted, or substituted with costs | **String DP — Edit Distance** |
| Exact substring matching | **Pattern #11 — String Pattern Matching** |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 3 |
| **Medium** | 4 |
| **Hard** | 1 |
| **Total** | **8** |

---

## 🎯 Question Progression (Curated 8-Question Set)

### 1. Foundation (2 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 1 | Valid Anagram | <a href="https://leetcode.com/problems/valid-anagram/" target="_blank">LeetCode 242</a> | Easy | Frequency equality: `int[26]` balance between two strings. |
| 2 | Ransom Note | <a href="https://leetcode.com/problems/ransom-note/" target="_blank">LeetCode 383</a> | Easy | Frequency availability: source must supply at least the required count of each character. |

---

### 2. Core (3 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 3 | Group Anagrams | <a href="https://leetcode.com/problems/group-anagrams/" target="_blank">LeetCode 49</a> | Medium | Canonical key: sort each string to produce a group identifier for frequency-equal strings. |
| 4 | Find All Anagrams in a String | <a href="https://leetcode.com/problems/find-all-anagrams-in-a-string/" target="_blank">LeetCode 438</a> | Medium | Fixed-size sliding window: incrementally update `int[26]` as window shifts, compare to target. |
| 5 | Permutation in String | <a href="https://leetcode.com/problems/permutation-in-string/" target="_blank">LeetCode 567</a> | Medium | Identical to LC 438 but return `true` on first match — introduces early exit. |

---

### 3. Advanced (2 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 6 | Find All Duplicates in an Array | <a href="https://leetcode.com/problems/find-all-duplicates-in-an-array/" target="_blank">LeetCode 442</a> | Medium | Index-position mapping: treat element values as structural array indices; negate to track visits in $O(1)$ space. |
| 7 | Substring with Concatenation of All Words | <a href="https://leetcode.com/problems/substring-with-concatenation-of-all-words/" target="_blank">LeetCode 30</a> | Hard | Word-level frequency window: scale the anagram window from characters to fixed-length word tokens. |

---

### 4. Interview Recognition — Pattern Hidden

| # | Problem | LeetCode Link | Difficulty | Objective |
|---|---------|---------------|------------|-----------|
| 8 | Rearrange Characters to Make Target String | <a href="https://leetcode.com/problems/rearrange-characters-to-make-target-string/" target="_blank">LeetCode 2287</a> | Easy | Count how many complete copies of `target` can be formed from the characters in `s`. |

<details>
<summary>💡 Reveal Pattern Hint (Click after attempting from a blank editor)</summary>

- **Problem 8:** Because characters can be freely rearranged, position is irrelevant. The maximum number of `target` copies is the minimum across all characters of `floor(sFreq[c] / targetFreq[c])`. Classification: **Frequency Bottleneck Capacity**.
</details>

---

## 🏆 Mastery Criteria

You have mastered **Pattern #6** when you can:

- [ ] Verify anagram equality using a frequency balance array in $O(N)$.
- [ ] Check frequency subset availability (source ≥ required) in $O(N)$.
- [ ] Group anagrams using a sorted canonical key in a `HashMap`.
- [ ] Slide a fixed-size frequency window to find anagram positions in a larger string.
- [ ] Compute bottleneck capacity using the minimum frequency ratio.
- [ ] Recognize when an anagram problem is actually a Sliding Window problem in disguise.
- [ ] Implement all 8 solutions from a blank editor without tutorial dependence.

---

## ➡️ Next Step

Once anagram mechanics are automatic, move to **[Pattern 07: String Construction & Transformation](./Pattern-07-String-Construction-and-Transformation.md)** to learn how to build and transform strings using explicit carry states, state machines, and structural mapping.
