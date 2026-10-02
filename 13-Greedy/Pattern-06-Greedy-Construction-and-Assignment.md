# Pattern 06 — Greedy Construction & Assignment

Greedy Construction and Assignment problems involve satisfying structural constraints across sequences or partitioning resources across options. Often, a single directional pass is insufficient; we must satisfy **two-pass directional constraints**, **retain local group maximums**, or **sort by relative-cost savings differentials**.

---

## Learning Progression

```text
Two-Pass Directional Constraints (LC 135) ──► Group Maximum Retention (LC 1578) ──► Relative Cost Differential Assignment (LC 1029)
```

1. **Two-Pass Constraints (<a href="https://leetcode.com/problems/candy/" target="_blank">LeetCode 135</a>)**: Solve left-to-right neighbor constraints first, then right-to-left neighbor constraints.
2. **Group Maximum Retention (<a href="https://leetcode.com/problems/minimum-time-to-make-rope-colorful/" target="_blank">LeetCode 1578</a>)**: In any duplicate group, retain the single most expensive element and remove cheaper ones.
3. **Relative Cost Assignment (<a href="https://leetcode.com/problems/two-city-scheduling/" target="_blank">LeetCode 1029</a>)**: Sort candidates by relative cost differential $(\text{costB} - \text{costA})$ to maximize savings.

> [!NOTE]
> **Canonical Ownership Rule:** Section 05 (Stacks) canonically owns <a href="https://leetcode.com/problems/remove-k-digits/" target="_blank">LeetCode 402 (Remove K Digits)</a> via Monotonic Stack. Section 08 (Heaps) canonically owns <a href="https://leetcode.com/problems/reorganize-string/" target="_blank">LeetCode 767</a> and <a href="https://leetcode.com/problems/task-scheduler/" target="_blank">LeetCode 621</a> via Priority Queue frequency construction.

---

## Canonical Question Set (3 Questions)

### 1. Candy (Understand & Apply)
- **Problem:** <a href="https://leetcode.com/problems/candy/" target="_blank">LeetCode 135</a>
- **Difficulty:** Hard
- **Core Greedy Idea:** Two-pass directional constraint resolution.
- **Greedy Decision:** Initialize all `candies = 1`.
  - Pass 1 (Left-to-Right): If `ratings[i] > ratings[i-1]`, set `candies[i] = candies[i-1] + 1`.
  - Pass 2 (Right-to-Left): If `ratings[i] > ratings[i+1]`, set `candies[i] = max(candies[i], candies[i+1] + 1)`.
- **Invariant / Proof Intuition:** A single directional pass cannot satisfy constraints from both neighbors simultaneously. Combining left-to-right and right-to-left passes using `max()` guarantees both neighbor constraints are satisfied with minimum total candies.
- **Recognition Clue:** Array sequence where element assignment depends on both immediate left and right neighbor values.
- **Common Wrong Approach:** Attempting a single-pass traversal or sorting ratings, which breaks linear sequence adjacencies.
- **Complexity:** Time: $O(N)$, Space: $O(N)$.
- **Cross-Pattern Connection:** Two-pass directional greedy construction.

---

### 2. Minimum Time to Make Rope Colorful (Recognize)
- **Problem:** <a href="https://leetcode.com/problems/minimum-time-to-make-rope-colorful/" target="_blank">LeetCode 1578</a>
- **Difficulty:** Medium
- **Core Greedy Idea:** Retain single maximum removal time per contiguous duplicate group.
- **Greedy Decision:** Iterate through balloons. In any contiguous block of identical colors, accumulate removal times for all balloons except the one with the **maximum removal time** `maxTime`.
- **Invariant / Proof Intuition:** To eliminate adjacent duplicates in a same-color group of size $K$, exactly $K-1$ balloons must be removed. To minimize total removal time, keep the single balloon with the highest removal cost and remove the rest.
- **Recognition Clue:** Eliminating adjacent duplicates in a string where each element has an associated removal cost.
- **Common Wrong Approach:** Complicating with DP or string manipulation when local group max retention handles it in $O(N)$ time.
- **Complexity:** Time: $O(N)$, Space: $O(1)$.
- **Cross-Pattern Connection:** Group-based local optimum retention.

---

### 3. Two City Scheduling (Transfer)
- **Problem:** <a href="https://leetcode.com/problems/two-city-scheduling/" target="_blank">LeetCode 1029</a>
- **Difficulty:** Medium
- **Core Greedy Idea:** Relative cost differential assignment sorting.
- **Greedy Decision:** Assume all $2N$ people fly to City A initially. Sort candidates ascending by differential `(costB - costA)`. Send the first $N$ people (those with the greatest cost reduction when choosing B over A) to City B, and the remaining $N$ people to City A.
- **Invariant / Proof Intuition:** Sorting by $(\text{costB} - \text{costA})$ orders people by how much money is saved by sending them to City B instead of City A. Selecting the top $N$ relative savings guarantees global cost minimization.
- **Recognition Clue:** Partitioning $2N$ candidates into two equal groups of size $N$ to minimize total cost.
- **Common Wrong Approach:** Sorting by absolute cost to City A or absolute cost to City B individually instead of their cost difference.
- **Complexity:** Time: $O(N \log N)$, Space: $O(1)$.
- **Cross-Pattern Connection:** Relative advantage greedy sorting.
