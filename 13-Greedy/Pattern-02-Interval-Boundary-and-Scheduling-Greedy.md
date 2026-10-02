# Pattern 02 — Interval, Boundary & Scheduling Greedy

Interval greedy problems revolve around arranging, eliminating, or segmenting ranges based on endpoint boundaries. The central objective is selecting endpoints that leave maximum remaining space for future intervals or expanding segment boundaries until all contained elements satisfy their last-occurrence constraints.

---

## Learning Progression

```text
Understand (Overlap Checking) ──► Apply (Earliest Finish / Min Arrows) ──► Recognize (Boundary Expansion) ──► Transfer (Horizon Coverage & Stitching)
```

1. **Understand (Overlap Checking)**: Detect overlapping interval pairs (<a href="https://leetcode.com/problems/meeting-rooms/" target="_blank">LeetCode 252</a>).
2. **Apply (Earliest Finish & Min Arrows)**: Minimize removals or arrow shots by sorting end points (<a href="https://leetcode.com/problems/non-overlapping-intervals/" target="_blank">LeetCode 435</a>, <a href="https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons/" target="_blank">LeetCode 452</a>).
3. **Recognize (Boundary Expansion)**: Expand partition boundaries dynamically based on character last-occurrence indices (<a href="https://leetcode.com/problems/partition-labels/" target="_blank">LeetCode 763</a>).
4. **Transfer (Horizon Coverage)**: Greedily extend maximum reachable coverage across overlapping intervals (<a href="https://leetcode.com/problems/video-stitching/" target="_blank">LeetCode 1024</a>).

---

## Canonical Question Set (5 Questions)

### 1. Meeting Rooms (Understand)
- **Problem:** <a href="https://leetcode.com/problems/meeting-rooms/" target="_blank">LeetCode 252</a>
- **Difficulty:** Easy
- **Core Greedy Idea:** Sort intervals by start time to detect adjacent schedule conflicts.
- **Greedy Decision:** Sort `intervals` by `start_i` ascending. Check if any meeting starts before the previous meeting ends (`intervals[i][0] < intervals[i-1][1]`).
- **Invariant / Proof Intuition:** Sorting places temporally adjacent meetings next to each other; if adjacent meetings do not overlap, no non-adjacent meetings can overlap.
- **Recognition Clue:** Single resource scheduling where any temporal overlap makes the schedule invalid.
- **Common Wrong Approach:** Pairwise $O(N^2)$ comparison without sorting start times.
- **Complexity:** Time: $O(N \log N)$, Space: $O(1)$.
- **Cross-Pattern Connection:** Baseline foundation for interval overlap detection.

---

### 2. Non-overlapping Intervals (Apply)
- **Problem:** <a href="https://leetcode.com/problems/non-overlapping-intervals/" target="_blank">LeetCode 435</a>
- **Difficulty:** Medium
- **Core Greedy Idea:** Maximize kept intervals by picking the interval that finishes earliest.
- **Greedy Decision:** Sort intervals by **end time** ascending. Keep the first interval, and greedily skip any subsequent interval that starts before `prev_end`.
- **Invariant / Proof Intuition:** Minimizing removals is mathematically equivalent to maximizing non-overlapping intervals. Choosing the earliest end time leaves the maximum possible remaining timeline for subsequent intervals.
- **Recognition Clue:** Minimizing deletions to make intervals disjoint.
- **Common Wrong Approach:** Sorting by start time or interval length, which fails when short/early-starting intervals extend far into the future.
- **Complexity:** Time: $O(N \log N)$, Space: $O(1)$.
- **Cross-Pattern Connection:** Identical greedy mechanics to Maximum Length of Pair Chain (Pattern 01).

---

### 3. Minimum Number of Arrows to Burst Balloons (Apply)
- **Problem:** <a href="https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons/" target="_blank">LeetCode 452</a>
- **Difficulty:** Medium
- **Core Greedy Idea:** Shoot arrow at earliest balloon end point to burst maximum overlapping balloons.
- **Greedy Decision:** Sort balloons by **end coordinate** `xend`. Place an arrow at the end coordinate of the first unburst balloon (`arrowPos = points[0][1]`). Skip all balloons whose start coordinate `xstart <= arrowPos`.
- **Invariant / Proof Intuition:** Shooting at the rightmost possible point of the earliest-ending balloon guarantees bursting that balloon while maximizing the chance of intersecting other overlapping balloons.
- **Recognition Clue:** Point-to-interval intersection optimization where 1 point can cover multiple overlapping ranges.
- **Common Wrong Approach:** Sorting by start coordinate, which leads to suboptimal arrow placements when long balloons contain shorter nested ones.
- **Complexity:** Time: $O(N \log N)$, Space: $O(1)$.
- **Cross-Pattern Connection:** Dual of Interval Scheduling (LC 435), where point shooting replaces interval selection.

---

### 4. Partition Labels (Recognize)
- **Problem:** <a href="https://leetcode.com/problems/partition-labels/" target="_blank">LeetCode 763</a>
- **Difficulty:** Medium
- **Core Greedy Idea:** Expand segment boundary to the maximum last-occurrence index of all characters seen so far.
- **Greedy Decision:** Precompute `last_occurrence` index for each character. Iterate through string, updating `max_boundary = max(max_boundary, last_occurrence[s[i]])`. Close partition when index `i == max_boundary`.
- **Invariant / Proof Intuition:** `last occurrence boundary -> expand current segment -> close when boundary is satisfied`. A partition cannot be closed until every character inside it has no remaining appearances to the right. Closing at the earliest satisfied boundary maximizes the total number of partitions.
- **Recognition Clue:** Partitioning a sequence such that distinct character sets are completely contained within their respective segments.
- **Common Wrong Approach:** Using complex two-pointer windowing without precomputing last-occurrence indices.
- **Complexity:** Time: $O(N)$, Space: $O(1)$ (26-character alphabet array).
- **Cross-Pattern Connection:** Teaches boundary-based greedy reasoning distinct from classic interval scheduling.

---

### 5. Video Stitching (Transfer)
- **Problem:** <a href="https://leetcode.com/problems/video-stitching/" target="_blank">LeetCode 1024</a>
- **Difficulty:** Medium
- **Core Greedy Idea:** Extend reach horizon greedily by choosing the clip with maximum end time among all clips starting within current reach.
- **Greedy Decision:** Sort clips by start time. Among all clips with `start <= curr_end`, select the clip that maximizes `max_reach`. Set `curr_end = max_reach` and increment clip count.
- **Invariant / Proof Intuition:** Expanding the reach horizon as far right as possible in each step minimizes the total number of clips needed to cover `[0, time]`.
- **Recognition Clue:** Minimum continuous interval coverage over target range `[0, T]` from overlapping candidate segments.
- **Common Wrong Approach:** Using interval DP $O(T^2)$ when level-horizon greedy extension solves the problem in $O(N \log N)$ time.
- **Complexity:** Time: $O(N \log N)$, Space: $O(1)$.
- **Cross-Pattern Connection:** Shares reach-horizon mechanics with Jump Game II (Pattern 03).
