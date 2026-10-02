# Pattern 02: Parentheses & Nested Structures

> Nested structures enforce strict scope boundaries. Outer scopes cannot close before inner scopes resolve. A stack naturally enforces this hierarchy by tracking opening boundaries and resolving them in reverse order of appearance (LIFO).

---

## Why This Pattern Exists

When processing structured strings (such as mathematical expressions, code syntax, nested encoding, or filesystem paths), elements exist within hierarchical scopes.

A stack allows us to:
- **Track Active Scope:** Push scope markers (like `(`, `[`, `{`, or directory names) when entering a deeper level.
- **Enforce Scope Closure:** Match closing markers (like `)`, `]`, `}`) strictly against the most recently opened scope.
- **Save & Restore State:** Store partial results or multipliers when entering nested blocks, and restore them when exiting (e.g., in `Decode String`).
- **Normalize Hierarchical Paths:** Process directory traversals (`.` and `..`) by popping the current folder on `..`.

---

## When to Use This Pattern

Use Parentheses & Nested Structures when:
- Evaluating syntax validity for paired brackets or tokens.
- Deleting minimum invalid brackets to restore structural balance.
- Processing nested expressions where inner computations modify outer contexts.
- Transforming hierarchical string paths into canonical representations.

---

## ☕ Standard Java Templates

### 1. Parentheses Matching
```java
Deque<Character> stack = new ArrayDeque<>();
for (char c : s.toCharArray()) {
    if (c == '(') stack.push(')');
    else if (c == '{') stack.push('}');
    else if (c == '[') stack.push(']');
    else {
        if (stack.isEmpty() || stack.pop() != c) return false;
    }
}
return stack.isEmpty();
```

### 2. Nested Decoding State Management
```java
Deque<Integer> countStack = new ArrayDeque<>();
Deque<StringBuilder> stringStack = new ArrayDeque<>();
StringBuilder currentString = new StringBuilder();
int k = 0;

for (char c : s.toCharArray()) {
    if (Character.isDigit(c)) {
        k = k * 10 + (c - '0');
    } else if (c == '[') {
        countStack.push(k);
        stringStack.push(currentString);
        currentString = new StringBuilder();
        k = 0;
    } else if (c == ']') {
        StringBuilder decoded = stringStack.pop();
        int count = countStack.pop();
        for (int i = 0; i < count; i++) decoded.append(currentString);
        currentString = decoded;
    } else {
        currentString.append(c);
    }
}
return currentString.toString();
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Basic Calculator** | Involves operator evaluation, signs, and arithmetic precedence — belongs to **Pattern 04**. |
| **Decode String** | Belongs **exclusively to Pattern 02** (nested scope & multiplier stacking), NOT Expression Processing. |
| **Remove All Adjacent Duplicates** | Simple adjacent cancellation without scope matching — belongs to **Pattern 01**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 2 |
| **Medium** | 3 |
| **Total** | **5** |

---

## 🎯 Question Set (5 Questions)

### Q1. Valid Parentheses
<a href="https://leetcode.com/problems/valid-parentheses/" target="_blank">LeetCode 20</a> — **Easy**

**Target Skill:** Structural balance verification and opening/closing pair matching.

**Core Reasoning:**
- When encountering an opening bracket `(`, `[`, `{`, push the corresponding closing bracket onto the stack.
- When encountering a closing bracket, check if the stack is non-empty and the popped character matches.
- String is valid if stack is completely empty at the end.

**Why it belongs here:** The canonical entry problem for nested scope validation. Teaches LIFO matching of bracket scopes.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

### Q2. Minimum Remove to Make Valid Parentheses
<a href="https://leetcode.com/problems/minimum-remove-to-make-valid-parentheses/" target="_blank">LeetCode 1249</a> — **Medium**

**Target Skill:** Index tracking for invalid scope removal.

**Core Reasoning:**
- Track indices of unmatched opening parentheses using a stack.
- If an unmatched closing parenthesis `)` is encountered (stack of `(` indices is empty), mark its index for removal.
- Any indices remaining in the stack after full traversal are unmatched `(` and must also be removed.
- Rebuild string excluding marked indices.

**Why it belongs here:** Demonstrates using a stack not just for validation, but for capturing index locations of structural mismatches to mutate the string cleanly.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

### Q3. Remove Outermost Parentheses
<a href="https://leetcode.com/problems/remove-outermost-parentheses/" target="_blank">LeetCode 1021</a> — **Easy**

**Target Skill:** Primitive decomposition via balance counter or depth tracking.

**Core Reasoning:**
- Maintain a balance counter (or depth tracker).
- An opening parenthesis `(` is inner (and appended) if `depth > 0`, then increment `depth`.
- A closing parenthesis `)` is inner (and appended) if `depth > 1` (before decrementing `depth`).
- Outer boundaries occur whenever `depth` transitions from $0 \to 1$ or $1 \to 0$.

**Why it belongs here:** Teaches scope level tracking to strip top-level container boundaries while preserving inner nested structure.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

### Q4. Decode String
<a href="https://leetcode.com/problems/decode-string/" target="_blank">LeetCode 394</a> — **Medium**

**Target Skill:** Nested state preservation (multiplier and partial string buffers).

**Core Reasoning:**
- Encoded string format: `k[encoded_string]`.
- When `[` is reached, save `k` onto `countStack` and the accumulated `currentString` onto `stringStack`. Reset `currentString` and `k`.
- When `]` is reached, pop the repeat count and previous string context, expand `currentString`, and append to restored context.

**Why it belongs here:** **Canonical home for Decode String.** Demonstrates push on scope open (`[`), pop and execute expansion on scope close (`]`).

**Complexity:** Time: $O(N \times \text{max\_k})$, Space: $O(N)$.

---

### Q5. Simplify Path
<a href="https://leetcode.com/problems/simplify-path/" target="_blank">LeetCode 71</a> — **Medium**

**Target Skill:** Hierarchical directory path resolution and stack-based path normalization.

**Core Reasoning:**
- Split path by `/`. Ignore empty tokens and single dots `.`.
- If token is double dot `..`, pop from stack if stack is non-empty (move up one directory level).
- For valid directory names, push token onto stack.
- Join remaining stack elements with `/` starting from the root `/`.

**Why it belongs here:** Filesystem path structures are hierarchical. Moving up a directory level (`..`) is conceptually identical to popping an outer scope from a stack.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

## ⚡ Mastery Checklist

- [ ] Can you handle multi-bracket matching (`()`, `[]`, `{}`) concisely?
- [ ] Do you know how to track index positions in a stack to resolve unmatched brackets?
- [ ] Can you explain why `Decode String` requires two stacks (or a stack of state objects)?
- [ ] Do you recognize why directory traversal (`..`) is fundamentally a LIFO operation?
