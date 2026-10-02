# Pattern 3: Palindrome Patterns

> A palindrome is not a single algorithm. It is a structural property that dictates whether you move pointers inward (verification), expand outward (discovery), or use dynamic programming (subsequences).

---

## 🔍 Beginner Audit: What Is a Palindrome?

A palindrome reads the same forwards and backwards.

- `"racecar"` → palindrome
- `"abc"` → not a palindrome
- `"A man a plan a canal Panama"` → palindrome (after ignoring spaces and case)

> **Critical Insight:** The palindrome definition is simple, but the *technique you use to verify, discover, count, or construct one is entirely determined by the problem's constraints.*

---

## Core Technical Intuition — Three Structural Approaches

```text
╔══════════════════════════════════════════════════════════════╗
║  QUESTION                     → TECHNIQUE                   ║
╠══════════════════════════════════════════════════════════════╣
║  Is this string a palindrome? → Two Pointers (inward)       ║
║  Find the longest palindrome  → Expand Around Center        ║
║  Count all palindromes        → Expand Around Center        ║
║  Palindrome subsequence?      → Dynamic Programming         ║
╚══════════════════════════════════════════════════════════════╝
```

### Why Three Different Techniques?

**Inward Two Pointers** — You already know if all characters exist. You just need to verify symmetry.
```text
  ← right        left →
"r  a  c  e  c  a  r"
  ↑                 ↑
left=0           right=6
```

**Expand Around Center** — You don't know where palindromes are. For each center (odd or even), you expand outward while characters match.
```text
      center
        ↓
"a  b  a  c  a  b  a"
      ←   →
   expand until mismatch
```

**Dynamic Programming** — You need non-contiguous characters (subsequences). Pointers and center-expansion break because characters can have gaps.

---

## ☕ Standard Java Code Templates

### Template 1: Two Pointers — Palindrome Verification
```java
// Use when: checking if an entire string (or cleaned version) is a palindrome
public boolean isPalindrome(String s) {
    int left = 0, right = s.length() - 1;
    while (left < right) {
        while (left < right && !Character.isLetterOrDigit(s.charAt(left))) left++;
        while (left < right && !Character.isLetterOrDigit(s.charAt(right))) right--;
        if (Character.toLowerCase(s.charAt(left)) != Character.toLowerCase(s.charAt(right)))
            return false;
        left++;
        right--;
    }
    return true;
}
```

### Template 2: Expand Around Center — Discovery
```java
// Use when: finding the longest palindromic substring
private int expand(String s, int left, int right) {
    while (left >= 0 && right < s.length() &&
           s.charAt(left) == s.charAt(right)) {
        left--;
        right++;
    }
    return right - left - 1; // length of palindrome found
}

public String longestPalindrome(String s) {
    int start = 0, maxLen = 1;
    for (int i = 0; i < s.length(); i++) {
        int odd  = expand(s, i, i);     // odd-length: single center
        int even = expand(s, i, i + 1); // even-length: two centers
        int best = Math.max(odd, even);
        if (best > maxLen) {
            maxLen = best;
            start = i - (best - 1) / 2;
        }
    }
    return s.substring(start, start + maxLen);
}
```

### Template 3: Greedy One-Skip — Near Palindrome Check
```java
// Use when: one deletion is allowed (Valid Palindrome II)
public boolean validPalindrome(String s) {
    int left = 0, right = s.length() - 1;
    while (left < right) {
        if (s.charAt(left) != s.charAt(right)) {
            // Try skipping left, or skipping right
            return isPalin(s, left + 1, right) || isPalin(s, left, right - 1);
        }
        left++; right--;
    }
    return true;
}

private boolean isPalin(String s, int l, int r) {
    while (l < r) {
        if (s.charAt(l) != s.charAt(r)) return false;
        l++; r--;
    }
    return true;
}
```

---

## 🔍 Step-by-Step Trace Table (Dry Run)

Tracing **Expand Around Center** on `s = "babad"`:

| Center `i` | Type | Left Expand Start | Right Expand Start | Characters Match? | Palindrome Found | Length |
|------------|------|-------------------|--------------------|-------------------|-----------------|--------|
| 0 (`'b'`) | Odd | 0 | 0 | `b==b` ✓, `out-of-bounds` | `"b"` | 1 |
| 0-1 | Even | 0 | 1 | `b==a`? ✗ | `""` | 0 |
| 1 (`'a'`) | Odd | 1 | 1 | `a==a` ✓, `b==b` ✓, OOB | `"bab"` | **3** |
| 1-2 | Even | 1 | 2 | `a==b`? ✗ | `""` | 0 |
| 2 (`'b'`) | Odd | 2 | 2 | `b==b` ✓, `a==a` ✓, OOB | `"aba"` | **3** |
| 2-3 | Even | 2 | 3 | `b==a`? ✗ | `""` | 0 |
| 3 (`'a'`) | Odd | 3 | 3 | `a==a` ✓, OOB | `"a"` | 1 |
| 4 (`'d'`) | Odd | 4 | 4 | `d==d` ✓, OOB | `"d"` | 1 |

**Result:** Longest palindromic substring is `"bab"` or `"aba"` (both length 3). Return `"bab"`.

---

## Pattern Recognition Layer

When you see a palindrome problem, walk through:

```text
Do I need to verify if the whole string (or filtered version) is a palindrome?
        ↓ Yes → Two Pointers (inward, with filtering)

Can I skip/modify one character and still be valid?
        ↓ Yes → Two Pointers + Greedy Branching (try skip-left OR skip-right)

Do I need to find the longest or count all contiguous palindromes in a string?
        ↓ Yes → Expand Around Center (2N-1 centers: odd + even)

Do I need palindromic subsequences (gaps allowed)?
        ↓ Yes → Dynamic Programming (the approach changes completely)
```

---

## Core Mental Models

- **Verification Invariant:** *"Left and right must always match. The moment they don't, there's no palindrome."*
- **Center Count:** *"A string of length N has exactly 2N-1 possible palindromic centers: N odd-length centers (each character) and N-1 even-length centers (each pair of adjacent characters)."*
- **Subsequence Escalation:** *"When contiguity is no longer required (gaps allowed), Two Pointers and Center Expansion break. The only correct approach is DP state over substring prefixes."*

---

## 🛑 Pattern Boundary

| Problem Requirement | Move To Pattern |
|---------------------|-----------------|
| Longest palindromic **subsequence** (gaps allowed) | **String DP — Subsequence Section** |
| Minimum insertions/deletions to make palindrome | **String DP** |
| Palindrome involving frequency counts (longest buildable palindrome) | **Pattern #2 — Character Frequency (Greedy Parity)** |
| Palindrome prefix alignment (KMP-based) | **Pattern #11 — String Pattern Matching** |

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
| 1 | Valid Palindrome | <a href="https://leetcode.com/problems/valid-palindrome/" target="_blank">LeetCode 125</a> | Easy | Two Pointers inward with character filtering and normalization. |
| 2 | Valid Palindrome II | <a href="https://leetcode.com/problems/valid-palindrome-ii/" target="_blank">LeetCode 680</a> | Easy | Greedy one-skip branching: upon mismatch, try skip-left OR skip-right. |

---

### 2. Core (3 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 3 | Palindrome Number | <a href="https://leetcode.com/problems/palindrome-number/" target="_blank">LeetCode 9</a> | Easy | Palindrome reasoning without string conversion. Reverse only the second half mathematically. |
| 4 | Longest Palindromic Substring | <a href="https://leetcode.com/problems/longest-palindromic-substring/" target="_blank">LeetCode 5</a> | Medium | Expand Around Center: iterate all 2N-1 centers, track max bounds. |
| 5 | Palindromic Substrings | <a href="https://leetcode.com/problems/palindromic-substrings/" target="_blank">LeetCode 647</a> | Medium | Same Expand Around Center logic: accumulate count instead of max length. |

---

### 3. Advanced (2 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 6 | Longest Palindromic Subsequence | <a href="https://leetcode.com/problems/longest-palindromic-subsequence/" target="_blank">LeetCode 516</a> | Medium | DP on non-contiguous characters: match adds 2, mismatch takes max of skipping left or right. |
| 7 | Minimum Insertion Steps to Make a String Palindrome | <a href="https://leetcode.com/problems/minimum-insertion-steps-to-make-a-string-palindrome/" target="_blank">LeetCode 1312</a> | Hard | DP bridge: minimum insertions = `N - LongestPalindromicSubsequence(N)`. |

---

### 4. Interview Recognition — Pattern Hidden

| # | Problem | LeetCode Link | Difficulty | Objective |
|---|---------|---------------|------------|-----------|
| 8 | Break a Palindrome | <a href="https://leetcode.com/problems/break-a-palindrome/" target="_blank">LeetCode 1328</a> | Medium | Given a palindromic string, replace one character to produce the lexicographically smallest non-palindrome. |

<details>
<summary>💡 Reveal Pattern Hint (Click after attempting from a blank editor)</summary>

- **Problem 8:** Use the palindrome structural property: the first half mirrors the second half. To minimize lexicographic value, scan the first half and replace the first non-`'a'` character with `'a'`. If all characters are `'a'` (or length 1), replace the last character with `'b'`. Classification: **Palindrome Structure + Greedy Reasoning**.
</details>

---

## 🏆 Mastery Criteria

You have mastered **Pattern #3** when you can:

- [ ] Implement Two Pointers palindrome verification with character filtering cleanly.
- [ ] Implement the greedy one-skip branching for near-palindromes.
- [ ] Implement Expand Around Center correctly for both odd and even length palindromes.
- [ ] Distinguish when to accumulate max length (longest) vs. count (all substrings).
- [ ] Recognize when a palindrome problem has become a DP subsequence problem.
- [ ] Explain the `2N-1 centers` insight clearly.
- [ ] Implement all 8 solutions from a blank editor without tutorial dependence.

---

## ➡️ Next Step

Once palindrome structures are clear, move to **[Pattern 04: Two Pointers on Strings](./Pattern-04-Two-Pointers-on-Strings.md)** to master opposite-direction and same-direction pointer mechanics on strings, multiple strings, and structured token parsing.
