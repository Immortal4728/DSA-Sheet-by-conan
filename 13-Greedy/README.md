# Section 13 — Greedy Algorithms

> **Core Philosophy**: Greedy is not a data structure. It is a decision strategy.

---

## 1. Section Purpose & Core Concept

A **Greedy Algorithm** builds a global solution step-by-step by making locally optimal choices at each stage without ever looking back or undoing previous decisions. 

In SDE-1 technical interviews, Greedy is often misunderstood as memorizing sorting tricks or using heaps. This section trains **recognition, invariant formulation, and exchange proof reasoning**—enabling you to independently determine whether an unfamiliar problem permits a provably safe local decision.

---

## 2. What Greedy Means: The Decision-Making Model

To evaluate whether a problem can be solved greedily rather than through Dynamic Programming or Backtracking, apply this 6-step decision-making model:

```text
1. Local Choice          ──► Can I make a local decision at this step?
2. Feasibility           ──► Does that decision preserve overall feasibility?
3. Optimality            ──► Does that decision preserve global optimality?
4. Invariant Proof       ──► What invariant proves the decision is safe?
5. Exchange Argument     ──► Can an exchange argument justify the choice over any other option?
6. Future Flexibility    ──► Does the choice preserve maximum future flexibility?
```

### Exchange Argument Intuition
An **Exchange Argument** proves greedy correctness: assume a hypothetical optimal solution $O$ differs from our greedy solution $G$ at the first choice step. We demonstrate that replacing $O$'s first choice with $G$'s greedy choice produces a solution $O'$ that is valid and at least as good as $O$. By induction, $G$ is globally optimal.

---

## 3. How to Recognize Greedy Problems

Ask these diagnostic questions when encountering an unlabeled problem statement:

1. **Irreversible Safety**: Can I make an immediate local decision without needing to explore alternate branches?
2. **Interval Freedom**: Does choosing the earliest-ending interval maximize remaining timeline freedom?
3. **Reach Expansion**: Does maintaining a dynamic reach boundary eliminate the need for full state search?
4. **Reverse Perspective**: Does working backwards from `target` to `start` turn exponential branching into a single deterministic choice?
5. **Coverage Invariant**: Does patching `reach + 1` double the contiguous coverage range `[1, reach]`?

---

## 4. Final Architecture & Pattern Navigation

This module contains **25 canonical Greedy questions**, organized across 7 structured pattern files:

```text
13-Greedy/
├── Pattern-01-Greedy-Fundamentals-and-Exchange-Reasoning.md
├── Pattern-02-Interval-Boundary-and-Scheduling-Greedy.md
├── Pattern-03-Jump-and-Reachability-Greedy.md
├── Pattern-04-Sorting-and-Selection-Greedy.md
├── Pattern-05-Heap-Assisted-Greedy.md
├── Pattern-06-Greedy-Construction-and-Assignment.md
├── Pattern-07-Hidden-Greedy-Recognition.md
└── README.md
```

### Question Distribution Summary

| Pattern File | Canonical Core Questions | Core Focus & Decision Mechanism |
| :--- | :--- | :--- |
| **01 — Greedy Fundamentals & Exchange Reasoning** | 5 (<a href="https://leetcode.com/problems/assign-cookies/" target="_blank">LeetCode 455</a>, <a href="https://leetcode.com/problems/lemonade-change/" target="_blank">LeetCode 860</a>, <a href="https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/" target="_blank">LeetCode 122</a>, <a href="https://leetcode.com/problems/wiggle-subsequence/" target="_blank">LeetCode 376</a>, <a href="https://leetcode.com/problems/maximum-length-of-pair-chain/" target="_blank">LeetCode 646</a>) | Resource allocation, state conservation, accumulative profit, slope wiggle & earliest finish freedom |
| **02 — Interval, Boundary & Scheduling Greedy** | 5 (<a href="https://leetcode.com/problems/meeting-rooms/" target="_blank">LeetCode 252</a>, <a href="https://leetcode.com/problems/non-overlapping-intervals/" target="_blank">LeetCode 435</a>, <a href="https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons/" target="_blank">LeetCode 452</a>, <a href="https://leetcode.com/problems/partition-labels/" target="_blank">LeetCode 763</a>, <a href="https://leetcode.com/problems/video-stitching/" target="_blank">LeetCode 1024</a>) | End-time sorting, shared arrow point shooting, character boundary expansion & max reach stitching |
| **03 — Jump & Reachability Greedy** | 3 (<a href="https://leetcode.com/problems/jump-game/" target="_blank">LeetCode 55</a>, <a href="https://leetcode.com/problems/jump-game-ii/" target="_blank">LeetCode 45</a>, <a href="https://leetcode.com/problems/gas-station/" target="_blank">LeetCode 134</a>) | `max_reach` dynamic bounds, level-horizon window steps & circular deficit resets |
| **04 — Sorting & Selection Greedy** | 4 (<a href="https://leetcode.com/problems/maximum-units-on-a-truck/" target="_blank">LeetCode 1710</a>, <a href="https://leetcode.com/problems/boats-to-save-people/" target="_blank">LeetCode 881</a>, <a href="https://leetcode.com/problems/queue-reconstruction-by-height/" target="_blank">LeetCode 406</a>, <a href="https://leetcode.com/problems/bag-of-tokens/" target="_blank">LeetCode 948</a>) | Unit density sorting, two-pointer extreme pairing, tallest-first placement & token score trade-offs |
| **05 — Heap-Assisted Greedy** | 2 (<a href="https://leetcode.com/problems/ipo/" target="_blank">LeetCode 502</a>, <a href="https://leetcode.com/problems/minimum-number-of-refueling-stops/" target="_blank">LeetCode 871</a>) | Dynamic feasibility frontiers & deferred fuel choices using Max-Heaps |
| **06 — Greedy Construction & Assignment** | 3 (<a href="https://leetcode.com/problems/candy/" target="_blank">LeetCode 135</a>, <a href="https://leetcode.com/problems/minimum-time-to-make-rope-colorful/" target="_blank">LeetCode 1578</a>, <a href="https://leetcode.com/problems/two-city-scheduling/" target="_blank">LeetCode 1029</a>) | Two-pass directional constraints, same-color group max retention & cost differential sorting |
| **07 — Hidden Greedy Recognition** | 3 (<a href="https://leetcode.com/problems/can-place-flowers/" target="_blank">LeetCode 605</a>, <a href="https://leetcode.com/problems/patching-array/" target="_blank">LeetCode 330</a>, <a href="https://leetcode.com/problems/broken-calculator/" target="_blank">LeetCode 991</a>) | Immediate placement freedom, continuous coverage array patching `[1, reach]` & target-to-start reverse choices |
| **TOTAL** | **25 Canonical Core Questions** | — |

---

## 5. Canonical Ownership Rules & Cross-Section References

The repository enforces **single canonical ownership** for every problem.

### Canonical Greedy Ownership
- **P01**: LC 455, LC 860, LC 122, LC 376 (Moved from DP), LC 646.
- **P02**: LC 435, LC 452, LC 252, LC 763, LC 1024.
- **P03**: LC 55, LC 45 (Canonical Owner), LC 134.
- **P04**: LC 881, LC 406, LC 948, LC 1710.
- **P05**: LC 502, LC 871.
- **P06**: LC 135, LC 1578, LC 1029.
- **P07**: LC 605, LC 330, LC 991.

### Cross-Section References (Owned Elsewhere)
Do not duplicate these problems in Greedy. Reference them as *"You already solved this through another data structure. Now identify the greedy decision inside it."*

- **Section 01 — Arrays / Sorting**: <a href="https://leetcode.com/problems/largest-number/" target="_blank">LeetCode 179 (Largest Number)</a> (Custom comparator sorting).
- **Section 05 — Stacks**: <a href="https://leetcode.com/problems/remove-k-digits/" target="_blank">LeetCode 402 (Remove K Digits)</a> (Monotonic Stack greedy removal).
- **Section 08 — Heaps & Priority Queues**:
  - <a href="https://leetcode.com/problems/furthest-building-you-can-reach/" target="_blank">LeetCode 1642 (Furthest Building)</a>
  - <a href="https://leetcode.com/problems/course-schedule-iii/" target="_blank">LeetCode 630 (Course Schedule III)</a>
  - <a href="https://leetcode.com/problems/maximum-number-of-events-that-can-be-attended/" target="_blank">LeetCode 1353 (Max Events Attended)</a>
  - <a href="https://leetcode.com/problems/minimum-cost-to-hire-k-workers/" target="_blank">LeetCode 857 (Hire K Workers)</a>
  - <a href="https://leetcode.com/problems/maximum-performance-of-a-team/" target="_blank">LeetCode 1383 (Max Performance)</a>
  - <a href="https://leetcode.com/problems/reorganize-string/" target="_blank">LeetCode 767 (Reorganize String)</a>
  - <a href="https://leetcode.com/problems/task-scheduler/" target="_blank">LeetCode 621 (Task Scheduler)</a>

---

## 6. Mastery Criteria

This section is mastered when you can:

1. **Recognize greedy without the label** in unlabeled problem statements.
2. **Identify the exact local decision** required at each step.
3. **Explain why the decision is safe** using invariant logic.
4. **Identify the invariant** governing interval, reach, or coverage boundaries.
5. **Distinguish greedy from DP** (knowing when local decisions don't corrupt future options).
6. **Distinguish greedy from brute force / backtracking**.
7. **Recognize when sorting is merely a tool** to expose greedy choices.
8. **Recognize when a heap is merely supporting greedy** execution over a dynamic frontier.
9. **Solve the problem from a blank editor** in Java.
10. **Explain correctness verbally** using exchange arguments.
11. **State time and space complexity** accurately.
12. **Transfer the underlying reasoning** to unfamiliar interview problems.
