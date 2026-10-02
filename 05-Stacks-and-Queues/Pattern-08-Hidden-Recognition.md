# Pattern 08: Hidden Recognition

> In real SDE interviews, problems are rarely tagged with "Stack" or "Queue". Your primary challenge is recognizing when an underlying LIFO, greedy-undo, or collapsing trajectory relationship requires a stack.

---

## Why This Pattern Exists

Most candidates memorize stack templates for obvious bracket matching or next-greater queries. However, unfamiliar interview questions present problem statements in terms of:
- Function execution logs and CPU timestamps.
- Moving vehicles on a single-lane highway.
- Building the smallest possible number by removing $K$ digits.

None of these mention a stack explicitly.

To solve them, you must learn to reason from first principles:
1. **Read Problem Statement:** Identify the physical process or constraint.
2. **Identify Unresolved State:** Which items are waiting for future conditions to resolve?
3. **Determine Necessary Information:** What data must be preserved vs discarded?
4. **Derive Structure:** Does the most recently added active item need to react first? $\longrightarrow$ **A Stack Emerges.**

---

## Deriving Stack Behavior from First Principles

### Scenario A: Call Stack / Nested Execution
- **Problem Observation:** A function starts, and before it finishes, another function starts.
- **Deduction:** The inner function must complete before the outer function can resume.
- **Emergent Pattern:** Nested execution $\to$ most recently active function $\to$ **LIFO Stack State**.

### Scenario B: Ordered Trajectories & Merging Fleets
- **Problem Observation:** Faster cars behind slower cars catch up and form a fleet. Fleets cannot pass each other.
- **Deduction:** Sort by starting position descending (closest to target first). If a car behind takes $\le$ time than the car ahead, it merges into the ahead car's fleet.
- **Emergent Pattern:** Ordered candidates $\to$ collapsing future states $\to$ **Stack Trajectory Reasoning**.

### Scenario C: Greedy Number Reduction
- **Problem Observation:** To make a number as small as possible, earlier (leftmost) digits should be as small as possible.
- **Deduction:** If digit $D_i < D_{i-1}$, removing $D_{i-1}$ produces a smaller overall number.
- **Emergent Pattern:** Previous choice becomes worse $\to$ undo previous choice $\to$ **Greedy + Stack Reasoning**.

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Valid Parentheses** | Explicit bracket scope matching — belongs to **Pattern 02**. |
| **Asteroid Collision** | Direct physical collision simulation — belongs to **Pattern 01**. |
| **Daily Temperatures** | Standard next-greater element query — belongs to **Pattern 03**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Medium** | 3 |
| **Total** | **3** |

---

## 🎯 Question Set (3 Questions)

### Q1. Exclusive Time of Functions
<a href="https://leetcode.com/problems/exclusive-time-of-functions/" target="_blank">LeetCode 636</a> — **Medium**

**Target Skill:** CPU execution log analysis, call stack state suspension, and execution delta tracking.

**Core Reasoning:**
- Given logs `"function_id:start_or_end:timestamp"`.
- When a function starts: if a function is already running on top of stack, add elapsed time (`timestamp - prevTime`) to its total exclusive time. Push new function ID to stack. Update `prevTime = timestamp`.
- When a function ends: pop top function ID, add elapsed time (`timestamp - prevTime + 1`) to its total time. Update `prevTime = timestamp + 1`.

**Why it belongs here:** The problem presents as log parsing. Recognizing that active functions are suspended and resumed in reverse order of entry derives the stack data structure automatically.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

### Q2. Car Fleet
<a href="https://leetcode.com/problems/car-fleet/" target="_blank">LeetCode 853</a> — **Medium**

**Target Skill:** Position-sorted trajectory arrival time analysis and fleet merger collapsing.

**Core Reasoning:**
- $N$ cars heading to `target`. Car $i$ has `position[i]` and `speed[i]`. Single lane, no overtaking.
- Calculate time to reach target for each car: `time = (target - position) / speed`.
- Sort cars by position descending (cars closest to target processed first).
- Iterate through sorted cars: if current car's time is $\le$ time of fleet ahead (top of stack), it merges into that fleet (do not push).
- If current car's time is $>$, it forms a new independent fleet (push time onto stack).
- Number of fleets = remaining elements in stack.

**Why it belongs here:** Appears as a physics/kinematics simulation. Sorting by position and collapsing slower trailing trajectories reveals a stack reduction pattern.

**Complexity:** Time: $O(N \log N)$ (due to sorting), Space: $O(N)$.

---

### Q3. Remove K Digits
<a href="https://leetcode.com/problems/remove-k-digits/" target="_blank">LeetCode 402</a> — **Medium**

**Target Skill:** Most-significant digit greedy minimization and monotonic order enforcement.

**Core Reasoning:**
- Given non-negative integer string `num` and integer $K$. Remove $K$ digits to make remaining number as small as possible.
- Leftmost digits have highest place value.
- Iterate through digits: while $K > 0$, stack is non-empty, and current digit < `stack.peek()`, pop stack and decrement $K$.
- Push current digit.
- If $K > 0$ remains after iteration, truncate $K$ digits from tail. Strip leading zeros.

**Why it belongs here:** Appears as a greedy string modification problem. Discovering that a larger predecessor digit hurts the overall magnitude leads directly to a stack undo pattern.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

## ⚡ Mastery Checklist

- [ ] Can you identify nested execution state from log timestamps without being told to use a stack?
- [ ] Do you know why sorting by position descending is critical in `Car Fleet`?
- [ ] Can you explain why leftmost digits must be minimized first in `Remove K Digits`?
- [ ] When presented with an unfamiliar problem, can you ask: *"Which previous decision needs to be undone or merged when new input arrives?"*
