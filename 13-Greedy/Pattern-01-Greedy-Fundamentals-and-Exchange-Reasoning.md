# Pattern 01 — Greedy Fundamentals & Exchange Reasoning

Greedy algorithms construct solutions step-by-step by making locally optimal decisions without backtracking. The foundational proof mechanism for greedy correctness is the **Exchange Argument**: demonstrating that any hypothetical optimal solution can be transformed step-by-step into the greedy solution without degrading quality.

---

## Learning Progression

```text
Understand (Matching / Allocation) ──► Apply (State Conservation) ──► Recognize (Local Profit Accumulation) ──► Transfer (Direction Changes & Exchange Reasoning)
```

1. **Understand (Matching)**: Match the smallest resource to the smallest requirement (<a href="https://leetcode.com/problems/assign-cookies/" target="_blank">LeetCode 455</a>).
2. **Apply (State Conservation)**: Conserve flexible high-value resources by spending restricted ones first (<a href="https://leetcode.com/problems/lemonade-change/" target="_blank">LeetCode 860</a>).
3. **Recognize (Accumulative Profit)**: Harvest every locally profitable increment (<a href="https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/" target="_blank">LeetCode 122</a>).
4. **Transfer (Direction Changes & Exchange)**: Track slope changes and earliest-finish interval boundaries to maximize future choice freedom (<a href="https://leetcode.com/problems/wiggle-subsequence/" target="_blank">LeetCode 376</a>, <a href="https://leetcode.com/problems/maximum-length-of-pair-chain/" target="_blank">LeetCode 646</a>).

---

## Canonical Question Set (5 Questions)

### 1. Assign Cookies (Understand)
- **Problem:** <a href="https://leetcode.com/problems/assign-cookies/" target="_blank">LeetCode 455</a>
- **Difficulty:** Easy
- **Core Greedy Idea:** Pair smallest effective resource with smallest demand.
- **Greedy Decision:** Sort both greed array `g` and cookie array `s`. Match the child with the smallest greed factor to the smallest cookie size that satisfies them (`s[j] >= g[i]`).
- **Invariant / Proof Intuition:** Saving larger cookies for children with higher greed factors preserves maximum matching capacity for remaining larger demands.
- **Recognition Clue:** Sorting resources and demands to maximize total completed assignments under 1-to-1 matching constraints.
- **Common Wrong Approach:** Attempting to assign largest cookies first without sorting greed factors, wasting high-value cookies on low-demand children.
- **Complexity:** Time: $O(N \log N + M \log M)$, Space: $O(1)$.
- **Cross-Pattern Connection:** Connects to Pattern 04 (Sorting & Selection Greedy).

---

### 2. Lemonade Change (Apply)
- **Problem:** <a href="https://leetcode.com/problems/lemonade-change/" target="_blank">LeetCode 860</a>
- **Difficulty:** Easy
- **Core Greedy Idea:** Conserve versatile change (\$5 bills) for future transactions.
- **Greedy Decision:** When receiving a \$20 bill, prefer giving one \$10 bill and one \$5 bill as change instead of three \$5 bills.
- **Invariant / Proof Intuition:** A \$5 bill can serve as change for both \$10 and \$20 transactions, whereas a \$10 bill can only serve \$20 transactions. Conserving \$5 bills strictly increases future transaction feasibility.
- **Recognition Clue:** Fixed transaction fee with limited denomination counters where lower denominations are strictly more flexible.
- **Common Wrong Approach:** Giving three \$5 bills for a \$20 bill even when a \$10 bill is available, causing premature \$5 bill exhaustion.
- **Complexity:** Time: $O(N)$, Space: $O(1)$.
- **Cross-Pattern Connection:** Connects to state-invariant checking patterns.

---

### 3. Best Time to Buy and Sell Stock II (Recognize)
- **Problem:** <a href="https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/" target="_blank">LeetCode 122</a>
- **Difficulty:** Medium
- **Core Greedy Idea:** Sum all positive consecutive price increments.
- **Greedy Decision:** Whenever `prices[i] > prices[i-1]`, buy on day `i-1` and sell on day `i`.
- **Invariant / Proof Intuition:** Any multi-day gain $P_C - P_A$ equals $(P_B - P_A) + (P_C - P_B)$. Accumulating all positive 1-day slopes captures the global maximum sum without missing any upward trends.
- **Recognition Clue:** Unlimited transactions with zero friction fees where total profit equals sum of positive differences.
- **Common Wrong Approach:** Trying to find global minimums and maximums via complex peak-valley state machines when simple consecutive delta summation achieves the exact optimum.
- **Complexity:** Time: $O(N)$, Space: $O(1)$.
- **Cross-Pattern Connection:** Contrast with State-Machine DP (Section 10 Pattern 07) where cooldowns or fees prevent pure daily slope accumulation.

---

### 4. Wiggle Subsequence (Transfer)
- **Problem:** <a href="https://leetcode.com/problems/wiggle-subsequence/" target="_blank">LeetCode 376</a>
- **Difficulty:** Medium
- **Core Greedy Idea:** Track slope direction changes and pick extreme peaks/troughs.
- **Greedy Decision:** Increment wiggle count whenever the sign of adjacent difference changes (`diff > 0` after `diff < 0` or vice versa).
- **Invariant / Proof Intuition:** Selecting the extreme peak or valley maximizes the difference available for the next element to reverse direction, preserving maximum flexibility for future choices.
- **Recognition Clue:** Alternating sequence constraints where intermediate monotonic elements can be safely bypassed.
- **Common Wrong Approach:** Using $O(N^2)$ sequence DP when local peak/trough extraction solves the problem in $O(N)$ linear time.
- **Complexity:** Time: $O(N)$, Space: $O(1)$.
- **Cross-Pattern Connection:** Canonically owned by Greedy P01; cross-referenced in DP (Section 10 Pattern 04).

---

### 5. Maximum Length of Pair Chain (Transfer)
- **Problem:** <a href="https://leetcode.com/problems/maximum-length-of-pair-chain/" target="_blank">LeetCode 646</a>
- **Difficulty:** Medium
- **Core Greedy Idea:** Earliest finish time selection preserves maximum future freedom.
- **Greedy Decision:** Sort pairs by ending value `pairs[i][1]` ascending. Greedily pick the next pair whose start value `c > current_end`.
- **Invariant / Proof Intuition:** *If I choose this interval now, how much future freedom am I preserving?* Selecting the pair with the smallest end value leaves the largest possible remaining range available for subsequent pairs to attach.
- **Recognition Clue:** Chaining pairs/intervals with strict boundary conditions where minimizing end points maximizes chain length.
- **Common Wrong Approach:** Sorting by starting value `pairs[i][0]`, which fails when a pair with a small start value has a huge end value that blocks multiple subsequent pairs.
- **Complexity:** Time: $O(N \log N)$, Space: $O(1)$.
- **Cross-Pattern Connection:** Connects directly to Pattern 02 (Interval Scheduling Greedy).
