# Pattern 2: Character Frequency & Hashing

> Store character state (counts, mappings, membership) so that every lookup costs $O(1)$ instead of $O(N)$, enabling linear-time solutions on string problems that would otherwise require nested scanning.

---

## 🔍 Beginner Audit: Why Do Strings Need Special Hashing?

- **In Array Hashing (Pattern #3 of Arrays):** You mapped `number → index` or `number → count` using a `HashMap<Integer, Integer>`.
- **In String Hashing:** You map `character → frequency`, `character → character`, or `string → group` using either a `HashMap<Character, T>` or a fixed-size `int[26]` / `int[128]` array.

> **Key Upgrade:** Unlike unbounded numbers in arrays, string characters are **constrained to a finite alphabet** (e.g., 26 lowercase letters = indices 0–25). An `int[26]` array is faster and uses less memory than a `HashMap` for character-only problems.

```text
'a' → index 0     (c - 'a')
'b' → index 1
...
'z' → index 25
```

---

## Core Technical Intuition — Character State Mapping

Before writing code, ask:

1. **What character relationship am I tracking?**
   - *Frequency:* How many times does each character appear?
   - *Membership:* Have I seen this character before?
   - *Mapping:* Does character `A` uniquely correspond to character `B`?
   - *Grouping:* Which strings share the same character composition?

2. **What data structure do I need?**
   - `int[26]` — lowercase alphabet frequency (fastest, $O(1)$ space).
   - `int[128]` — full ASCII frequency.
   - `HashMap<Character, Integer>` — arbitrary character-to-count mapping.
   - `HashSet<Character>` — existence checking only.

```text
s = "anagram"

Build frequency:
a → freq[0] = 3
n → freq[13] = 1
g → freq[6] = 1
r → freq[17] = 1
m → freq[12] = 1
```

---

## ☕ Standard Java Code Templates

### Template 1: Frequency Array (Lowercase Only)
```java
// Use when: comparing two strings for anagrams, character balance
int[] freq = new int[26];
for (char c : s.toCharArray()) {
    freq[c - 'a']++;    // increment for s
}
for (char c : t.toCharArray()) {
    freq[c - 'a']--;    // decrement for t
}
// freq[i] == 0 for all i means s and t are anagrams
```

### Template 2: Two-Pass Frequency Lookup
```java
// Use when: you need to find characters by frequency AFTER collecting all counts
int[] freq = new int[26];
for (char c : s.toCharArray()) freq[c - 'a']++;    // Pass 1: collect
for (int i = 0; i < s.length(); i++) {             // Pass 2: decide
    if (freq[s.charAt(i) - 'a'] == 1) return i;   // first unique
}
return -1;
```

### Template 3: Bidirectional Character Mapping
```java
// Use when: checking isomorphic strings or word patterns (1-to-1 mapping)
Map<Character, Character> sToT = new HashMap<>();
Map<Character, Character> tToS = new HashMap<>();
for (int i = 0; i < s.length(); i++) {
    char sc = s.charAt(i), tc = t.charAt(i);
    if (sToT.containsKey(sc) && sToT.get(sc) != tc) return false;
    if (tToS.containsKey(tc) && tToS.get(tc) != sc) return false;
    sToT.put(sc, tc);
    tToS.put(tc, sc);
}
return true;
```

### Template 4: Canonical Key for Grouping
```java
// Use when: grouping anagrams — strings with same characters map to same key
Map<String, List<String>> map = new HashMap<>();
for (String word : strs) {
    char[] arr = word.toCharArray();
    Arrays.sort(arr);                           // sorted string = canonical key
    String key = new String(arr);
    map.computeIfAbsent(key, k -> new ArrayList<>()).add(word);
}
return new ArrayList<>(map.values());
```

---

## 🔍 Step-by-Step Trace Table (Dry Run)

Tracing **Valid Anagram (`LeetCode 242`)** on `s = "rat"`, `t = "car"`:

| Char | Operation | freq['r'-'a'] | freq['a'-'a'] | freq['t'-'a'] | freq['c'-'a'] |
|------|-----------|---------------|---------------|---------------|---------------|
| `'r'` (s) | +1 | **1** | 0 | 0 | 0 |
| `'a'` (s) | +1 | 1 | **1** | 0 | 0 |
| `'t'` (s) | +1 | 1 | 1 | **1** | 0 |
| `'c'` (t) | -1 | 1 | 1 | 1 | **-1** |
| `'a'` (t) | -1 | 1 | **0** | 1 | -1 |
| `'r'` (t) | -1 | **0** | 0 | 1 | -1 |

**Check:** Not all zeros → `freq['t'-'a'] = 1`, `freq['c'-'a'] = -1` → **Not an anagram**. Return `false`.

---

## Pattern Recognition Layer

When you see a string problem with character relationships, ask:

```text
Do I need to remember something about each character seen so far?
        ↓
Is it about frequency? → Use int[26] or HashMap<Char, Int>
        ↓
Is it about membership? → Use HashSet<Character>
        ↓
Is it about 1-to-1 mapping? → Use two HashMaps (bidirectional)
        ↓
Is it about grouping strings together? → Use a canonical key (sort or count signature)
        ↓
Does the frequency state need to change as a window moves? → Hashing + Sliding Window (Pattern #5)
```

---

## Core Mental Models

- **Frequency Array Over HashMap:** *"When the character set is bounded (a-z), `int[26]` gives $O(1)$ access with zero overhead. Always prefer it over `HashMap` for lowercase letter problems."*
- **Two-Pass Strategy:** *"Collect all information in pass 1. Make decisions in pass 2. Never decide while you're still collecting."*
- **Canonical Keys:** *"Any two strings with identical character compositions share the same sorted key. Sorting them produces a unique group identifier."*
- **Hashing as Support:** *"When you see Hashing + Sliding Window, hashing is the memory — the Window is the driver. They are separate concerns."*

---

## 🛑 Pattern Boundary

| Problem Requirement | Move To Pattern |
|---------------------|-----------------|
| Frequencies over a moving substring window | **Pattern #5 — String Sliding Window** |
| Detecting palindromic structure | **Pattern #3 — Palindrome Patterns** |
| Exact substring matching (KMP) | **Pattern #11 — String Pattern Matching** |
| Edit distance or LCS | **String DP (later section)** |
| Nested characters or brackets | **Pattern #9 — Stack-Based String Problems** |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 7 |
| **Medium** | 3 |
| **Hard** | 2 |
| **Total** | **12** |

---

## 🎯 Question Progression (Curated 12-Question Set)

### 1. Foundation (2 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 1 | Valid Anagram | <a href="https://leetcode.com/problems/valid-anagram/" target="_blank">LeetCode 242</a> | Easy | `int[26]` frequency balance between two strings. |
| 2 | First Unique Character in a String | <a href="https://leetcode.com/problems/first-unique-character-in-a-string/" target="_blank">LeetCode 387</a> | Easy | Two-pass frequency lookup: collect then decide. |

---

### 2. Core (4 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 3 | Ransom Note | <a href="https://leetcode.com/problems/ransom-note/" target="_blank">LeetCode 383</a> | Easy | Frequency availability: source supplies at least the required counts. |
| 4 | Isomorphic Strings | <a href="https://leetcode.com/problems/isomorphic-strings/" target="_blank">LeetCode 205</a> | Easy | Bidirectional character mapping for 1-to-1 correspondence validation. |
| 5 | Word Pattern | <a href="https://leetcode.com/problems/word-pattern/" target="_blank">LeetCode 290</a> | Easy | Bidirectional mapping between a character pattern and word tokens. |
| 6 | Group Anagrams | <a href="https://leetcode.com/problems/group-anagrams/" target="_blank">LeetCode 49</a> | Medium | Canonical key grouping: sorted string maps strings with equal composition. |

---

### 3. Advanced (4 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 7 | Sort Characters By Frequency | <a href="https://leetcode.com/problems/sort-characters-by-frequency/" target="_blank">LeetCode 451</a> | Medium | Collect frequencies, sort by count, reconstruct output string. |
| 8 | Custom Sort String | <a href="https://leetcode.com/problems/custom-sort-string/" target="_blank">LeetCode 791</a> | Medium | Frequency-based reconstruction using an externally defined ordering. |
| 9 | Longest Palindrome | <a href="https://leetcode.com/problems/longest-palindrome/" target="_blank">LeetCode 409</a> | Easy | Greedy frequency parity: use all even counts, allow at most one odd center. |
| 10 | Find All Anagrams in a String | <a href="https://leetcode.com/problems/find-all-anagrams-in-a-string/" target="_blank">LeetCode 438</a> | Medium | Fixed-size sliding window updating frequency state in $O(1)$ per shift. |

---

### 4. Interview Recognition — Pattern Hidden

| # | Problem | LeetCode Link | Difficulty | Objective |
|---|---------|---------------|------------|-----------|
| 11 | Longest Substring Without Repeating Characters | <a href="https://leetcode.com/problems/longest-substring-without-repeating-characters/" target="_blank">LeetCode 3</a> | Medium | Track duplicate presence in a growing window, shrink to restore uniqueness. |
| 12 | Minimum Window Substring | <a href="https://leetcode.com/problems/minimum-window-substring/" target="_blank">LeetCode 76</a> | Hard | Track required frequency satisfaction over a variable window, find minimum valid span. |

<details>
<summary>💡 Reveal Pattern Hints (Click after attempting from a blank editor)</summary>

- **Problem 11:** The dominant mechanism is a variable Sliding Window (`left`, `right`) where a `HashSet` tracks character membership. Shrink `left` when a duplicate enters. Classification: **Hashing + Sliding Window**.
- **Problem 12:** Maintain two frequency maps: one for required counts (`t`), one for current window counts. Track how many distinct characters are "satisfied". Expand right; once all satisfied, shrink left. Classification: **Hashing + Sliding Window (Hard)**.
</details>

---

## 🏆 Mastery Criteria

You have mastered **Pattern #2** when you can:

- [ ] Choose between `int[26]`, `int[128]`, `HashMap`, and `HashSet` based on the problem constraint.
- [ ] Build a frequency array in one pass and query it in $O(1)$.
- [ ] Implement bidirectional 1-to-1 character mapping validation.
- [ ] Construct a canonical grouping key from a string's character composition.
- [ ] Combine frequency hashing with greedy logic (parity-based palindrome construction).
- [ ] Understand that Hashing in a Sliding Window is a supporting role, not the primary pattern.
- [ ] Implement all 12 solutions from a blank editor without tutorial dependence.

---

## ➡️ Next Step

Once character hashing feels automatic, move to **[Pattern 03: Palindrome Patterns](./Pattern-03-Palindrome-Patterns.md)** to learn how the structural symmetry of palindromes dictates whether you use Two Pointers, Expand Around Center, or Dynamic Programming.
