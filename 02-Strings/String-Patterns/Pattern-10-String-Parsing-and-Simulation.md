# Pattern 10: String Parsing & Simulation

> Parsing problems require you to separate two distinct concerns: **"How do I interpret the rules of this input format?"** and **"How do I update state as I read it?"** Conflating these two causes bugs.

---

## 🔍 Beginner Audit: Parsing vs. Construction vs. Stack

These three String patterns are often confused. Here is the clear distinction:

| Pattern | Core Mechanic | Example |
|---------|--------------|---------|
| **Pattern #7 — Construction** | Build a new string following math/structural rules | Add two number-strings digit by digit |
| **Pattern #9 — Stack** | Resolve nested or LIFO-deferred state | Decode `3[ab]`, balance brackets |
| **Pattern #10 — Parsing** | Read a rule-governed input and update continuous state | Robot movement, version comparison, calculator |

> **Key Insight:** Parsing is about **interpreting symbols**. Each character in the input carries a meaning defined by the problem's rules. Your job is to read that meaning, update a state variable, and handle boundaries.

---

## Core Technical Intuition — State Machine Model

Every parsing problem is a hidden **state machine**. Before writing code, identify:

```text
1. CURRENT TOKEN   — What am I reading? (digit, space, operator, letter, delimiter?)
2. CURRENT STATE   — What context am I in? (inside a word? after a sign? inside parentheses?)
3. TRANSITION      — How does this token change my state?
4. TERMINATION     — When is a token complete and ready to be processed?
5. EDGE CASES      — Empty input? Trailing character? Leading noise?
```

Example for `atoi("   -42abc")`:
```text
State: LEADING_SPACES  → consume " " → transition to SIGN
State: SIGN            → consume "-" → transition to DIGITS, sign = -1
State: DIGITS          → consume "4","2" → result = 42
State: STOP            → consume "a" → non-digit stops everything
Output: -42
```

---

## ☕ Standard Java Code Templates

### Template 1: Trailing Boundary Token Scan (Length of Last Word)
```java
// Use when: finding a token from the back, ignoring trailing delimiters
int i = s.length() - 1;
while (i >= 0 && s.charAt(i) == ' ') i--;  // skip trailing spaces
int count = 0;
while (i >= 0 && s.charAt(i) != ' ') { count++; i--; } // count word chars
return count;
```

### Template 2: Symbol Interpretation Map (Goal Parser)
```java
// Use when: characters map to multi-character outputs based on look-ahead
StringBuilder sb = new StringBuilder();
for (int i = 0; i < command.length(); i++) {
    if (command.charAt(i) == 'G') {
        sb.append('G');
    } else if (command.charAt(i) == '(' && command.charAt(i + 1) == ')') {
        sb.append('o'); i++;            // consume both '(' and ')'
    } else {
        sb.append("al"); i += 3;       // consume "(al)"
    }
}
return sb.toString();
```

### Template 3: Continuous State Update Simulation (Robot)
```java
// Use when: each character maps to an action that updates a running state
int x = 0, y = 0;
for (char c : moves.toCharArray()) {
    if      (c == 'U') y++;
    else if (c == 'D') y--;
    else if (c == 'L') x--;
    else if (c == 'R') x++;
}
return x == 0 && y == 0;
```

### Template 4: Delimiter-Based Numeric Parsing (Version Numbers)
```java
// Use when: comparing/parsing structured numeric tokens separated by delimiters
int i = 0, j = 0, n = version1.length(), m = version2.length();
while (i < n || j < m) {
    int v1 = 0, v2 = 0;
    while (i < n && version1.charAt(i) != '.') v1 = v1 * 10 + (version1.charAt(i++) - '0');
    while (j < m && version2.charAt(j) != '.') v2 = v2 * 10 + (version2.charAt(j++) - '0');
    if (v1 > v2) return 1;
    if (v1 < v2) return -1;
    i++; j++; // skip '.'
}
return 0;
```

---

## 🔍 Step-by-Step Trace Table (Dry Run)

Tracing **Robot Return to Origin (`LeetCode 657`)** on `moves = "UDLR"`:

| Step | Char | `x` before | `y` before | Action | `x` after | `y` after |
|------|------|-------------|-------------|--------|------------|------------|
| 1 | `'U'` | 0 | 0 | `y++` | 0 | 1 |
| 2 | `'D'` | 0 | 1 | `y--` | 0 | 0 |
| 3 | `'L'` | 0 | 0 | `x--` | -1 | 0 |
| 4 | `'R'` | -1 | 0 | `x++` | 0 | 0 |

**Final state:** `(0, 0)` → **Return `true`** (robot is back at origin).

---

## Pattern Recognition Layer

```text
Is the problem describing a rule system where each character/token has a defined action?
        ↓ Yes → Parsing & Simulation

Is the input delimited by a specific separator (spaces, '.', operators)?
        ↓ Yes → Delimiter-based token parsing (manual — do not use .split())

Does the problem require reading a character, then looking ahead to decide interpretation?
        ↓ Yes → Look-ahead symbol map (Template 2)

Does the problem update a running coordinate, direction, or count based on input symbols?
        ↓ Yes → Continuous state update simulation (Template 3)

Does the problem involve mathematical expressions with precedence?
        ↓ Yes → Stack-based expression evaluation (see Pattern #9)
```

---

## Core Mental Models

- **Separate Interpretation from Execution:** *"First decide what a symbol means (parse). Then decide what to do about it (execute). Writing these in one interleaved mess causes bugs. Keep them distinct."*
- **No Language Abstractions:** *"Never use `String.split('.')` for version parsing in interviews — `.` is a regex special character and the behavior can surprise you. Parse manually with index scanning."*
- **Boundary Handling First:** *"Always handle the trailing-boundary case (trailing spaces, trailing delimiter) before you start counting. It prevents off-by-one errors."*
- **State Machine Phases:** *"For complex parsers (atoi, calculators), write the phases as comments first: whitespace → sign → digits → stop. Then fill in code for each phase. Never free-code a parser."*

---

## 🛑 Pattern Boundary

| Problem Requirement | Move To Pattern |
|---------------------|-----------------|
| Backspace or undo operations (LIFO) | **Pattern #9 — Stack-Based String Problems** |
| Counting characters / frequencies | **Pattern #2 — Character Frequency & Hashing** |
| String construction / carry arithmetic | **Pattern #7 — String Construction & Transformation** |
| Nested parenthesis expression evaluation with full precedence | **Pattern #9 — Stack-Based String Problems** |

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
| 1 | Length of Last Word | <a href="https://leetcode.com/problems/length-of-last-word/" target="_blank">LeetCode 58</a> | Easy | Trailing boundary scan: skip trailing spaces, count backwards until next space. |
| 2 | Reverse Words in a String | <a href="https://leetcode.com/problems/reverse-words-in-a-string/" target="_blank">LeetCode 151</a> | Medium | Token extraction with variable whitespace: parse word boundaries, reverse their order. |

---

### 2. Core (3 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 3 | String to Integer (atoi) | <a href="https://leetcode.com/problems/string-to-integer-atoi/" target="_blank">LeetCode 8</a> | Medium | State machine: whitespace → sign → digits → overflow clamp → stop on non-digit. |
| 4 | Goal Parser Interpretation | <a href="https://leetcode.com/problems/goal-parser-interpretation/" target="_blank">LeetCode 1678</a> | Easy | Symbol interpretation: look-ahead to classify `G`, `()`, and `(al)` tokens. |
| 5 | Robot Return to Origin | <a href="https://leetcode.com/problems/robot-return-to-origin/" target="_blank">LeetCode 657</a> | Easy | Continuous state simulation: each character maps to a coordinate update rule. |

---

### 3. Advanced (2 Questions)

| # | Problem | LeetCode Link | Difficulty | Target Skill |
|---|---------|---------------|------------|--------------|
| 6 | Basic Calculator | <a href="https://leetcode.com/problems/basic-calculator/" target="_blank">LeetCode 224</a> | Hard | Expression parsing with parentheses: build multi-digit operands, toggle sign on `+/-`, save/restore sign context on `()/`. |
| 7 | Compare Version Numbers | <a href="https://leetcode.com/problems/compare-version-numbers/" target="_blank">LeetCode 165</a> | Medium | Delimiter-based numeric scan: parse integer chunks separated by `'.'` using manual index pointers. |

---

### 4. Interview Recognition — Pattern Hidden

| # | Problem | LeetCode Link | Difficulty | Objective |
|---|---------|---------------|------------|-----------|
| 8 | Evaluate Reverse Polish Notation | <a href="https://leetcode.com/problems/evaluate-reverse-polish-notation/" target="_blank">LeetCode 150</a> | Medium | Evaluate a postfix expression: numbers and operators in reverse-Polish order. |

<details>
<summary>💡 Reveal Pattern Hint (Click after attempting from a blank editor)</summary>

- **Problem 8:** When an operator is encountered, it acts on the two most recently seen operands. The most recently seen operand must be resolved first (LIFO). Push numbers onto a stack; on encountering an operator, pop two operands, apply the operator, push the result back. Classification: **Stack-Based Expression Evaluation** (the parsing is simple; the resolution is LIFO — connects to Pattern #9).
</details>

---

## 🏆 Mastery Criteria

You have mastered **Pattern #10** when you can:

- [ ] Identify the phases of a state machine parser before writing any code.
- [ ] Parse numeric tokens manually from a string without `.parseInt()` or `.split()`.
- [ ] Handle leading whitespace, trailing boundaries, and non-character tokens correctly.
- [ ] Implement a multi-digit number accumulator within a traversal loop.
- [ ] Update coordinates or direction state from a symbol map cleanly.
- [ ] Distinguish parsing problems from stack-required LIFO resolution problems.
- [ ] Implement all 8 solutions from a blank editor without tutorial dependence.

---

## ➡️ Next Step

Once parsing mechanics feel solid, move to **[Pattern 11: String Pattern Matching](./Pattern-11-String-Pattern-Matching.md)** to learn why naive $O(NM)$ substring search fails and how KMP and the Z-Function eliminate redundant re-evaluation of characters.
