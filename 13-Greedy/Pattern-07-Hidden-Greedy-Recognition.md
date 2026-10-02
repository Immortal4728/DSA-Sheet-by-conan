# Pattern 07 — Hidden Greedy Recognition

In technical coding interviews, advanced greedy problems rarely advertise themselves. They appear disguised as Complex Dynamic Programming, Exponential Backtracking, or System Operations.

Hidden Greedy Recognition requires testing for **unbroken local invariants**, **coverage doubling intervals**, or **reversing problem execution perspectives**.

---

## Learning Progression

```text
Local Placement Invariant (LC 605) ──► Coverage Interval Doubling (LC 330) ──► Reverse Perspective Choice (LC 991)
```

1. **Local Placement Invariant (<a href="https://leetcode.com/problems/can-place-flowers/" target="_blank">LeetCode 605</a>)**: Immediate valid placement never degrades future placement capacity.
2. **Coverage Interval Doubling (<a href="https://leetcode.com/problems/patching-array/" target="_blank">LeetCode 330</a>)**: Maintain continuous sum coverage `[1, reach]`; patch `reach + 1` when gaps occur to double coverage.
3. **Reverse Perspective Choice (<a href="https://leetcode.com/problems/broken-calculator/" target="_blank">LeetCode 991</a>)**: Work backwards from target to start to convert branching choices into deterministic greedy decisions.

---

## Canonical Question Set (3 Questions)

### 1. Can Place Flowers (Understand)
- **Problem:** <a href="https://leetcode.com/problems/can-place-flowers/" target="_blank">LeetCode 605</a>
- **Difficulty:** Easy
- **Core Greedy Idea:** Immediate valid placement preserves future placement capacity.
- **Greedy Decision:** Iterate `i` from `0` to `len-1`. If `flowerbed[i] == 0` and both left and right neighbors are empty (or out of bounds), plant a flower immediately (`flowerbed[i] = 1`, `count++`).
- **Invariant / Proof Intuition:** Planting at index `i` consumes index `i+1`. Waiting to plant at `i+1` instead would consume index `i+2`. Planting at the earliest valid index `i` never reduces total placement capacity for the remaining array.
- **Recognition Clue:** Non-adjacent placement in a 1D array to maximize total items placed.
- **Common Wrong Approach:** Backtracking or counting zeros without mutating planted spots, leading to illegal adjacent placements.
- **Complexity:** Time: $O(N)$, Space: $O(1)$.
- **Cross-Pattern Connection:** Immediate valid placement greedy.

---

### 2. Patching Array (Apply & Recognize)
- **Problem:** <a href="https://leetcode.com/problems/patching-array/" target="_blank">LeetCode 330</a>
- **Difficulty:** Hard
- **Core Greedy Idea:** Continuous sum range coverage expansion `[1, reach]`.
- **Greedy Decision:** Maintain `reach` = maximum continuous sum value reachable from `1` to `reach`. Iterate through `nums`:
  - If `i < nums.length && nums[i] <= reach + 1`: Extend reach without patching (`reach += nums[i]`).
  - Else: Patch the array with `reach + 1`, doubling range (`reach += reach + 1`, `patches++`).
- **Invariant / Proof Intuition:** `currently construct [1 ... reach]`. If the next array value is `<= reach + 1`, coverage extends seamlessly. If `> reach + 1` or array is exhausted, patching `reach + 1` is mathematically optimal because it covers the missing value while maximizing the new upper bound (`2 * reach + 1`).
- **Recognition Clue:** Minimum element insertion to form all continuous subset sums up to $N$.
- **Common Wrong Approach:** Generating all subset sums ($O(2^K)$ exponential TLE) or patching arbitrary numbers instead of `reach + 1`.
- **Complexity:** Time: $O(\text{nums.length} + \log N)$, Space: $O(1)$.
- **Cross-Pattern Connection:** Mathematical range-doubling greedy invariant.

---

### 3. Broken Calculator (Recognize & Transfer)
- **Problem:** <a href="https://leetcode.com/problems/broken-calculator/" target="_blank">LeetCode 991</a>
- **Difficulty:** Medium
- **Core Greedy Idea:** Reverse execution perspective (target $\to$ start).
- **Greedy Decision:** Work backwards from `target` to `startValue`:
  - If `target > startValue` and `target` is **even**: Divide by 2 (`target /= 2`, `ops++`).
  - If `target > startValue` and `target` is **odd**: Add 1 (`target += 1`, `ops++`).
  - When `target <= startValue`: Add `(startValue - target)` remaining ops.
- **Invariant / Proof Intuition:** Moving forward from `startValue` to `target` creates exponential branching choices (double vs subtract). Moving backwards from `target` is deterministic: dividing an even number by 2 gets us to `startValue` in fewer steps than subtracting 1 multiple times.
- **Recognition Clue:** System operations problem where forward paths branch exponentially, but backward paths collapse into single deterministic choices.
- **Common Wrong Approach:** Forward BFS state space search ($O(2^{\text{target}})$ TLE / Memory Limit Exceeded).
- **Complexity:** Time: $O(\log(\text{target}))$, Space: $O(1)$.
- **Cross-Pattern Connection:** Backward execution greedy transformation.
