# Pattern 04: Stack + Expression Processing

> Mathematical expression parsing requires evaluating operands according to operator precedence and nested parentheses. A stack manages operand stacks, pending operator state, and saved calculation context across nested sub-expressions.

---

## Why This Pattern Exists

Human-readable arithmetic expressions are written in **infix notation** (`a + b * c`), where operator precedence (`*`, `/` over `+`, `-`) and parentheses override simple left-to-right evaluation.

Computers evaluate expressions either by:
1. Converting infix to **postfix (Reverse Polish Notation)** where operators follow operands, eliminating parentheses.
2. Maintaining an intermediate **stack of pending values and signs** during a single pass over an infix expression.

This pattern demonstrates the natural progression:
$$\text{Postfix Evaluation (LC 150)} \longrightarrow \text{Precedence Handling (LC 227)} \longrightarrow \text{Parentheses \& Nested State (LC 224)}$$

---

## Progression & Core Mechanics

### Level 1: Postfix Evaluation (RPN)
- Operands are pushed onto stack.
- When an operator is encountered, pop two operands ($b$ then $a$), apply operation ($a \text{ op } b$), and push result.

### Level 2: Operator Precedence (No Parentheses)
- Multiplications and divisions are evaluated immediately with the preceding number.
- Additions and subtractions are deferred by pushing signed values onto the stack.
- Final answer is the sum of all stack values.

### Level 3: Nested Parentheses & Sub-Expressions
- When `(` is reached, push the currently accumulated `result` and `sign` onto the stack, then reset `result` and `sign`.
- When `)` is reached, pop `sign` and `previous_result`, combining the completed inner result:
  $$\text{result} = \text{previous\_result} + (\text{sign} \times \text{inner\_result})$$

---

## ☕ Standard Java Templates

### Basic Calculator (Parentheses & Signs)
```java
public int calculate(String s) {
    Deque<Integer> stack = new ArrayDeque<>();
    int result = 0;
    int number = 0;
    int sign = 1; // 1 for +, -1 for -

    for (int i = 0; i < s.length(); i++) {
        char c = s.charAt(i);
        if (Character.isDigit(c)) {
            number = number * 10 + (c - '0');
        } else if (c == '+') {
            result += sign * number;
            number = 0;
            sign = 1;
        } else if (c == '-') {
            result += sign * number;
            number = 0;
            sign = -1;
        } else if (c == '(') {
            // Save current result and sign onto stack
            stack.push(result);
            stack.push(sign);
            // Reset for inside parentheses
            result = 0;
            sign = 1;
        } else if (c == ')') {
            result += sign * number;
            number = 0;
            result *= stack.pop(); // Multiply by saved sign
            result += stack.pop(); // Add saved result before '('
        }
    }
    return result + (sign * number);
}
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Decode String** | Belongs **exclusively to Pattern 02** (nested scope & string replication, not arithmetic expression processing). |
| **Valid Parentheses** | Pure syntax matching without numerical operand state — belongs to **Pattern 02**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Medium** | 2 |
| **Hard** | 1 |
| **Total** | **3** |

---

## 🎯 Question Set (3 Questions)

### Q1. Evaluate Reverse Polish Notation
<a href="https://leetcode.com/problems/evaluate-reverse-polish-notation/" target="_blank">LeetCode 150</a> — **Medium**

**Target Skill:** Postfix expression evaluation using operand stack.

**Core Reasoning:**
- In RPN, operators follow their operands (e.g., `["2", "1", "+", "3", "*"]` $\to (2 + 1) * 3$).
- Iterate through tokens: if token is an integer, push to stack.
- If token is an operator (`+`, `-`, `*`, `/`), pop $op_2$, pop $op_1$, perform operation ($op_1 \text{ op } op_2$), and push result.
- Return final value in stack.

**Why it belongs here:** Entry point for expression processing. Demonstrates postfix stack mechanics without operator precedence or bracket complexities.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

### Q2. Basic Calculator II
<a href="https://leetcode.com/problems/basic-calculator-ii/" target="_blank">LeetCode 227</a> — **Medium**

**Target Skill:** Operator precedence resolution (`+`, `-`, `*`, `/`) using stack accumulation.

**Core Reasoning:**
- Expression contains non-negative integers and operators `+`, `-`, `*`, `/` (no parentheses).
- Maintain `currentNumber` and `lastOperator` (initialized to `+`).
- When encountering an operator or end of string:
  - If `+`: push `+currentNumber`.
  - If `-`: push `-currentNumber`.
  - If `*`: pop stack, multiply by `currentNumber`, push result.
  - If `/`: pop stack, divide by `currentNumber`, push result.
- Sum all elements in stack at the end.

**Why it belongs here:** Teaches how stack deferred evaluation resolves binary operator precedence ($O(1)$ stack space variant also exists).

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

### Q3. Basic Calculator
<a href="https://leetcode.com/problems/basic-calculator/" target="_blank">LeetCode 224</a> — **Hard**

**Target Skill:** Full expression evaluation with unary signs, addition, subtraction, and nested parentheses.

**Core Reasoning:**
- Expression contains `+`, `-`, `(`, `)`, spaces, and digits.
- Track running `result`, current `number`, and current `sign` ($+1$ or $-1$).
- On `(`: push `result` and `sign` onto stack. Reset `result = 0`, `sign = 1`.
- On `)`: finalize current sub-expression `result += sign * number`, then apply popped `sign` and add popped `previousResult`.

**Why it belongs here:** The peak of expression processing. Integrates stack sign preservation with scope nesting.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

## ⚡ Mastery Checklist

- [ ] Do you know why RPN (postfix) needs no parentheses or operator precedence checks?
- [ ] Can you evaluate multiplication/division immediately while deferring addition/subtraction?
- [ ] How do you handle unary negation (e.g., `- (3 + 2)`) using a sign multiplier stack?
- [ ] Can you implement `Basic Calculator` in a single $O(N)$ pass?
