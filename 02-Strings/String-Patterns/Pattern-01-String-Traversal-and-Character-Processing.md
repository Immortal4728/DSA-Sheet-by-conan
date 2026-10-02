# Pattern 1: String Traversal & Character Processing

> Scan a string sequentially to inspect, filter, normalize, or construct output — one character at a time.

---

## 🔍 Beginner Audit: What Makes String Traversal Different from Array Traversal?

- **Array Traversal:** Iterate over `nums[i]` — values are numbers with no inherent structural rules.
- **String Traversal:** Iterate over `s.charAt(i)` — values are characters with rules about case, whitespace, alphanumeric membership, and boundaries.

> **Key Distinction:** Java Strings are **immutable**. You cannot modify `s.charAt(i)` in-place. To modify characters, convert to a `char[]` first. To build a new string, always use `StringBuilder`.

---

## Core Technical Intuition — Character-Level Scanning

String traversal is about answering three questions before you write a single line of code:

1. **What do I inspect?** — individual `char` values, sequences, or boundaries (like spaces).
2. **What do I filter or normalize?** — skip non-alphanumeric characters, convert to lowercase, etc.
3. **How do I produce the output?** — return a `boolean`, return an `int` index, or construct a new `String` via `StringBuilder`.

```text
s = "Hello World!"

      H  e  l  l  o     W  o  r  l  d  !
      0  1  2  3  4  5  6  7  8  9  10 11
      ↑                                ↑
     i=0  ──────► scan ──────────►   i=11
```

By maintaining a single index `i`, you read every character in $O(N)$ and never need to re-scan.

---

## ☕ Standard Java Code Templates

### Template 1: Simple Traversal (Read-Only)
```java
// Use when: counting, searching, or validating characters
for (int i = 0; i < s.length(); i++) {
    char c = s.charAt(i);
    // Process character c
}
```

### Template 2: In-Place Modification via `char[]`
```java
// Use when: reversing, swapping, or overwriting characters
char[] arr = s.toCharArray();
int left = 0, right = arr.length - 1;
while (left < right) {
    char temp = arr[left];
    arr[left] = arr[right];
    arr[right] = temp;
    left++;
    right--;
}
String result = new String(arr);
```

### Template 3: Output Construction via `StringBuilder`
```java
// Use when: building a filtered, transformed, or combined string
StringBuilder sb = new StringBuilder();
for (int i = 0; i < s.length(); i++) {
    char c = s.charAt(i);
    if (Character.isLetter(c)) {
        sb.append(Character.toLowerCase(c)); // filter + normalize
    }
}
return sb.toString();
```

### Template 4: Boundary Detection (Trailing / Leading Tokens)
```java
// Use when: finding word boundaries, lengths from the back
int i = s.length() - 1;
while (i >= 0 && s.charAt(i) == ' ') i--; // skip trailing spaces
int count = 0;
while (i >= 0 && s.charAt(i) != ' ') { count++; i--; } // count word
```

---

## 🔍 Step-by-Step Trace Table (Dry Run)

Tracing **Length of Last Word (`LeetCode 58`)** on `s = "Hello World   "`:

| Phase | Index `i` | `s.charAt(i)` | Action | `count` |
|-------|-----------|---------------|--------|---------|
| Skip trailing spaces | 13 | `' '` | skip | 0 |
| Skip trailing spaces | 12 | `' '` | skip | 0 |
| Skip trailing spaces | 11 | `' '` | skip | 0 |
| Count word chars | 10 | `'d'` | count++ | 1 |
| Count word chars | 9 | `'l'` | count++ | 2 |
| Count word chars | 8 | `'r'` | count++ | 3 |
| Count word chars | 7 | `'o'` | count++ | 4 |
| Count word chars | 6 | `'W'` | count++ | 5 |
| Hit space | 5 | `' '` | stop | **5** |

**Result:** `5` (length of `"World"`).

---

## Pattern Recognition Layer

When you see a string problem, walk through this checklist:

```text
Does every character need to be visited at least once?
        ↓
Is there a single filtering or normalization condition?
        ↓
Is the result a boolean, index, count, or new string?
        ↓
Can it be done in one pass without maintaining a range?
        ↓
→ If YES to all: Basic String Traversal
```

---

## Core Mental Models

- **Immutability Rule:** *"Java Strings cannot be modified. Always decide upfront: `char[]` for in-place swap, `StringBuilder` for construction."*
- **Boundary Detection:** *"Whitespace, case changes, and non-alphanumeric characters are natural string boundaries. Always scan explicitly to handle them."*
- **Single-Pass Principle:** *"If the result only depends on each character independently, one pass is always sufficient."*

---

## 🛑 Pattern Boundary (When to Move Beyond Basic Traversal)

| Problem Requirement | Move To Pattern |
|---------------------|-----------------|
| Counting character **frequencies** | **Pattern #2 — Character Frequency & Hashing** |
| Verifying or finding **palindromes** | **Pattern #3 — Palindrome Patterns** |
| Comparing **opposite ends** or two strings | **Pattern #4 — Two Pointers on Strings** |
| Finding **optimal contiguous substrings** | **Pattern #5 — String Sliding Window** |
| Processing **nested structures** (brackets) | **Pattern #9 — Stack-Based String Problems** |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 5 |
| **Medium** | 1 |
| **Hard** | 0 |
| **Total** | **6** |

---

## 🎯 Question Progression (Curated 6-Question Set)

### 1. Foundation (2 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 1 | Reverse String | <a href="https://leetcode.com/problems/reverse-string/" target="_blank">LeetCode 344</a> | Easy | In-place `char[]` swap using two indices. |
| 2 | Valid Palindrome | <a href="https://leetcode.com/problems/valid-palindrome/" target="_blank">LeetCode 125</a> | Easy | Character filtering (`isAlphanumeric`) and case normalization during traversal. |

---

### 2. Core (3 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 3 | Length of Last Word | <a href="https://leetcode.com/problems/length-of-last-word/" target="_blank">LeetCode 58</a> | Easy | Trailing whitespace boundary detection. Traversal from the back. |
| 4 | Reverse Words in a String III | <a href="https://leetcode.com/problems/reverse-words-in-a-string-iii/" target="_blank">LeetCode 557</a> | Easy | Identify word boundaries (spaces) and reverse each word segment independently. |
| 5 | Goal Parser Interpretation | <a href="https://leetcode.com/problems/goal-parser-interpretation/" target="_blank">LeetCode 1678</a> | Easy | Conditional look-ahead during traversal to match multi-character tokens. |

---

### 3. Interview Recognition — Pattern Hidden

| # | Problem | LeetCode Link | Difficulty | Objective |
|---|---------|---------------|------------|-----------|
| 6 | Find First Palindromic String in the Array | <a href="https://leetcode.com/problems/find-first-palindromic-string-in-the-array/" target="_blank">LeetCode 2108</a> | Easy | Traverse an array of strings and validate a localized character property. No complex state needed. |

<details>
<summary>💡 Reveal Pattern Hint (Click after attempting from a blank editor)</summary>

- **Problem 6:** For each string in the array, use a two-pointer palindrome check (`left` and `right` inward). No HashMap, no sorting. Return the first string where the check passes.
</details>

---

## 🏆 Mastery Criteria

You have mastered **Pattern #1** when you can:

- [ ] Traverse a string confidently using `charAt(i)` or `toCharArray()`.
- [ ] Decide upfront when to use `char[]` (in-place) vs. `StringBuilder` (construction).
- [ ] Handle leading, trailing, and internal whitespace boundaries cleanly.
- [ ] Normalize characters using `Character.isLetter()`, `Character.isDigit()`, `Character.toLowerCase()`.
- [ ] Detect token boundaries (words, segments) from either direction.
- [ ] Recognize when a problem is simple traversal vs. when it actually needs Hashing, Window, or Stack.
- [ ] Implement all 6 solutions from a blank editor without tutorial dependence.

---

## ➡️ Next Step

Once single-pass traversal feels automatic, move to **[Pattern 02: Character Frequency & Hashing](./Pattern-02-Character-Frequency-and-Hashing.md)** to learn how to store and retrieve character state in $O(1)$ time using frequency arrays and hash maps.
