# Pattern 01: Stack Fundamentals & Simulation

> The core property of a stack is LIFO (Last In, First Out). When a problem requires undoing previous actions, matching sequential cancellations, or maintaining unresolved context while processing elements one by one, a stack is the natural data structure.

---

## Why This Pattern Exists

Many sequential processing problems require evaluating how incoming elements interact with the most recently processed elements.

Brute-force solutions often require repeated array scans or dynamic array resizings $O(N^2)$. A stack solves this in $O(N)$ time by maintaining only the **unresolved state** — the elements that are still active and waiting to interact with future inputs.

This pattern addresses:
- **LIFO Behavior:** Accessing and modifying the top of the stack in $O(1)$ time.
- **Sequential Cancellation:** Removing elements when they meet a matching condition with an incoming element.
- **Collision Resolution:** Simulating pairwise interactions between adjacent elements.
- **State Preservation:** Retaining prior state so execution can resume cleanly once sub-tasks resolve.

> **Note on Problem Placement:** While some problems in this pattern process strings or numbers, their canonical home is **Stack Fundamentals** because the dominant reasoning is stack-based simulation rather than string manipulation or mathematical formulation.

---

## When to Use This Pattern

Use Stack Fundamentals & Simulation when:
- An incoming element can modify, cancel, or merge with the most recent element.
- Operations depend on the order of occurrence in reverse (last seen is first processed).
- You need an $O(1)$ undo mechanism during sequential iteration.
- A score, string, or state is built iteratively with conditional backspaces or modifications.

---

## ☕ Standard Java Templates

### 1. Basic Stack Simulation (Using Deque)
```java
// ArrayDeque is preferred over java.util.Stack due to performance and thread-safety overhead of Vector
Deque<Character> stack = new ArrayDeque<>();

for (char c : input.toCharArray()) {
    if (shouldCancel(stack.peek(), c)) {
        stack.pop(); // Undo / cancel top element
    } else {
        stack.push(c); // Add to unresolved state
    }
}

// Convert stack to final result string if needed
StringBuilder sb = new StringBuilder();
while (!stack.isEmpty()) {
    sb.append(stack.pollLast()); // Read from bottom to top
}
return sb.toString();
```

### 2. Collision Simulation Pattern
```java
Deque<Integer> stack = new ArrayDeque<>();

for (int item : items) {
    boolean destroyed = false;
    while (!stack.isEmpty() && causesCollision(stack.peek(), item)) {
        if (stack.peek() < Math.abs(item)) {
            stack.pop(); // Stack element is destroyed; check next
            continue;
        } else if (stack.peek() == Math.abs(item)) {
            stack.pop(); // Both destroyed
        }
        destroyed = true;
        break; // Current item is destroyed
    }
    if (!destroyed) {
        stack.push(item);
    }
}
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Valid Parentheses** | Involves strict structural balance and type matching — belongs to **Pattern 02**. |
| **Implement Stack using Queues** | Data structure design using auxiliary queues — belongs to **Pattern 07**. |
| **Decode String** | Nested repetition and multiplier state tracking — belongs to **Pattern 02**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 3 |
| **Medium** | 1 |
| **Total** | **4** |

---

## 🎯 Question Set (4 Questions)

### Q1. Backspace String Compare
<a href="https://leetcode.com/problems/backspace-string-compare/" target="_blank">LeetCode 844</a> — **Easy**

**Target Skill:** Stack simulation for backspace cancellation and string construction.

**Core Reasoning:**
- A `#` character acts as a backspace, removing the previously typed character.
- Process string $S$ and string $T$ using a stack: push regular characters, pop when encountering `#` if stack is non-empty.
- Compare the final string representations produced by both stacks.

**Why it belongs here:** Demonstrates pure LIFO cancellation. The `#` key only affects the immediate prior character available at the top of the stack.

**Complexity:** Time: $O(N + M)$, Space: $O(N + M)$ (or $O(1)$ auxiliary space using two pointers from right-to-left).

---

### Q2. Remove All Adjacent Duplicates in String
<a href="https://leetcode.com/problems/remove-all-adjacent-duplicates-in-string/" target="_blank">LeetCode 1047</a> — **Easy**

**Target Skill:** Adjacent element matching and iterative stack reduction.

**Core Reasoning:**
- Iterate through the string character by character.
- If the current character matches the character at the top of the stack, pop the top character (they cancel out).
- Otherwise, push the current character onto the stack.
- Reconstruct the string from the remaining characters in the stack.

**Why it belongs here:** Shows how stack LIFO ordering automatically handles cascaded cancellations (e.g., `"abba"` $\to$ `'b'` pops `'b'`, leaving `"aa"`, then `'a'` pops `'a'`).

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

### Q3. Asteroid Collision
<a href="https://leetcode.com/problems/asteroid-collision/" target="_blank">LeetCode 735</a> — **Medium**

**Target Skill:** Pairwise collision resolution and multi-pop simulation.

**Core Reasoning:**
- Asteroids moving right ($>0$) never collide with prior asteroids. Asteroids moving left ($<0$) collide with any prior right-moving asteroids on top of the stack.
- For each left-moving asteroid:
  - While top of stack is moving right and smaller than current asteroid: pop stack (top asteroid explodes).
  - If top is equal size: pop stack and destroy current (both explode).
  - If top is larger: destroy current asteroid.
  - If stack becomes empty or top is left-moving: push current asteroid.

**Why it belongs here:** Perfect example of maintaining unresolved state during pairwise physical simulation.

**Complexity:** Time: $O(N)$ (each asteroid is pushed and popped at most once), Space: $O(N)$.

---

### Q4. Baseball Game
<a href="https://leetcode.com/problems/baseball-game/" target="_blank">LeetCode 682</a> — **Easy**

**Target Skill:** Multi-operation state history maintenance and cumulative operations.

**Core Reasoning:**
- Operations modify previous scores: `"+"`, `"D"`, `"C"`, or an integer.
- Use a stack of valid scores:
  - Integer: push score.
  - `"+"`: peek top two scores, sum them, push sum.
  - `"D"`: peek top score, double it, push result.
  - `"C"`: pop top score (cancel previous invalid score).
- Sum all elements remaining in the stack at the end.

**Why it belongs here:** Directly exercises stack state maintenance where current scores depend on looking back 1 or 2 steps into the LIFO history.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

## ⚡ Mastery Checklist

- [ ] Can you implement backspace processing using a stack in $O(N)$ time?
- [ ] Do you understand why adjacent cancellation automatically handles cascading pairs?
- [ ] Can you write clean collision simulation loops without infinite while-loop bugs?
- [ ] Do you default to `ArrayDeque` in Java over legacy `java.util.Stack`?
