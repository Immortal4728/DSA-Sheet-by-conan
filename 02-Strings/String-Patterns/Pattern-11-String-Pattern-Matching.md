# Pattern 11: String Pattern Matching

> Never re-evaluate a character whose structural relationship to the pattern is already known. The LPS array is the engine that makes this possible.

---

## 🔍 Beginner Audit: Why Does Naive Search Fail?

The naive approach: for each starting index `i` in the text, check if `pattern` starts there. If any character mismatches, restart from `i+1` in the text and `0` in the pattern.

**The problem:** When you restart from pattern index 0, you throw away all information about characters you already matched successfully.

```text
Text:    "A A A A A A A B"
Pattern: "A A A A B"

Naive at i=0: A A A A (match) ... B vs A (mismatch!) → restart from i=1
Naive at i=1: A A A A (match) ... B vs A (mismatch!) → restart from i=2
...repeat N times...
Total: O(N × M) comparisons

KMP at mismatch after 4 A's:
  LPS says: "the first 3 of the 4 matched A's form a valid prefix too"
  → shift only as far as necessary, continue from position 3 in pattern
Total: O(N + M) comparisons
```

> **Key Insight:** The Longest Prefix Suffix (LPS) array precomputes how much of the pattern's prefix can be reused after a mismatch. This eliminates all redundant re-scanning.

---

## Core Technical Intuition — LPS Array & Z-Array

### The LPS Array (Prefix Function / Failure Function)

`LPS[i]` = the length of the longest proper prefix of `pattern[0..i]` that is also a suffix of `pattern[0..i]`.

```text
pattern = "A B C A B D"

Index:     0  1  2  3  4  5
Char:      A  B  C  A  B  D
LPS:       0  0  0  1  2  0

LPS[4] = 2 because "AB" is both a prefix and suffix of "ABCAB"

On mismatch at pattern index 5 (after matching "ABCAB"):
  → Don't restart from 0. Fall back to LPS[4] = 2.
  → Continue matching from pattern index 2.
```

### The Z-Array (Z-Function)

`Z[i]` = the length of the longest substring starting at `s[i]` that is also a prefix of `s`.

```text
s = "AABXAA"
Z:   0  1  0  0  2  1

Z[4] = 2 because s[4..5] = "AA" matches prefix s[0..1] = "AA"
```

**Use Case:** Concatenate `pattern + "#" + text`, compute Z-array. Any index where `Z[i] == len(pattern)` is a match position.

---

## ☕ Standard Java Code Templates

### Template 1: Build LPS Array (KMP Preprocessing)
```java
// This is the core of KMP — must be memorizable
private int[] buildLPS(String pattern) {
    int n = pattern.length();
    int[] lps = new int[n];
    int len = 0; // length of previous longest prefix-suffix
    int i = 1;
    while (i < n) {
        if (pattern.charAt(i) == pattern.charAt(len)) {
            lps[i++] = ++len;
        } else if (len > 0) {
            len = lps[len - 1]; // fall back (do NOT increment i)
        } else {
            lps[i++] = 0;
        }
    }
    return lps;
}
```

### Template 2: KMP Search (Using LPS Array)
```java
// Use when: finding all occurrences of pattern in text in O(N+M)
public List<Integer> kmpSearch(String text, String pattern) {
    List<Integer> result = new ArrayList<>();
    int[] lps = buildLPS(pattern);
    int i = 0, j = 0; // i: text pointer, j: pattern pointer
    while (i < text.length()) {
        if (text.charAt(i) == pattern.charAt(j)) {
            i++; j++;
        }
        if (j == pattern.length()) {
            result.add(i - j);     // full match found
            j = lps[j - 1];       // look for next match
        } else if (i < text.length() && text.charAt(i) != pattern.charAt(j)) {
            if (j > 0) j = lps[j - 1]; // fall back
            else i++;                   // no fallback available, advance text
        }
    }
    return result;
}
```

### Template 3: Build Z-Array
```java
// Use when: finding prefix matches at every position in O(N)
private int[] buildZArray(String s) {
    int n = s.length();
    int[] z = new int[n];
    int l = 0, r = 0;
    for (int i = 1; i < n; i++) {
        if (i < r) z[i] = Math.min(r - i, z[i - l]);
        while (i + z[i] < n && s.charAt(z[i]) == s.charAt(i + z[i])) z[i]++;
        if (i + z[i] > r) { l = i; r = i + z[i]; }
    }
    return z;
}
```

---

## 🔍 Step-by-Step Trace Table (Dry Run)

Tracing **buildLPS** on `pattern = "AABAAB"`:

| `i` | `len` | `pattern[i]` | `pattern[len]` | Match? | Action | `lps[i]` |
|-----|-------|--------------|----------------|--------|--------|----------|
| 1 | 0 | `'A'` | `'A'` | ✓ | `lps[1] = 1`, `i=2`, `len=1` | **1** |
| 2 | 1 | `'B'` | `'A'` | ✗ | `len = lps[0] = 0` | — |
| 2 | 0 | `'B'` | `'A'` | ✗ | `lps[2] = 0`, `i=3` | **0** |
| 3 | 0 | `'A'` | `'A'` | ✓ | `lps[3] = 1`, `i=4`, `len=1` | **1** |
| 4 | 1 | `'A'` | `'A'` | ✓ | `lps[4] = 2`, `i=5`, `len=2` | **2** |
| 5 | 2 | `'B'` | `'B'` | ✓ | `lps[5] = 3`, `i=6`, `len=3` | **3** |

**LPS = `[0, 1, 0, 1, 2, 3]`**

Interpretation: `LPS[5] = 3` means `"AAB"` is both a proper prefix and suffix of `"AABAAB"`.

---

## Pattern Recognition Layer

```text
Am I searching for an exact contiguous pattern inside a larger string?
        ↓ Yes → Build LPS → KMP Search. O(N + M).

Does the problem ask for the longest proper prefix that is also a suffix?
        ↓ Yes → Run the LPS builder only. The answer is LPS[N-1].

Does the problem involve periodicity? (Is string formed by repeating a substring?)
        ↓ Yes → Check: N % (N - LPS[N-1]) == 0

Does the problem ask for lengths of longest prefix match starting at every index?
        ↓ Yes → Z-Array. O(N).

Is a palindromic prefix or suffix involved?
        ↓ Yes → Concatenate s + "#" + reverse(s), run LPS. LPS[last] gives palindromic prefix length.
```

---

## Core Mental Models

- **Never Restart from Zero:** *"The entire point of KMP is to use LPS to determine the furthest back you must fall in the pattern on a mismatch — not always 0."*
- **LPS Holds Periodicity:** *"If `N % (N - LPS[N-1]) == 0`, the string is built from a repeated unit of length `N - LPS[N-1]`. This emerges directly from the prefix-suffix structure."*
- **Z-Function is LPS in Disguise:** *"The Z-array answers 'how long is the prefix match at every position'. The LPS array answers 'what fallback position keeps the most context'. Both avoid $O(NM)$ re-scanning — they just approach it differently."*

---

## 🛑 Pattern Boundary

| Problem Requirement | Move To Pattern |
|---------------------|-----------------|
| Non-contiguous matching (subsequences) | **Pattern #8 — Subsequence Reasoning / DP** |
| Searching multiple different words simultaneously | **Pattern #12 — Advanced String Structures (Trie + Aho-Corasick)** |
| Wildcard or regex matching (`*`, `?`) | **String DP** |
| Simple single-character boundary scan | **Pattern #1 — String Traversal** |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 1 |
| **Medium** | 1 |
| **Hard** | 4 |
| **Total** | **6** |

---

## 🎯 Question Progression (Curated 6-Question Set)

> *This section strictly limits questions. We do not add duplicate KMP implementations. Each problem introduces a distinct structural concept.*

### 1. Foundation (2 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 1 | Find the Index of the First Occurrence in a String | <a href="https://leetcode.com/problems/find-the-index-of-the-first-occurrence-in-a-string/" target="_blank">LeetCode 28</a> | Easy | Understand naive $O(NM)$ failure. Implement LPS array. Implement KMP search in $O(N+M)$. |
| 2 | Longest Happy Prefix | <a href="https://leetcode.com/problems/longest-happy-prefix/" target="_blank">LeetCode 1392</a> | Hard | Pure LPS extraction: find the longest proper prefix that is also a suffix. Forces LPS builder mastery independent of the search phase. |

---

### 2. Core (2 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 3 | Repeated Substring Pattern | <a href="https://leetcode.com/problems/repeated-substring-pattern/" target="_blank">LeetCode 459</a> | Easy | KMP application: a string is formed by a repeated unit iff `N % (N - LPS[N-1]) == 0`. |
| 4 | Sum of Scores of Built Strings | <a href="https://leetcode.com/problems/sum-of-scores-of-built-strings/" target="_blank">LeetCode 2223</a> | Hard | Z-Array definition: compute and sum lengths of longest prefix matches at every suffix start. |

---

### 3. Interview Recognition — Pattern Hidden

| # | Problem | LeetCode Link | Difficulty | Objective |
|---|---------|---------------|------------|-----------|
| 5 | Shortest Palindrome | <a href="https://leetcode.com/problems/shortest-palindrome/" target="_blank">LeetCode 214</a> | Hard | Find the shortest palindrome by adding characters only to the front of the string. |
| 6 | Repeated String Match | <a href="https://leetcode.com/problems/repeated-string-match/" target="_blank">LeetCode 686</a> | Medium | Find the minimum times `a` must be repeated so that `b` is a substring of the repetition. |

<details>
<summary>💡 Reveal Pattern Hints (Click after attempting from a blank editor)</summary>

- **Problem 5:** Find the longest palindromic prefix of `s`. Concatenate `s + "#" + reverse(s)` and run the LPS builder. `LPS[last]` gives the length of the longest palindromic prefix. Prepend `reverse(s[LPS[last]..end])` to `s`. Classification: **Hidden LPS Boundary — KMP Preprocessing**.
- **Problem 6:** The minimum repetitions needed is `ceil(len(b) / len(a))`. You may need one more repeat for alignment. Use KMP to verify if `b` is a substring of the constructed repeated string. Classification: **Hidden KMP Search with Mathematical Bound on Repetitions**.
</details>

---

## 🏆 Mastery Criteria

You have mastered **Pattern #11** when you can:

- [ ] Explain why naive $O(NM)$ search wastes work on repetitive inputs.
- [ ] Implement the LPS builder (`buildLPS`) from memory without reference.
- [ ] Implement full KMP search using LPS fallback logic.
- [ ] Compute the Z-array and explain what `Z[i]` represents.
- [ ] Use `N % (N - LPS[N-1]) == 0` to detect periodicity.
- [ ] Construct the `s + "#" + reverse(s)` trick to find palindromic prefixes.
- [ ] Implement all 6 solutions from a blank editor without tutorial dependence.

---

## ➡️ Next Step

Once KMP and the Z-Function feel clear, move to **[Pattern 12: Advanced String Structures](./Pattern-12-Advanced-String-Structures.md)** to learn when HashMaps are insufficient for prefix-based lookups and how Tries exploit shared prefixes to eliminate redundant work.
