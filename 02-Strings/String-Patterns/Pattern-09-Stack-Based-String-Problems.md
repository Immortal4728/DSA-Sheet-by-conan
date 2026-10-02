# Pattern 9: Stack-Based String Problems

> When the most recently seen unresolved information must be processed first, a stack is the correct data structure — not because "parentheses = stack", but because the problem has **LIFO (Last-In-First-Out) resolution order**.

---

## 🔍 Beginner Audit: What Makes a Problem "Stack-Based"?

When you traverse a string left-to-right, you sometimes encounter characters that cannot be evaluated until you read more characters later. This is **deferred resolution**.

The question that determines if you need a stack:

> **"Does the most recently unresolved piece of information need to be resolved first?"**

- **Parentheses:** The most recently opened `(` must be closed before any outer `(` can close. → **LIFO → Stack.**
- **Backspace:** The most recently typed character is deleted first. → **LIFO → Stack (or reverse Two Pointers).**
- **Decode String `3[abc]`:** You must finish decoding the innermost bracket before appending to the outer context. → **LIFO → Stack.**

> **Common Mistake:** Seeing a parenthesis and immediately reaching for a stack template. Instead, ask: *"What state is unresolved? Does the most recent one get resolved first?"* A simple balanced parenthesis count only needs an integer counter — not a stack.

---

## Core Technical Intuition — LIFO State Modeling

```text
LIFO Deferred Resolution:

Input: "3[a2[bc]]"

Traverse:
  See '3'   → push multiplier 3 to stack
  See '['   → push current string "" to stack
  See 'a'   → current = "a"
  See '2'   → push multiplier 2
  See '['   → push current string "a"
  See 'b'   → current = "b"
  See 'c'   → current = "bc"
  See ']'   → pop multiplier 2, pop saved "a"
              current = "a" + "bc"*2 = "abcbc"
  See ']'   → pop multiplier 3, pop saved ""
              current = "" + "abcbc"*3 = "abcbcabcbcabcbc"

Result: "abcbcabcbcabcbc"
```

The innermost bracket resolved first → LIFO → stack of saved contexts.

---

## ☕ Standard Java Code Templates

### Template 1: Bracket Matching (Valid Parentheses)
```java
// Use when: validating matching of multiple bracket types
Deque<Character> stack = new ArrayDeque<>();
for (char c : s.toCharArray()) {
    if (c == '(' || c == '[' || c == '{') {
        stack.push(c);
    } else {
        if (stack.isEmpty()) return false;
        char top = stack.pop();
        if (c == ')' && top != '(') return false;
        if (c == ']' && top != '[') return false;
        if (c == '}' && top != '{') return false;
    }
}
return stack.isEmpty();
```

### Template 2: Character Cancellation (Adjacent Duplicates)
```java
// Use when: collapsing adjacent duplicate characters
StringBuilder sb = new StringBuilder();
for (char c : s.toCharArray()) {
    if (sb.length() > 0 && sb.charAt(sb.length() - 1) == c) {
        sb.deleteCharAt(sb.length() - 1); // cancel: pop last char
    } else {
        sb.append(c);                     // push: add to stack
    }
}
return sb.toString();
```

### Template 3: K-Adjacent Cancellation (with count tracking)
```java
// Use when: removing K consecutive identical characters
Deque<int[]> stack = new ArrayDeque<>(); // stores [char_as_int, count]
for (char c : s.toCharArray()) {
    if (!stack.isEmpty() && stack.peek()[0] == c) {
        stack.peek()[1]++;                       // extend run
        if (stack.peek()[1] == k) stack.pop();  // cancel K-run
    } else {
        stack.push(new int[]{c, 1});            // start new run
    }
}
StringBuilder sb = new StringBuilder();
for (int[] pair : stack) {
    for (int i = 0; i < pair[1]; i++) sb.append((char) pair[0]);
}
return sb.reverse().toString();
```

### Template 4: Nested State Decoding (Decode String)
```java
// Use when: decoding nested repetition structures like "3[a2[bc]]"
Deque<Integer> countStack = new ArrayDeque<>();
Deque<StringBuilder> stringStack = new ArrayDeque<>();
StringBuilder current = new StringBuilder();
int k = 0;
for (char c : s.toCharArray()) {
    if (Character.isDigit(c)) {
        k = k * 10 + (c - '0');          // accumulate multi-digit numbers
    } else if (c == '[') {
        countStack.push(k);               // save multiplier
        stringStack.push(current);        // save current context
        current = new StringBuilder();    // start fresh inside bracket
        k = 0;
    } else if (c == ']') {
        int count = countStack.pop();
        StringBuilder prev = stringStack.pop();
        prev.append(current.toString().repeat(count)); // expand
        current = prev;
    } else {
        current.append(c);
    }
}
return current.toString();
```

---

## 🔍 Step-by-Step Trace Table (Dry Run)

Tracing **Remove All Adjacent Duplicates In String (`LeetCode 1047`)** on `s = "abbaca"`:

| Step | Char `c` | Stack (as string) | Action |
|------|----------|-------------------|--------|
| 1 | `'a'` | `""` | stack empty → push → `"a"` |
| 2 | `'b'` | `"a"` | top `'a'` ≠ `'b'` → push → `"ab"` |
| 3 | `'b'` | `"ab"` | top `'b'` == `'b'` → **cancel** → `"a"` |
| 4 | `'a'` | `"a"` | top `'a'` == `'a'` → **cancel** → `""` |
| 5 | `'c'` | `""` | stack empty → push → `"c"` |
| 6 | `'a'` | `"c"` | top `'c'` ≠ `'a'` → push → `"ca"` |

**Result:** `"ca"` ✓

---

## Pattern Recognition Layer

```text
Does the problem require information to be resolved in reverse order of arrival?
        ↓ Yes → Stack (LIFO)

Is there a matching requirement (open → must close in reverse)?
        ↓ Yes → Bracket Matching Stack (Template 1)

Do adjacent identical characters cancel each other in pairs?
        ↓ Yes → Character Cancellation via StringBuilder-as-Stack (Template 2)

Do K consecutive identical characters trigger a cancellation?
        ↓ Yes → K-Adjacent Cancellation with (char, count) pair stack (Template 3)

Are there nested repetition structures with multipliers?
        ↓ Yes → Dual-Stack Decode (countStack + stringStack) (Template 4)

Is there a backspace character in the string?
        ↓ Can use stack, but consider: reverse Two Pointers achieves O(1) space (Pattern #4)
```

---

## Core Mental Models

- **LIFO Resolution:** *"A stack is correct when: the information you need to resolve a current character was left unresolved earlier AND it must be resolved in reverse order of how it was saved."*
- **StringBuilder as Stack:** *"In cancellation problems, `StringBuilder` acts as an implicit stack — `append()` is push, `deleteCharAt(last)` is pop. This avoids explicit `Deque` overhead."*
- **Dual Stack for Nested Context:** *"When encountering `[`, you have two pieces of saved state: (1) the multiplier, (2) the string built so far outside this bracket. Push both. Pop both on `]`."*
- **Integer Counter vs. Stack:** *"For simple balanced parenthesis counting (not multiple types, no state), a single `int counter` is faster than a Stack. Only use a Stack when you need to save multi-dimensional state."*

---

## 🛑 Pattern Boundary

| Problem Requirement | Move To Pattern |
|---------------------|-----------------|
| Backspace comparison in $O(1)$ space | **Pattern #4 — Two Pointers (reverse simulation)** |
| Contiguous substring optimization | **Pattern #5 — Sliding Window** |
| Math expression evaluation with full precedence and parentheses | **Pattern #10 — String Parsing & Simulation** |
| Non-contiguous character decisions | **Pattern #8 — Subsequence Reasoning / DP** |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 4 |
| **Medium** | 3 |
| **Hard** | 0 |
| **Total** | **7** |

---

## 🎯 Question Progression (Curated 8-Question Set)

### 1. Foundation (2 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 1 | Valid Parentheses | <a href="https://leetcode.com/problems/valid-parentheses/" target="_blank">LeetCode 20</a> | Easy | Bracket matching: push on open, pop and validate on close. |
| 2 | Remove All Adjacent Duplicates In String | <a href="https://leetcode.com/problems/remove-all-adjacent-duplicates-in-string/" target="_blank">LeetCode 1047</a> | Easy | Single cancellation: use StringBuilder as a stack — append or cancel the last character. |

---

### 2. Core (3 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 3 | Backspace String Compare | <a href="https://leetcode.com/problems/backspace-string-compare/" target="_blank">LeetCode 844</a> | Easy | Reversible state: Stack simulates naturally; reverse Two Pointers achieves $O(1)$ space. |
| 4 | Make The String Great | <a href="https://leetcode.com/problems/make-the-string-great/" target="_blank">LeetCode 1544</a> | Easy | Condition-based cancellation: same stack mechanic as LC 1047 but condition is case-polarity difference. |
| 5 | Remove All Adjacent Duplicates in String II | <a href="https://leetcode.com/problems/remove-all-adjacent-duplicates-in-string-ii/" target="_blank">LeetCode 1209</a> | Medium | K-adjacent cancellation: store `(character, count)` pairs — pop when count reaches `K`. |

---

### 3. Advanced (2 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 6 | Decode String | <a href="https://leetcode.com/problems/decode-string/" target="_blank">LeetCode 394</a> | Medium | Dual-stack decode: save multiplier AND current string on `[`, expand and merge on `]`. |
| 7 | Basic Calculator II | <a href="https://leetcode.com/problems/basic-calculator-ii/" target="_blank">LeetCode 227</a> | Medium | Deferred operations: push operands as positive/negative integers; multiply/divide immediately. |

---

### 4. Interview Recognition — Pattern Hidden

| # | Problem | LeetCode Link | Difficulty | Objective |
|---|---------|---------------|------------|-----------|
| 8 | Simplify Path | <a href="https://leetcode.com/problems/simplify-path/" target="_blank">LeetCode 71</a> | Medium | Simplify a Unix file path string by resolving `.`, `..`, and empty segments. |

<details>
<summary>💡 Reveal Pattern Hint (Click after attempting from a blank editor)</summary>

- **Problem 8:** Split the path by `'/'`. For each token: `"."` = do nothing. `".."` = pop the last valid directory from the stack. Empty string = skip. Any other name = push to stack. Join the remaining stack with `'/'`. Classification: **Stack-Based Parsing + Directory State Modeling**.
</details>

---

## 🏆 Mastery Criteria

You have mastered **Pattern #9** when you can:

- [ ] Identify LIFO resolution order without seeing explicit bracket structures.
- [ ] Implement bracket matching with multiple bracket types cleanly.
- [ ] Use `StringBuilder` as an implicit stack for character cancellation.
- [ ] Track `(character, count)` pairs for K-adjacent cascading cancellations.
- [ ] Implement dual-stack decoding for nested repetition structures.
- [ ] Know when a simple integer counter replaces a stack for balanced parentheses.
- [ ] Implement all 8 solutions from a blank editor without tutorial dependence.

---

## ➡️ Next Step

Once stack-based string mechanics are solid, move to **[Pattern 10: String Parsing & Simulation](./Pattern-10-String-Parsing-and-Simulation.md)** to learn how to interpret strict textual rule systems and continuously update state while reading an input string.
