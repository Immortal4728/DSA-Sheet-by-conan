# Pattern 03 — Jump & Reachability Greedy

Jump and Reachability problems model linear traversals and circular journeys where decisions at step $i$ determine maximum future reach horizons. The core greedy insight is maintaining a dynamic boundary representing the farthest reachable position without needing explicit recursion or queue-based BFS state management.

---

## Learning Progression

```text
Can I reach the end? (LC 55) ──► What is the min number of jumps? (LC 45) ──► Where can I restart the journey? (LC 134)
```

1. **Reachability Check (<a href="https://leetcode.com/problems/jump-game/" target="_blank">LeetCode 55</a>)**: Track maximum reachable index `max_reach` dynamically across array traversal.
2. **Step Minimization (<a href="https://leetcode.com/problems/jump-game-ii/" target="_blank">LeetCode 45</a>)**: Track level horizons (`curr_end` vs `max_reach`) to count minimum jump steps.
3. **Circular Reset (<a href="https://leetcode.com/problems/gas-station/" target="_blank">LeetCode 134</a>)**: Reset candidate start index when cumulative fuel balance drops below zero.

---

## Canonical Question Set (3 Questions)

### 1. Jump Game (Understand)
- **Problem:** <a href="https://leetcode.com/problems/jump-game/" target="_blank">LeetCode 55</a>
- **Difficulty:** Medium
- **Core Greedy Idea:** Maintain dynamic maximum reachable index horizon.
- **Greedy Decision:** Iterate `i` from `0` to `N-1`. If `i > max_reach`, return `false`. Update `max_reach = max(max_reach, i + nums[i])`. Return `true` if `max_reach >= N - 1`.
- **Invariant / Proof Intuition:** If index `i` is reachable, all indices $j < i$ were also reachable. Expanding `max_reach` at each step guarantees tracking the global maximum reach boundary.
- **Recognition Clue:** Boolean reachability check on array with variable step sizes.
- **Common Wrong Approach:** Exponential backtracking trying all possible jump lengths from each index.
- **Complexity:** Time: $O(N)$, Space: $O(1)$.
- **Cross-Pattern Connection:** Baseline reachability foundation for Pattern 03.

---

### 2. Jump Game II (Apply)
- **Problem:** <a href="https://leetcode.com/problems/jump-game-ii/" target="_blank">LeetCode 45</a>
- **Difficulty:** Medium
- **Core Greedy Idea:** Implicit BFS level-by-level horizon expansion.
- **Greedy Decision:** Maintain `curr_end` (end of current jump range) and `max_reach` (farthest reach with next jump). When `i` reaches `curr_end`, increment `jumps++` and set `curr_end = max_reach`.
- **Invariant / Proof Intuition:** *Canonical Ownership: Owned by Greedy P03.* All indices within `[curr_start, curr_end]` are reachable with $K$ jumps. Extending `max_reach` across this window finds the maximum range for $K+1$ jumps, minimizing total jump count.
- **Recognition Clue:** Finding minimum steps to reach the end of an array with variable jump lengths.
- **Common Wrong Approach:** $O(N^2)$ 1D DP table when implicit BFS level tracking solves the problem in $O(N)$ linear time.
- **Complexity:** Time: $O(N)$, Space: $O(1)$.
- **Cross-Pattern Connection:** DP (Section 10) cross-references LC 45 as a contrasting approach, but canonical home is locked here.

---

### 3. Gas Station (Recognize & Transfer)
- **Problem:** <a href="https://leetcode.com/problems/gas-station/" target="_blank">LeetCode 134</a>
- **Difficulty:** Medium
- **Core Greedy Idea:** Cumulative deficit reset rule for circular journey start points.
- **Greedy Decision:** Track `curr_tank` and `total_tank`. If `curr_tank < 0` at station `i`, reset `start_station = i + 1` and `curr_tank = 0`. If `total_tank >= 0`, return `start_station`.
- **Invariant / Proof Intuition:** If starting at station $A$ causes fuel deficiency at station $B$, then **no station between $A$ and $B$ can be a valid starting point** (since starting at any intermediate station would arrive at $B$ with even less fuel). Resetting `start_station` to $B+1$ skips all invalid candidates safely.
- **Recognition Clue:** Circular array traversal where cumulative balance must remain non-negative at every step.
- **Common Wrong Approach:** Simulating the full circular journey from every starting index ($O(N^2)$ brute-force).
- **Complexity:** Time: $O(N)$, Space: $O(1)$.
- **Cross-Pattern Connection:** Connects to prefix sum balance invariants and single-pass reset mechanics.
