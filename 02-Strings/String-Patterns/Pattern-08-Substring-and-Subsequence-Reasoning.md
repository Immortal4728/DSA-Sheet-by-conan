# Pattern 8: Substring & Subsequence Reasoning

> Before solving any sequence problem, establish whether the target is contiguous (substring) or gapped (subsequence). This single distinction determines whether you use Two Pointers, Expand Around Center, or Dynamic Programming.

---

## 🔍 Beginner Audit: Four Structural Definitions You Must Know

These definitions must become instinctive. Getting them wrong causes misclassification.

```text
┌─────────────────┬───────────────────────────────────────────────────┬──────────────────────────┐
│ Term            │ Structural Rule                                    │ Example in "abcde"       │
├─────────────────┼───────────────────────────────────────────────────┼──────────────────────────┤
│ Substring       │ Contiguous: chars must be adjacent                │ "bcd" ✓  "ace" ✗         │
│ Subsequence     │ Ordered but gapped: position must increase        │ "ace" ✓  "eca" ✗         │
│ Subarray        │ Contiguous block in an array (same as substring)  │ [1,2,3] from [0,1,2,3,4] │
│ Subset          │ Any elements, order ignored entirely              │ {a,c,e} = {e,a,c}        │
└─────────────────┴───────────────────────────────────────────────────┴──────────────────────────┘
```

> **Why it matters:** Every algorithmic technique that works for substrings (Sliding Window, KMP) will silently give **wrong answers** on subsequence problems. The moment you allow gaps, you need DP.

---

## Core Technical Intuition — When Does DP Become Necessary?

For **substrings**, you can always maintain a contiguous window — the validity of adding/removing one character is $O(1)$.

For **subsequences**, adding a gap means there is no single "window boundary" to manage. You must evaluate all possible ways to pick $k$ characters in order — this creates **overlapping subproblems** that require DP memoization.

```text
Substring "abc" in "xabcyz":
  Use a sliding window [left...right]. Expand right, shrink left. O(N)

Subsequence "ace" in "abcde":
  At each character in "abcde", you choose: include it or skip.
  At 'a': match or skip. At 'b': skip. At 'c': match or skip...
  → Overlapping decisions → DP.
```

---

## ☕ Standard Java Code Templates

### Template 1: Substring Exact Match (Naive — brute force baseline)
```java
// Use when: finding first occurrence of needle in haystack (Pattern 11 improves this with KMP)
public int strStr(String haystack, String needle) {
    for (int i = 0; i <= haystack.length() - needle.length(); i++) {
        if (haystack.startsWith(needle, i)) return i;
    }
    return -1;
}
```

### Template 2: Subsequence Two-Pointer Check
```java
// Use when: checking if s is a subsequence of t in O(N)
public boolean isSubsequence(String s, String t) {
    int i = 0, j = 0;
    while (i < s.length() && j < t.length()) {
        if (s.charAt(i) == t.charAt(j)) i++;
        j++;
    }
    return i == s.length();
}
```

### Template 3: Longest Common Subsequence (DP)
```java
// Use when: finding the LCS of two strings — the foundational string DP
public int longestCommonSubsequence(String s1, String s2) {
    int m = s1.length(), n = s2.length();
    int[][] dp = new int[m + 1][n + 1];
    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (s1.charAt(i - 1) == s2.charAt(j - 1))
                dp[i][j] = dp[i-1][j-1] + 1;       // match: extend
            else
                dp[i][j] = Math.max(dp[i-1][j], dp[i][j-1]); // mismatch: take best skip
        }
    }
    return dp[m][n];
}
```

### Template 4: Bucket Preprocessing for Repeated Queries
```java
// Use when: checking many subsequences against one large string (avoid O(N) per word)
Map<Character, List<Integer>> buckets = new HashMap<>();
for (int i = 0; i < s.length(); i++) {
    buckets.computeIfAbsent(s.charAt(i), k -> new ArrayList<>()).add(i);
}
// For each query word, binary search in buckets for the next valid index
```

---

## 🔍 Step-by-Step Trace Table (Dry Run)

Tracing **Longest Common Subsequence (`LeetCode 1143`)** on `s1 = "abcd"`, `s2 = "acbd"`:

|       | `""` | `'a'` | `'c'` | `'b'` | `'d'` |
|-------|------|-------|-------|-------|-------|
| `""`  | 0 | 0 | 0 | 0 | 0 |
| `'a'` | 0 | **1** | 1 | 1 | 1 |
| `'b'` | 0 | 1 | 1 | **2** | 2 |
| `'c'` | 0 | 1 | **2** | 2 | 2 |
| `'d'` | 0 | 1 | 2 | 2 | **3** |

**Result:** LCS = `3` (the subsequence is `"abd"` or `"acd"`).

**Reading the table:** At `dp[i][j]`, if characters match → diagonal + 1. Else → max of left or top.

---

## Pattern Recognition Layer

```text
Is the problem about a contiguous block of characters?
        ↓ Yes → Substring
        → Two Pointers / Sliding Window / KMP (Pattern 4, 5, 11)

Can characters be in any non-contiguous order-preserving positions?
        ↓ Yes → Subsequence
        → Two Pointers (simple check) / DP (count or length or transformation)

Do I need to compare across two strings simultaneously?
        ↓ Yes → LCS-family DP (match = diagonal + 1, mismatch = max of left/top)

Am I running many subsequence queries against the same string?
        ↓ Yes → Preprocess the string into character position buckets; binary search
```

---

## Core Mental Models

- **The Contiguity Test:** *"Ask yourself: can I remove any character from the target sequence without affecting validity? If yes, it's a subsequence. If no, it's a substring."*
- **LCS Recurrence:** *"When characters match at `s1[i-1]` and `s2[j-1]`, extend the LCS of both prefixes. When they don't match, take the best of skipping one character from either string."*
- **Palindromic Subsequence Reduction:** *"The longest palindromic subsequence of `s` equals the LCS of `s` and its reverse `reverse(s)`. This converts a palindrome problem into a standard LCS problem."*

---

## 🛑 Pattern Boundary

| Problem Requirement | Move To Pattern |
|---------------------|-----------------|
| Contiguous palindromes (find or count) | **Pattern #3 — Palindrome Patterns** |
| Exact substring search with $O(N+M)$ | **Pattern #11 — String Pattern Matching** |
| Optimal contiguous window | **Pattern #5 — String Sliding Window** |
| Full String DP curriculum (space optimization, reconstruction) | **Dedicated Dynamic Programming Section** |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 3 |
| **Medium** | 5 |
| **Hard** | 2 |
| **Total** | **10** |

---

## 🎯 Question Progression (Curated 10-Question Set)

### 1. Foundation (2 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 1 | Find the Index of the First Occurrence in a String | <a href="https://leetcode.com/problems/find-the-index-of-the-first-occurrence-in-a-string/" target="_blank">LeetCode 28</a> | Easy | Substring (contiguous): every character of needle must appear in exact adjacent order. |
| 2 | Is Subsequence | <a href="https://leetcode.com/problems/is-subsequence/" target="_blank">LeetCode 392</a> | Easy | Subsequence (gapped): two-pointer greedy check — `j` always advances, `i` advances on match. |

---

### 2. Core (3 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 3 | Longest Common Prefix | <a href="https://leetcode.com/problems/longest-common-prefix/" target="_blank">LeetCode 14</a> | Easy | Multi-string vertical scanning: compare characters column-by-column until a mismatch. |
| 4 | Number of Matching Subsequences | <a href="https://leetcode.com/problems/number-of-matching-subsequences/" target="_blank">LeetCode 792</a> | Medium | Bucket preprocessing: group query words by next needed character; scan `s` once. |
| 5 | Longest Common Subsequence | <a href="https://leetcode.com/problems/longest-common-subsequence/" target="_blank">LeetCode 1143</a> | Medium | LCS DP foundation: `dp[i][j]` = LCS of `s1[0..i-1]` and `s2[0..j-1]`. Match → diagonal+1. |

---

### 3. Advanced (3 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 6 | Longest Palindromic Subsequence | <a href="https://leetcode.com/problems/longest-palindromic-subsequence/" target="_blank">LeetCode 516</a> | Medium | Structural reduction: LCS of `s` and `reverse(s)` gives the longest palindromic subsequence. |
| 7 | Distinct Subsequences | <a href="https://leetcode.com/problems/distinct-subsequences/" target="_blank">LeetCode 115</a> | Hard | Count distinct subsequences of `t` in `s`: on match, add paths from both using and skipping. |
| 8 | Shortest Common Supersequence | <a href="https://leetcode.com/problems/shortest-common-supersequence/" target="_blank">LeetCode 1092</a> | Hard | Build shortest string containing both as subsequences: find LCS, then merge non-LCS chars. |

---

### 4. Interview Recognition — Pattern Hidden

| # | Problem | LeetCode Link | Difficulty | Objective |
|---|---------|---------------|------------|-----------|
| 9 | Edit Distance | <a href="https://leetcode.com/problems/edit-distance/" target="_blank">LeetCode 72</a> | Medium | Find minimum insert, delete, or replace operations to convert `word1` to `word2`. |
| 10 | Interleaving String | <a href="https://leetcode.com/problems/interleaving-string/" target="_blank">LeetCode 97</a> | Medium | Determine if `s3` can be formed by interleaving characters of `s1` and `s2` in order. |

<details>
<summary>💡 Reveal Pattern Hints (Click after attempting from a blank editor)</summary>

- **Problem 9:** At each position `(i, j)`, you have 3 choices: insert, delete, or replace. `dp[i][j]` = minimum edits to convert `word1[0..i-1]` to `word2[0..j-1]`. Match → `dp[i-1][j-1]`. Mismatch → `1 + min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1])`. Classification: **2D String DP (Structural Transformation State)**.
- **Problem 10:** `dp[i][j]` = can `s3[0..i+j-1]` be formed from `s1[0..i-1]` and `s2[0..j-1]`. Check if `s3[i+j-1]` matches `s1[i-1]` (take from s1) OR `s2[j-1]` (take from s2). Classification: **2D Boolean DP (Dual-Prefix Partition State)**.
</details>

---

## 🏆 Mastery Criteria

You have mastered **Pattern #8** when you can:

- [ ] Instantly classify any sequence problem as substring or subsequence by asking the contiguity question.
- [ ] Implement the two-pointer subsequence check in $O(N)$ from memory.
- [ ] Implement the LCS `dp[i][j]` table with the correct match/mismatch recurrence.
- [ ] Reduce palindromic subsequence problems to LCS on reversed string.
- [ ] Recognize when overlapping subproblems require memoization.
- [ ] Preprocess a string into character buckets for efficient repeated subsequence queries.
- [ ] Implement all 10 solutions from a blank editor without tutorial dependence.

---

## ➡️ Next Step

Once substring/subsequence classification is automatic, move to **[Pattern 09: Stack-Based String Problems](./Pattern-09-Stack-Based-String-Problems.md)** to learn how to model LIFO unresolved state in string problems involving nested structures, cascading cancellations, and expression evaluation.
