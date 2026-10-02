# Pattern 7: String Construction & Transformation

> When a problem asks you to build, encode, transform, or simulate string output through strict rules — you need explicit state control, not language abstractions.

---

## 🔍 Beginner Audit: What Is "Construction" vs. "Transformation"?

- **Construction:** Building a new string from scratch following arithmetic or structural rules — e.g., adding two number-strings digit-by-digit.
- **Transformation:** Modifying an existing string to produce a new form — e.g., reversing the order of words, compressing repeated characters.

> **Critical Habit:** These problems tempt you to use `Integer.parseInt()`, `BigInteger`, or `String.split()`. Resist. In interviews, these are disallowed or penalize space complexity. Learn to manage carry states, read/write pointers, and delimiter protocols manually.

---

## Core Technical Intuition — Explicit State Machines

Construction problems share one pattern: the output is determined by processing the input through a **sequence of well-defined states**. Your job is to identify those states.

```text
Example: String to Integer (atoi)

Input: "   -42abc"

State 1: Skip whitespace   →  "   " consumed
State 2: Parse sign        →  '-' consumed, sign = -1
State 3: Accumulate digits →  '4', '2' consumed, result = 42
State 4: Hit non-digit     →  'a' stops parsing
Output:  -42 (clamped to bounds)
```

This sequential state machine is the backbone of most parsing/transformation problems.

---

## ☕ Standard Java Code Templates

### Template 1: Right-to-Left Carry Arithmetic (Add Strings / Add Binary)
```java
// Use when: adding two number-strings digit by digit without converting to int
int i = s1.length() - 1, j = s2.length() - 1, carry = 0;
StringBuilder sb = new StringBuilder();
while (i >= 0 || j >= 0 || carry > 0) {
    int sum = carry;
    if (i >= 0) sum += (s1.charAt(i--) - '0');  // extract digit from s1
    if (j >= 0) sum += (s2.charAt(j--) - '0');  // extract digit from s2
    sb.append(sum % base);   // base = 10 for Add Strings, 2 for Add Binary
    carry = sum / base;
}
return sb.reverse().toString();
```

### Template 2: State Machine Parsing (atoi)
```java
// Use when: parsing a string with strict sequential rules (whitespace → sign → digits → stop)
public int myAtoi(String s) {
    int i = 0, n = s.length(), sign = 1, result = 0;
    while (i < n && s.charAt(i) == ' ') i++;                  // State 1: skip spaces
    if (i < n && (s.charAt(i) == '+' || s.charAt(i) == '-'))  // State 2: parse sign
        sign = (s.charAt(i++) == '-') ? -1 : 1;
    while (i < n && Character.isDigit(s.charAt(i))) {          // State 3: accumulate
        int digit = s.charAt(i++) - '0';
        if (result > (Integer.MAX_VALUE - digit) / 10)         // State 4: overflow check
            return sign == 1 ? Integer.MAX_VALUE : Integer.MIN_VALUE;
        result = result * 10 + digit;
    }
    return sign * result;
}
```

### Template 3: In-Place Read/Write Pointer (String Compression)
```java
// Use when: compressing or modifying a char[] in-place without extra space
public int compress(char[] chars) {
    int read = 0, write = 0;
    while (read < chars.length) {
        char cur = chars[read];
        int count = 0;
        while (read < chars.length && chars[read] == cur) { read++; count++; } // read run
        chars[write++] = cur;                                                    // write char
        if (count > 1)
            for (char c : String.valueOf(count).toCharArray()) chars[write++] = c; // write count
    }
    return write;
}
```

### Template 4: Length-Prefix Encoding (Encode & Decode)
```java
// Use when: serializing strings to a single string with unambiguous delimiter-free boundaries
public String encode(List<String> strs) {
    StringBuilder sb = new StringBuilder();
    for (String s : strs) sb.append(s.length()).append('#').append(s); // "4#code3#art"
    return sb.toString();
}
public List<String> decode(String s) {
    List<String> result = new ArrayList<>();
    int i = 0;
    while (i < s.length()) {
        int j = s.indexOf('#', i);
        int len = Integer.parseInt(s.substring(i, j));
        result.add(s.substring(j + 1, j + 1 + len));
        i = j + 1 + len;
    }
    return result;
}
```

---

## 🔍 Step-by-Step Trace Table (Dry Run)

Tracing **Add Strings (`LeetCode 415`)** on `s1 = "456"`, `s2 = "77"`, base = 10:

| Step | `i` | `j` | `s1[i]` | `s2[j]` | `sum` | `carry` | Output digit |
|------|-----|-----|---------|---------|-------|---------|--------------|
| 1 | 2 | 1 | `'6'`→6 | `'7'`→7 | 0+6+7=13 | 1 | `3` |
| 2 | 1 | 0 | `'5'`→5 | `'7'`→7 | 1+5+7=13 | 1 | `3` |
| 3 | 0 | -1 | `'4'`→4 | — | 1+4=5 | 0 | `5` |

`sb = "335"` → reversed → **`"533"`**

---

## Pattern Recognition Layer

```text
Am I asked to add, multiply, or compare numbers stored as strings?
        ↓ Yes → Right-to-left carry arithmetic (Template 1)

Does the problem require reading an input through multiple strict phases?
(whitespace → sign → digits → overflow → stop)
        ↓ Yes → State Machine Parsing (Template 2)

Do I need to modify a char[] or string buffer in-place without extra space?
        ↓ Yes → Read/Write Pointer (Template 3)

Do I need to serialize/deserialize a list of strings through a single string?
        ↓ Yes → Length-Prefix Encoding Protocol (Template 4)

Does a structured pattern exist (e.g., zigzag rows, direction cycles)?
        ↓ Yes → Simulate the structure directly using a direction flag or index formula
```

---

## Core Mental Models

- **No Language Abstractions:** *"Never use `Integer.parseInt()` or `BigInteger` in interviews for arithmetic string problems. Implement carry manually — this is what the interviewer is testing."*
- **State Machine Mindset:** *"For any parsing problem, write down the ordered phases first: (1) skip leading garbage, (2) detect special characters, (3) accumulate meaningful content, (4) stop on boundary. Code follows directly."*
- **Read/Write Separation:** *"When modifying a string in-place, separate concerns: `read` pointer discovers information, `write` pointer records results. They advance at different rates."*
- **Length-Prefix Solves Delimiter Ambiguity:** *"Any character can appear inside a payload string. A length-prefix like `5#hello` encodes exactly how many characters to read — no need to scan for a delimiter inside the payload."*

---

## 🛑 Pattern Boundary

| Problem Requirement | Move To Pattern |
|---------------------|-----------------|
| Token comparison over a moving window | **Pattern #5 — Sliding Window** |
| Palindrome structure or symmetry | **Pattern #3 — Palindrome Patterns** |
| Non-contiguous subsequence construction | **Pattern #8 — Subsequence Reasoning / DP** |
| Stack-required nested structures | **Pattern #9 — Stack-Based String Problems** |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 2 |
| **Medium** | 5 |
| **Hard** | 0 |
| **Total** | **7** |

---

## 🎯 Question Progression (Curated 8-Question Set)

### 1. Foundation (2 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 1 | Add Strings | <a href="https://leetcode.com/problems/add-strings/" target="_blank">LeetCode 415</a> | Easy | Right-to-left dual pointer carry: base-10 digit-by-digit addition without integer conversion. |
| 2 | Add Binary | <a href="https://leetcode.com/problems/add-binary/" target="_blank">LeetCode 67</a> | Easy | Same dual-pointer carry template, now in base-2 (`sum % 2`, `sum / 2`). |

---

### 2. Core (3 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 3 | String to Integer (atoi) | <a href="https://leetcode.com/problems/string-to-integer-atoi/" target="_blank">LeetCode 8</a> | Medium | State machine: whitespace → sign → digit accumulation → overflow clamping. |
| 4 | Multiply Strings | <a href="https://leetcode.com/problems/multiply-strings/" target="_blank">LeetCode 43</a> | Medium | Positional arithmetic: digit at `s1[i]` × digit at `s2[j]` contributes to output index `i + j + 1`. |
| 5 | Reverse Words in a String | <a href="https://leetcode.com/problems/reverse-words-in-a-string/" target="_blank">LeetCode 151</a> | Medium | Token transformation: reverse full string → reverse each word → strip extra spaces. |

---

### 3. Advanced (2 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 6 | Encode and Decode Strings | <a href="https://leetcode.com/problems/encode-and-decode-strings/" target="_blank">LeetCode 271</a> | Medium | Length-prefix encoding protocol: format as `len#string` to ensure unambiguous decoding. |
| 7 | Zigzag Conversion | <a href="https://leetcode.com/problems/zigzag-conversion/" target="_blank">LeetCode 6</a> | Medium | Structural direction simulation: a direction flag (`down`/`up`) maps each character to a row buffer. |

---

### 4. Interview Recognition — Pattern Hidden

| # | Problem | LeetCode Link | Difficulty | Objective |
|---|---------|---------------|------------|-----------|
| 8 | String Compression | <a href="https://leetcode.com/problems/string-compression/" target="_blank">LeetCode 443</a> | Medium | Compress repeated adjacent characters in-place, writing the character and its run-length count. |

<details>
<summary>💡 Reveal Pattern Hint (Click after attempting from a blank editor)</summary>

- **Problem 8:** Use a `read` pointer to scan and count consecutive character runs. Use a `write` pointer to overwrite the array in-place with the character and then each digit of the count. Both pointers advance at different rates. Classification: **In-Place Read/Write Pointer Compression**.
</details>

---

## 🏆 Mastery Criteria

You have mastered **Pattern #7** when you can:

- [ ] Implement right-to-left carry arithmetic for arbitrary base without `parseInt`.
- [ ] Implement a sequential state machine parser with overflow bounds checking.
- [ ] Use positional digit multiplication with an output index formula.
- [ ] Implement a read/write pointer for in-place buffer compression.
- [ ] Design a length-prefix encoding that survives any payload content.
- [ ] Simulate a structural layout (like Zigzag) using a direction flag.
- [ ] Implement all 8 solutions from a blank editor without tutorial dependence.

---

## ➡️ Next Step

Once construction mechanics feel solid, move to **[Pattern 08: Substring & Subsequence Reasoning](./Pattern-08-Substring-and-Subsequence-Reasoning.md)** to master the most critical structural distinction in string problems: contiguous vs. non-contiguous sequences.
