# Interview Execution System

> **The DSA curriculum is complete. This folder is about using it.**

The `interview-execution/` system bridges the gap between *knowing* DSA patterns and *performing* under live interview pressure. It is a structured mental framework — not additional theory.

---

## What This Folder Contains

| File | Purpose |
|---|---|
| [interview-execution.md](./interview-execution.md) | The complete SDE-1 interview execution guide |

---

## The Core Problem This Solves

You can know every pattern in the curriculum — sliding window, two pointers, Union-Find, DP, Tries — and still fail an interview.

Why? Because knowing a pattern and *deploying it correctly under pressure* are different skills.

The failure modes are predictable:

- You recognize the pattern but cannot explain your reasoning clearly.
- You jump to code before fully understanding the problem.
- You skip brute force and go straight to the optimized solution, confusing the interviewer.
- You solve the problem correctly but cannot state the complexity.
- You get stuck on an edge case and go silent for 3 minutes.
- You write working code but cannot walk through it when asked.

This system trains you to eliminate every one of these failure modes.

---

## The Mental Process (Summary)

```
Problem Received
      ↓
Read & Restate (confirm understanding)
      ↓
Clarify (edge cases, input type, output format)
      ↓
Constraint Analysis (determine target complexity)
      ↓
Brute Force (name it, state its complexity)
      ↓
Identify Bottleneck (what repeated work makes it slow?)
      ↓
Pattern Recognition (match to known technique)
      ↓
Choose Approach (data structure + algorithm + complexity)
      ↓
Code (clean, readable, commented)
      ↓
Dry Run (manual trace on the given example)
      ↓
Edge Cases (empty, single, extreme values)
      ↓
Explain (4-part template: approach, logic, complexity, verification)
      ↓
Follow-up Questions (answer with honesty and structure)
```

---

## How to Use This

### If you are preparing for interviews (starting now)

Read [`interview-execution.md`](./interview-execution.md) in full once. Understand the structure. Then immediately start **Mode 1 — Blind Solve** (Section 14) and apply the Quick Reference Card at the bottom of the guide to every problem you attempt from that point forward.

### If you have an interview in the next 7 days

Focus on:
1. Section 2 (Clarification Checklist) — internalize it, do not read from it
2. Section 3 (Constraint → Complexity Map) — memorize the table
3. Section 4 (Brute Force → Bottleneck → Optimization loop) — apply to 5 problems today
4. Section 8 (Explanation Template) — practice out loud until it flows naturally
5. Section 14 (Mode 5, Mock Interview) — simulate at least once before the real thing

### If you are maintaining a daily practice routine

Use the **Weekly Practice Rhythm** table in Section 14. Rotate modes deliberately. Never do the same mode two days in a row.

---

## Relationship to the DSA Curriculum

This folder is **not** a DSA section. It does not contain new algorithmic patterns, new canonical questions, or new theory.

The 15-section DSA curriculum is complete and locked:

| Section | Topic |
|---|---|
| 01 | Arrays, Searching & Sorting |
| 02 | Strings |
| 03 | Hashing & HashMaps |
| 04 | Linked Lists |
| 05 | Stacks & Queues |
| 06 | Binary Trees |
| 07 | Binary Search Trees |
| 08 | Heaps & Priority Queues |
| 09 | Recursion & Backtracking |
| 10 | Dynamic Programming |
| 11 | Graphs |
| 12 | Binary Search |
| 13 | Greedy Algorithms |
| 14 | Tries |
| 15 | Bit Manipulation |

This folder assumes that curriculum. It teaches you how to apply it.

---

## Related Guides at Repository Root

| Guide | Purpose |
|---|---|
| [HOW-TO-RECOGNIZE-PATTERNS.md](../HOW-TO-RECOGNIZE-PATTERNS.md) | Signal-to-pattern recognition reference |
| [HOW-TO-OPTIMIZE-AND-SELECT-APPROACHES.md](../HOW-TO-OPTIMIZE-AND-SELECT-APPROACHES.md) | Brute force → optimization framework |

---

*The interview is not a test of what you know. It is a test of how you think when you don't know.*
