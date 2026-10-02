# Pattern 4: Two Pointers on Strings

> Maintain two index variables moving deterministically across a string (or two strings) to align, compare, filter, or parse characters without nested loops.

---

## 🔍 Beginner Audit: What Is a "Pointer" in String Patterns?

A **pointer** is just an integer index into a string — `int left = 0` or `int right = s.length() - 1`. It is not a memory address.

The power is in how you move the pointers:
- **Opposite-direction:** Start at both ends, move inward. Used for palindromes, reversal, and pair validation.
- **Same-direction (two strings):** `i` tracks String A, `j` tracks String B. Each advances based on conditional matching. Used for subsequences, merging, and comparing.
- **Same-direction (fast/slow):** `fast` scans ahead, `slow` holds a valid write position. Used for in-place filtering.

> **Transfer from Arrays:** You already used Two Pointers in Array Pattern #5 for pair sums and deduplication. On Strings, the logic is identical — you just replace numerical comparisons with character comparisons and add filtering conditions (e.g., skip spaces or non-alphanumeric characters).

---

## Core Technical Intuition — Deterministic Pointer Movement

The key question for every Two Pointers string problem:

> *"Can one pointer's movement deterministically eliminate possibilities based on character comparisons?"*

```text
Opposite-direction (Palindrome Check):
   "r  a  c  e  c  a  r"
    ↑                 ↑
  left=0           right=6
  match? → both move inward
  mismatch? → return false immediately

Same-direction (Subsequence):
   s = "ace"   t = "abcde"
        ↑             ↑
       i=0           j=0
  s[i]==t[j]? → i++, j++
  else         → j++ only
  Win: i == s.length() → all matched
```

---

## ☕ Standard Java Code Templates

### Template 1: Opposite-Direction (Palindrome / Reversal)
```java
// Use when: validating palindromes, reversing vowels, comparing from both ends
int left = 0, right = s.length() - 1;
while (left < right) {
    // Optional: skip/filter characters
    while (left < right && !Character.isLetterOrDigit(s.charAt(left))) left++;
    while (left < right && !Character.isLetterOrDigit(s.charAt(right))) right--;
    if (s.charAt(left) != s.charAt(right)) return false;
    left++;
    right--;
}
return true;
```

### Template 2: Same-Direction (Two Strings — Subsequence)
```java
// Use when: checking if s is a subsequence of t, merging two strings
int i = 0, j = 0;
while (i < s.length() && j < t.length()) {
    if (s.charAt(i) == t.charAt(j)) {
        i++; // advance s pointer only on a match
    }
    j++; // always advance t pointer
}
return i == s.length(); // all of s was matched
```

### Template 3: Reverse Simulation (Backspace / Skip-State)
```java
// Use when: simulating operations from the back avoids extra space (Backspace Compare)
int i = s.length() - 1, j = t.length() - 1;
int skipS = 0, skipT = 0;
while (i >= 0 || j >= 0) {
    while (i >= 0) {
        if (s.charAt(i) == '#') { skipS++; i--; }
        else if (skipS > 0)     { skipS--; i--; }
        else break;
    }
    while (j >= 0) {
        if (t.charAt(j) == '#') { skipT++; j--; }
        else if (skipT > 0)     { skipT--; j--; }
        else break;
    }
    if (i >= 0 && j >= 0 && s.charAt(i) != t.charAt(j)) return false;
    if ((i >= 0) != (j >= 0)) return false;
    i--; j--;
}
return true;
```

---

## 🔍 Step-by-Step Trace Table (Dry Run)

Tracing **Is Subsequence (`LeetCode 392`)** on `s = "ace"`, `t = "abcde"`:

| Step | `i` | `j` | `s[i]` | `t[j]` | Match? | Action |
|------|-----|-----|---------|---------|--------|--------|
| 1 | 0 | 0 | `'a'` | `'a'` | ✓ | `i++`, `j++` |
| 2 | 1 | 1 | `'c'` | `'b'` | ✗ | `j++` only |
| 3 | 1 | 2 | `'c'` | `'c'` | ✓ | `i++`, `j++` |
| 4 | 2 | 3 | `'e'` | `'d'` | ✗ | `j++` only |
| 5 | 2 | 4 | `'e'` | `'e'` | ✓ | `i++`, `j++` |
| End | 3 | 5 | — | — | `i == s.length()` | **Return `true`** |

---

## Pattern Recognition Layer

```text
Do I need to validate or compare a string from both ends simultaneously?
        ↓ Yes → Opposite-direction pointers

Am I aligning or matching characters across two different strings?
        ↓ Yes → Same-direction independent pointers (i on S, j on T)

Am I filtering characters and writing valid ones to a position?
        ↓ Yes → Fast/Slow same-direction pointers (fast reads, slow writes)

Am I processing operations that can be undone by scanning backwards?
        ↓ Yes → Reverse simulation with a skip-count state variable
```

---

## Core Mental Models

- **Opposite Ends:** *"When the answer depends on comparing characters at symmetric positions, start from the extremes and move inward."*
- **Independent Progression:** *"When processing two strings that advance at different rates, each pointer follows its own conditional rule. Never link them to move at the same speed."*
- **Reverse Simulation:** *"When a forward simulation requires a stack, ask if traversing backwards with a skip-count eliminates the need for $O(N)$ extra space."*

---

## 🛑 Pattern Boundary

| Problem Requirement | Move To Pattern |
|---------------------|-----------------|
| Finding optimal **contiguous** substrings | **Pattern #5 — String Sliding Window** |
| Counting or finding palindromic **substrings** | **Pattern #3 — Palindrome Patterns** |
| Parsing nested structures (brackets, expressions) | **Pattern #9 — Stack-Based String Problems** |
| Finding longest common **subsequence** | **Pattern #8 — Substring & Subsequence Reasoning** |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 6 |
| **Medium** | 3 |
| **Hard** | 1 |
| **Total** | **10** |

---

## 🎯 Question Progression (Curated 10-Question Set)

### 1. Foundation (2 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 1 | Valid Palindrome | <a href="https://leetcode.com/problems/valid-palindrome/" target="_blank">LeetCode 125</a> | Easy | Opposite-direction pointers with `isLetterOrDigit` filtering and case normalization. |
| 2 | Reverse Vowels of a String | <a href="https://leetcode.com/problems/reverse-vowels-of-a-string/" target="_blank">LeetCode 345</a> | Easy | Conditional advancement: advance each pointer until it points to a vowel, then swap. |

---

### 2. Core (4 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 3 | Valid Palindrome II | <a href="https://leetcode.com/problems/valid-palindrome-ii/" target="_blank">LeetCode 680</a> | Easy | Greedy branching on mismatch: try skip-left OR skip-right independently. |
| 4 | Is Subsequence | <a href="https://leetcode.com/problems/is-subsequence/" target="_blank">LeetCode 392</a> | Easy | Two-string traversal: `j` always advances, `i` advances only on match. |
| 5 | Merge Strings Alternately | <a href="https://leetcode.com/problems/merge-strings-alternately/" target="_blank">LeetCode 1768</a> | Easy | Two independent pointers interleaving characters from strings of unequal lengths. |
| 6 | Backspace String Compare | <a href="https://leetcode.com/problems/backspace-string-compare/" target="_blank">LeetCode 844</a> | Easy | Reverse simulation: traverse backwards, use skip-count for `'#'` backspaces in $O(1)$ space. |

---

### 3. Advanced (2 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 7 | Compare Version Numbers | <a href="https://leetcode.com/problems/compare-version-numbers/" target="_blank">LeetCode 165</a> | Medium | Parse integer tokens between `.` delimiters using two independent pointers without `split()`. |
| 8 | Boats to Save People | <a href="https://leetcode.com/problems/boats-to-save-people/" target="_blank">LeetCode 881</a> | Medium | Opposite-direction greedy: pair the heaviest with the lightest; if combined weight exceeds limit, heaviest goes alone. |

---

### 4. Interview Recognition — Pattern Hidden

| # | Problem | LeetCode Link | Difficulty | Objective |
|---|---------|---------------|------------|-----------|
| 9 | One Edit Distance | <a href="https://leetcode.com/problems/one-edit-distance/" target="_blank">LeetCode 161</a> | Medium | Determine if two strings are exactly one insert, delete, or replace away from each other. |
| 10 | Shortest Palindrome | <a href="https://leetcode.com/problems/shortest-palindrome/" target="_blank">LeetCode 214</a> | Hard | Find the shortest palindrome by prepending characters to a given string. |

<details>
<summary>💡 Reveal Pattern Hints (Click after attempting from a blank editor)</summary>

- **Problem 9:** Compare character-by-character. When a mismatch is found, use string lengths to decide whether to advance one pointer (insert/delete) or both (replace), then check if the remaining substrings are identical. Classification: **Two Pointers + String Length Reasoning**.
- **Problem 10:** Find the longest palindromic prefix, then prepend the reverse of the remaining suffix. The optimal $O(N)$ approach uses KMP — connect to **Pattern #11 (String Pattern Matching)**.
</details>

---

## 🏆 Mastery Criteria

You have mastered **Pattern #4** when you can:

- [ ] Implement opposite-direction pointers with conditional character filtering.
- [ ] Implement same-direction two-string traversal with independent advancement.
- [ ] Implement reverse simulation with skip-count tracking.
- [ ] Parse structured string tokens manually without using `.split()`.
- [ ] Handle strings of unequal lengths without index-out-of-bounds errors.
- [ ] Recognize when reverse traversal eliminates the need for a stack.
- [ ] Implement all 10 solutions from a blank editor without tutorial dependence.

---

## ➡️ Next Step

Once Two Pointer mechanics feel natural on strings, move to **[Pattern 05: String Sliding Window](./Pattern-05-String-Sliding-Window.md)** to learn how to maintain dynamic state across a contiguous moving substring in $O(N)$ time.
