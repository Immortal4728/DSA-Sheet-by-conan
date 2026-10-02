# 📦 Module 05: Stacks & Queues

> Master LIFO and FIFO invariants, monotonic ordering, expression parsing, sliding window deques, and hidden stack recognition for SDE-1 interviews.

---

## 🎯 Section Overview

Stacks and Queues are fundamental linear data structures that enforce strict access and eviction rules:
- **Stack (LIFO — Last In, First Out):** Resolves the most recently added unresolved element first. Ideal for nested scope matching, undo mechanisms, function execution, and monotonic candidate filtering.
- **Queue (FIFO — First In, First Out):** Preserves order of arrival. Ideal for streaming windows, round-robin turn processing, circular buffer management, and breadth-first exploration.
- **Deque (Double-Ended Queue):** Supports $O(1)$ operations at both head and tail. Essential for sliding window monotonic max/min filtering.

This curriculum contains **32 carefully curated questions** structured across **8 distinct patterns**. Every problem has **exactly one canonical home** in this repository.

---

## 🗺️ Curriculum Architecture

| Pattern | Focus Area | Questions | Core Invariant / Mechanism |
|---------|------------|-----------|----------------------------|
| **[Pattern 01](./Pattern-01-Stack-Fundamentals-and-Simulation.md)** | Stack Fundamentals & Simulation | 4 | Basic LIFO state, sequential cancellation, collision simulation |
| **[Pattern 02](./Pattern-02-Parentheses-and-Nested-Structures.md)** | Parentheses & Nested Structures | 5 | Scope matching, balance tracking, nested decoding, path resolution |
| **[Pattern 03](./Pattern-03-Monotonic-Stack.md)** | Monotonic Stack | 8 | Nearest greater/smaller element, boundary detection, contribution counting |
| **[Pattern 04](./Pattern-04-Stack-and-Expression-Processing.md)** | Stack + Expression Processing | 3 | Postfix evaluation, operator precedence, sign/parentheses state |
| **[Pattern 05](./Pattern-05-Queue-and-Deque-Fundamentals.md)** | Queue & Deque Fundamentals | 4 | FIFO state, circular array buffers, expiring stream windows, round-robin |
| **[Pattern 06](./Pattern-06-Monotonic-Deque.md)** | Monotonic Deque | 2 | Sliding window max/min, dual-ended eviction, non-monotonic prefix sums |
| **[Pattern 07](./Pattern-07-Stack-Queue-Design.md)** | Stack/Queue Design | 3 | Auxiliary min state, dual-stack queue simulation, queue rotation stack |
| **[Pattern 08](./Pattern-08-Hidden-Recognition.md)** | Hidden Recognition | 3 | Unresolved log context, collapsing trajectory fleets, greedy digit undo |

---

## 🔒 Canonical Placement & Duplication Rules

To prevent confusion, every problem is assigned to exactly **one canonical home**:

- **LC 225 (Implement Stack using Queues):** Assigned exclusively to **Pattern 07 (Stack/Queue Design)**.
- **LC 232 (Implement Queue using Stacks):** Assigned exclusively to **Pattern 07 (Stack/Queue Design)**.
- **LC 394 (Decode String):** Assigned exclusively to **Pattern 02 (Parentheses & Nested Structures)**. (Not duplicated in Expression Processing).
- **LC 844 (Backspace String Compare):** Assigned exclusively to **Pattern 01 (Stack Fundamentals)**.
- **LC 1047 (Remove All Adjacent Duplicates):** Assigned exclusively to **Pattern 01 (Stack Fundamentals)**.
- **LC 907 (Sum of Subarray Minimums):** Assigned exclusively to **Pattern 03 (Monotonic Stack)**.
- **LC 239 (Sliding Window Maximum):** Assigned exclusively to **Pattern 06 (Monotonic Deque)**.
- **LC 402 (Remove K Digits):** Assigned exclusively to **Pattern 08 (Hidden Recognition)**.

---

## 📑 Full Question Matrix (32 Questions)

### Pattern 01: Stack Fundamentals & Simulation (4 Questions)
1. <a href="https://leetcode.com/problems/backspace-string-compare/" target="_blank">LeetCode 844: Backspace String Compare</a> — **Easy**
2. <a href="https://leetcode.com/problems/remove-all-adjacent-duplicates-in-string/" target="_blank">LeetCode 1047: Remove All Adjacent Duplicates in String</a> — **Easy**
3. <a href="https://leetcode.com/problems/asteroid-collision/" target="_blank">LeetCode 735: Asteroid Collision</a> — **Medium**
4. <a href="https://leetcode.com/problems/baseball-game/" target="_blank">LeetCode 682: Baseball Game</a> — **Easy**

### Pattern 02: Parentheses & Nested Structures (5 Questions)
1. <a href="https://leetcode.com/problems/valid-parentheses/" target="_blank">LeetCode 20: Valid Parentheses</a> — **Easy**
2. <a href="https://leetcode.com/problems/minimum-remove-to-make-valid-parentheses/" target="_blank">LeetCode 1249: Minimum Remove to Make Valid Parentheses</a> — **Medium**
3. <a href="https://leetcode.com/problems/remove-outermost-parentheses/" target="_blank">LeetCode 1021: Remove Outermost Parentheses</a> — **Easy**
4. <a href="https://leetcode.com/problems/decode-string/" target="_blank">LeetCode 394: Decode String</a> — **Medium**
5. <a href="https://leetcode.com/problems/simplify-path/" target="_blank">LeetCode 71: Simplify Path</a> — **Medium**

### Pattern 03: Monotonic Stack (8 Questions)
1. <a href="https://leetcode.com/problems/next-greater-element-i/" target="_blank">LeetCode 496: Next Greater Element I</a> — **Easy**
2. <a href="https://leetcode.com/problems/daily-temperatures/" target="_blank">LeetCode 739: Daily Temperatures</a> — **Medium**
3. <a href="https://leetcode.com/problems/next-greater-element-ii/" target="_blank">LeetCode 503: Next Greater Element II</a> — **Medium**
4. <a href="https://leetcode.com/problems/online-stock-span/" target="_blank">LeetCode 901: Online Stock Span</a> — **Medium**
5. <a href="https://leetcode.com/problems/final-prices-with-a-special-discount-in-a-shop/" target="_blank">LeetCode 1475: Final Prices With a Special Discount in a Shop</a> — **Easy**
6. <a href="https://leetcode.com/problems/largest-rectangle-in-histogram/" target="_blank">LeetCode 84: Largest Rectangle in Histogram</a> — **Hard**
7. <a href="https://leetcode.com/problems/sum-of-subarray-minimums/" target="_blank">LeetCode 907: Sum of Subarray Minimums</a> — **Medium**
8. <a href="https://leetcode.com/problems/trapping-rain-water/" target="_blank">LeetCode 42: Trapping Rain Water</a> — **Hard**

### Pattern 04: Stack + Expression Processing (3 Questions)
1. <a href="https://leetcode.com/problems/evaluate-reverse-polish-notation/" target="_blank">LeetCode 150: Evaluate Reverse Polish Notation</a> — **Medium**
2. <a href="https://leetcode.com/problems/basic-calculator-ii/" target="_blank">LeetCode 227: Basic Calculator II</a> — **Medium**
3. <a href="https://leetcode.com/problems/basic-calculator/" target="_blank">LeetCode 224: Basic Calculator</a> — **Hard**

### Pattern 05: Queue & Deque Fundamentals (4 Questions)
1. <a href="https://leetcode.com/problems/design-circular-queue/" target="_blank">LeetCode 622: Design Circular Queue</a> — **Medium**
2. <a href="https://leetcode.com/problems/number-of-recent-calls/" target="_blank">LeetCode 933: Number of Recent Calls</a> — **Easy**
3. <a href="https://leetcode.com/problems/time-needed-to-buy-tickets/" target="_blank">LeetCode 2073: Time Needed to Buy Tickets</a> — **Easy**
4. <a href="https://leetcode.com/problems/dota2-senate/" target="_blank">LeetCode 649: Dota2 Senate</a> — **Medium**

### Pattern 06: Monotonic Deque (2 Questions)
1. <a href="https://leetcode.com/problems/sliding-window-maximum/" target="_blank">LeetCode 239: Sliding Window Maximum</a> — **Hard**
2. <a href="https://leetcode.com/problems/shortest-subarray-with-sum-at-least-k/" target="_blank">LeetCode 862: Shortest Subarray with Sum at Least K</a> — **Hard**

### Pattern 07: Stack/Queue Design (3 Questions)
1. <a href="https://leetcode.com/problems/min-stack/" target="_blank">LeetCode 155: Min Stack</a> — **Medium**
2. <a href="https://leetcode.com/problems/implement-queue-using-stacks/" target="_blank">LeetCode 232: Implement Queue using Stacks</a> — **Easy**
3. <a href="https://leetcode.com/problems/implement-stack-using-queues/" target="_blank">LeetCode 225: Implement Stack using Queues</a> — **Easy**

### Pattern 08: Hidden Recognition (3 Questions)
1. <a href="https://leetcode.com/problems/exclusive-time-of-functions/" target="_blank">LeetCode 636: Exclusive Time of Functions</a> — **Medium**
2. <a href="https://leetcode.com/problems/car-fleet/" target="_blank">LeetCode 853: Car Fleet</a> — **Medium**
3. <a href="https://leetcode.com/problems/remove-k-digits/" target="_blank">LeetCode 402: Remove K Digits</a> — **Medium**

---

## 💡 Learning Standard

When studying this section, focus on answering:
1. **Why does LIFO or FIFO fit this problem better than a simple array scan?**
2. **What elements are rendered permanently useless when a new element arrives?**
3. **What invariant is preserved after every push and pop operation?**
4. **How do you derive the necessity of a stack when the problem statement does not explicitly mention one?**
