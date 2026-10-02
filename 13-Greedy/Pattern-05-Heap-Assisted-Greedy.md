# Pattern 05 — Heap-Assisted Greedy

Heap-Assisted Greedy applies when the **feasibility frontier expands dynamically** as decisions are executed. A Max-Heap (Priority Queue) acts as a dynamic candidate pool, storing newly reachable options and granting $O(\log N)$ access to the best available greedy choice.

---

## Learning Progression

```text
Dynamic Feasibility Frontier (LC 502) ──► Deferred Greedy Choice (LC 871)
```

1. **Dynamic Feasibility Frontier (<a href="https://leetcode.com/problems/ipo/" target="_blank">LeetCode 502</a>)**: Unlock projects into a Max-Heap as capital increases; greedily pick max profit project.
2. **Deferred Greedy Choice (<a href="https://leetcode.com/problems/minimum-number-of-refueling-stops/" target="_blank">LeetCode 871</a>)**: Pass gas stations without stopping, collecting fuel in a Max-Heap; greedily consume largest fuel source only when current tank runs dry.

> [!NOTE]
> **Canonical Ownership Rule:** Section 08 (Heaps) canonically owns <a href="https://leetcode.com/problems/furthest-building-you-can-reach/" target="_blank">LeetCode 1642</a>, <a href="https://leetcode.com/problems/course-schedule-iii/" target="_blank">LeetCode 630</a>, <a href="https://leetcode.com/problems/maximum-number-of-events-that-can-be-attended/" target="_blank">LeetCode 1353</a>, <a href="https://leetcode.com/problems/minimum-cost-to-hire-k-workers/" target="_blank">LeetCode 857</a>, and <a href="https://leetcode.com/problems/maximum-performance-of-a-team/" target="_blank">LeetCode 1383</a>. Greedy P05 keeps a focused 2-question benchmark to teach dynamic feasibility frontier mechanics without duplicating heap section contents.

---

## Canonical Question Set (2 Questions)

### 1. IPO (Understand & Apply)
- **Problem:** <a href="https://leetcode.com/problems/ipo/" target="_blank">LeetCode 502</a>
- **Difficulty:** Hard
- **Core Greedy Idea:** Maximize profit choice over expanding capital availability pool.
- **Greedy Decision:** Sort projects by required capital ascending. At each step, push all projects with `capital <= current_w` into a Max-Heap ordered by profit. Pop and execute the project with maximum profit to increase `current_w`. Repeat $k$ times.
- **Invariant / Proof Intuition:** Executing the highest profit project among all currently affordable choices maximizes available capital for the next step, unlocking the largest possible set of future projects.
- **Recognition Clue:** Selecting $k$ projects to maximize capital where project availability depends on current capital.
- **Common Wrong Approach:** Picking the project with the lowest capital requirement or highest profit without maintaining a dynamic feasibility heap.
- **Complexity:** Time: $O(N \log N + K \log N)$, Space: $O(N)$.
- **Cross-Pattern Connection:** Greedy decision + Heap + expanding feasibility frontier.

---

### 2. Minimum Number of Refueling Stops (Recognize & Transfer)
- **Problem:** <a href="https://leetcode.com/problems/minimum-number-of-refueling-stops/" target="_blank">LeetCode 871</a>
- **Difficulty:** Hard
- **Core Greedy Idea:** Deferred greedy choice using a Max-Heap of passed fuel options.
- **Greedy Decision:** Drive forward as far as `curr_fuel` allows, storing all passed gas stations in a Max-Heap. When `curr_fuel < target` and cannot reach the next station, pop the largest fuel amount from the Max-Heap and add it to `curr_fuel` until reachable.
- **Invariant / Proof Intuition:** Reaching station $S$ gives you the option to refuel there. Deferring the decision to refuel until fuel actually runs out—and then picking the largest available past fuel station—guarantees minimum total stops.
- **Recognition Clue:** Traversing a distance with consumable capacity points where choice of consumption can be deferred until deficit occurs.
- **Common Wrong Approach:** Using $O(\text{Target})$ or $O(N^2)$ DP when deferred Max-Heap refuel selection solves it in $O(N \log N)$.
- **Complexity:** Time: $O(N \log N)$, Space: $O(N)$.
- **Cross-Pattern Connection:** Connects to deferred choice greedy execution.
