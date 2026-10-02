# SDE-1 Interview Execution System

> **The goal is not to memorize solutions.**
> The goal is to develop the mental process that turns any unfamiliar problem into a correct, efficient, well-explained solution — live, under pressure.

---

## Table of Contents

1. [Interview Mindset](#1-interview-mindset)
2. [Problem Clarification](#2-problem-clarification)
3. [Constraint Analysis](#3-constraint-analysis)
4. [Brute Force → Bottleneck → Optimization](#4-brute-force--bottleneck--optimization)
5. [Pattern Recognition](#5-pattern-recognition)
6. [Choosing the Approach](#6-choosing-the-approach)
7. [Complexity Analysis](#7-complexity-analysis)
8. [Explanation Template](#8-explanation-template)
9. [Think-Aloud Training](#9-think-aloud-training)
10. [Coding Execution](#10-coding-execution)
11. [Manual Dry Run](#11-manual-dry-run)
12. [Self-Debugging](#12-self-debugging)
13. [Interview Follow-ups](#13-interview-follow-ups)
14. [Timed Practice Modes](#14-timed-practice-modes)

---

## 1. Interview Mindset

### What the Interviewer is Actually Evaluating

| Dimension | What They Watch For |
|---|---|
| **Clarity of thought** | Can you articulate what you understand before you code? |
| **Structured problem-solving** | Do you move from brute force → optimized with reasoning? |
| **Communication** | Do you think out loud, explain trade-offs, and narrate decisions? |
| **Coding fluency** | Is the code clean, readable, and correct on first attempt? |
| **Verification habit** | Do you test with examples before submitting? |
| **Composure under pressure** | Do you panic, or do you slow down and think? |

### Mindset Rules

1. **Silence is your enemy.** Always be speaking — even if you are thinking.
2. **Brute force first, always.** Never skip directly to the optimized solution without naming the brute force and why it is too slow.
3. **Ask before you assume.** One clarifying question prevents ten wrong minutes.
4. **Correctness before cleverness.** A working O(n²) solution beats a broken O(n log n) one.
5. **The interviewer is not your enemy.** They want you to succeed. Take hints gracefully.
6. **An imperfect solution explained well beats a perfect solution explained poorly.**

---

## 2. Problem Clarification

**Before writing a single line of code, ask questions.**

Even if the problem seems obvious, spend 60–90 seconds on clarification. This demonstrates maturity and prevents wasted effort.

### Clarification Checklist

```
Input:
  □ What is the data type of the input? (array of ints, string, graph edges?)
  □ Can the input be empty or null?
  □ Are values sorted or unsorted?
  □ Can values be negative, zero, or duplicate?
  □ Are strings ASCII only or Unicode?
  □ Are node/edge weights positive or can they be negative?

Output:
  □ What exactly should be returned? (index, value, boolean, list?)
  □ If multiple answers exist, return any one or all?
  □ Should the result be sorted?

Constraints:
  □ What is the maximum size of the input? (n = ?)
  □ Are there memory constraints?
  □ Do I need to modify the input in-place, or can I use extra space?

Edge Cases to Confirm:
  □ Empty input
  □ Single element
  □ All elements identical
  □ Already sorted / reverse sorted
  □ No valid answer exists — return -1, [], or null?
```

### How to Ask Without Sounding Unsure

> "Before I start — just to make sure I have the full picture — can the array contain duplicate values? And should I assume the input is always valid, or do I need to handle null?"

This sounds confident. You are verifying assumptions, not asking for help.

---

## 3. Constraint Analysis

**The constraint tells you the expected complexity. Read it like a signal.**

### The Complexity-Constraint Map

| Input Size (n) | Maximum Acceptable Complexity | Likely Approach |
|---|---|---|
| n ≤ 10 | O(n!) or O(2ⁿ) | Brute force / recursion / bitmask |
| n ≤ 20 | O(2ⁿ) | Bitmask DP, backtracking |
| n ≤ 100 | O(n³) | Triple nested loop |
| n ≤ 1,000 | O(n²) | Two nested loops |
| n ≤ 10,000 | O(n² log n) | Nested loop + sort |
| n ≤ 100,000 | O(n log n) | Sorting, heap, divide & conquer |
| n ≤ 1,000,000 | O(n) | Linear scan, hashmap, two-pointer |
| n ≤ 10,000,000 | O(n) or O(n log n) | Pure linear algorithms |

### Reading the Constraint Signal

- `n ≤ 10⁵` and time limit 1s → **must be O(n log n) or better**
- `n ≤ 10⁶` → **O(n) is the target**, avoid extra log factors
- `k` given as a small constant → **sliding window of size k is a strong hint**
- Values in range `[1, n]` → **in-place marking or cyclic sort is possible**
- Strings with only lowercase letters → **frequency array of size 26 is O(1) space**

---

## 4. Brute Force → Bottleneck → Optimization

**This is the core mental process. Never skip any step.**

### The Mental Loop

```
Step 1 — Brute Force
   What is the most straightforward, naive solution?
   What does it do for every element / pair / subset?

Step 2 — Analyze Complexity
   What is the time and space complexity of the brute force?
   Is it acceptable given the constraints? (use the table above)

Step 3 — Identify the Bottleneck
   Where does the brute force do repeated or unnecessary work?
   Which loop or operation is too slow?
   Is there a recomputed subproblem, a repeated scan, or redundant state?

Step 4 — Eliminate the Bottleneck
   What data structure or algorithm removes this repeated work?
   Can I precompute something? Sort? Use a window? Cache results?

Step 5 — Derive the Optimized Solution
   What is the time and space complexity now?
   Is there a further trade-off possible (e.g., O(n log n) → O(n))?
```

### Example Mental Loop (Applied)

**Problem:** Find two numbers in an array that sum to a target.

| Step | Thought |
|---|---|
| Brute Force | Try every pair: O(n²) |
| Bottleneck | For each element x, scanning all remaining elements for `target - x` is O(n) per element |
| Eliminate | Store seen elements in a hashmap → look up `target - x` in O(1) |
| Optimized | One pass, O(n) time, O(n) space |

---

## 5. Pattern Recognition

**When you see a problem, immediately map it to one of the known patterns.**

### Pattern Recognition Triggers

| Signal in Problem | Pattern to Consider |
|---|---|
| "subarray", "contiguous", fixed window size | Sliding Window |
| "pair", "triplet", sorted array, two ends | Two Pointers |
| "prefix sum", "range sum query" | Prefix Sum |
| "find k-th largest/smallest", "stream" | Heap / Priority Queue |
| "next greater element", "monotonic" | Monotonic Stack |
| "all subsets", "combinations", "permutations" | Backtracking |
| "overlapping subproblems", "optimal substructure" | Dynamic Programming |
| "grid", "island", "connected region" | BFS / DFS on Grid |
| "shortest path", "weighted graph" | Dijkstra / BFS |
| "cycle detection", "union-find" | Union-Find / DFS |
| "prefix word", "autocomplete", "search words" | Trie |
| "range", "interval merge/overlap" | Interval Greedy |
| "sorted array + binary search" | Binary Search on Answer |
| "XOR", "missing number", "single number" | Bit Manipulation |
| "sorted rotated array" | Modified Binary Search |
| "parentheses", "matching brackets" | Stack |
| "top-down reuse", "memoize recursive calls" | Memoization (DP) |
| "jump from index", "reach end" | Greedy / Jump |

### When Multiple Patterns Seem Valid

Use constraints to break the tie:

1. If `n ≤ 20` → backtracking/bitmask is acceptable
2. If `n ≤ 10⁵` → eliminate O(n²), look for O(n log n) or O(n)
3. If data is sorted → binary search is almost always relevant
4. If graph has equal weights → BFS gives shortest path
5. If graph has positive weights → Dijkstra
6. If problem asks for "all" answers → backtracking
7. If problem asks for "optimal" (min/max) → DP or Greedy

---

## 6. Choosing the Approach

**Finalize your approach before you write any code. State it explicitly.**

### Decision Checklist Before Coding

```
□ I have named the approach (e.g., "sliding window", "DP with memoization")
□ I know the time complexity of the approach
□ I know the space complexity of the approach
□ The complexity is acceptable for the given constraints
□ I have considered at least one alternative approach and chosen this one for a reason
□ I have communicated this choice to the interviewer
```

### How to State Your Approach

> "My plan is to use a monotonic stack. For each element, I'll maintain a decreasing stack and pop whenever I find a larger element — this gives me the next greater element in O(n) total time. Space is O(n) for the stack and result array. Does that sound good before I start coding?"

This one statement:
- Names the data structure
- Explains the core logic
- States complexity
- Invites the interviewer to redirect if needed

---

## 7. Complexity Analysis

**Always compute both time and space complexity. Do this before you finish.**

### Time Complexity — How to Compute It

```
1. Identify all loops and recursive calls.
2. For each loop, determine how many times it runs as a function of n.
3. Identify any nested loops — multiply their complexities.
4. Identify any log-factor operations (binary search, heap push/pop, tree traversal).
5. Add complexities for sequential steps; multiply for nested steps.
6. State the dominant term.
```

### Space Complexity — What Counts

```
Count all extra space beyond the input:
  □ Auxiliary data structures (hashmap, stack, queue, array)
  □ Recursive call stack depth
  □ Output array (sometimes excluded by convention — clarify)

Do NOT count:
  □ The input itself (unless you are modifying it in-place)
  □ A few scalar variables (these are O(1))
```

### Common Complexity Mistakes

| Mistake | Reality |
|---|---|
| "It's O(1) space because I only declared a few variables" | If you use a hashmap of size n, space is O(n) |
| "Binary search on the answer is O(log n)" | It's O(log(range) × cost_of_check) |
| "Recursion without memoization is fine" | Without memoization it is often O(2ⁿ) |
| "Sorting doesn't change complexity" | Sorting adds O(n log n) — include it |
| "BFS/DFS on a graph is O(n)" | It is O(V + E); for dense graphs E = O(V²) |

---

## 8. Explanation Template

**Use this template every time you finish coding.**

### The 4-Part Explanation

```
Part 1 — State the Approach
  "My approach uses [data structure / algorithm]. The core idea is [one sentence]."

Part 2 — Walk Through the Logic
  "For each [element / node / interval], I [do this action]. When [condition],
   I [handle this case]. At the end, I return [result]."

Part 3 — State the Complexity
  "Time complexity is O(___) because [brief reason].
   Space complexity is O(___) because [brief reason]."

Part 4 — Verify with Example
  "Let me trace through the example: input = [...].
   Step 1: [...]. Step 2: [...]. Final output: [...]."
```

### Example (Applied to Two Sum)

> "My approach uses a hashmap. The core idea is: for each element, check if its complement already exists in the map. If yes, return the pair. If no, store the current element and its index.
>
> Time complexity is O(n) — one pass through the array, with O(1) hashmap lookups. Space complexity is O(n) — in the worst case, all elements are stored in the map.
>
> Let me trace: input = [2, 7, 11, 15], target = 9. At index 0, value is 2, complement is 7 — not in map, store {2: 0}. At index 1, value is 7, complement is 2 — found in map at index 0. Return [0, 1]."

---

## 9. Think-Aloud Training

**Thinking out loud is a skill. It must be practiced deliberately.**

### What to Say at Every Phase

| Phase | What to Verbalize |
|---|---|
| Reading the problem | "So the input is... the output should be... I need to find..." |
| Clarifying | "I want to confirm — can the array have duplicates? Can it be empty?" |
| Brute force | "The naive approach would be to try every [pair/subset/path]..." |
| Identifying bottleneck | "The issue with that is it's O(n²) because for each element I'm scanning..." |
| Pattern recognition | "This reminds me of a sliding window / two-pointer / stack problem because..." |
| Approaching optimization | "If I use a hashmap / sort first / maintain a running max, I can avoid rescanning..." |
| While coding | "I'm initializing the left pointer here... I'm tracking the window sum... I'm checking the edge case where the array is empty..." |
| After coding | "Let me walk through the example to verify..." |
| After verification | "Edge cases I should check: empty input, single element, all duplicates..." |

### Drills

1. **Silent-to-loud:** Solve a problem first silently, then solve a different problem talking through every step aloud. Record yourself.
2. **Narrate the dry run:** Walk through every line of your code out loud using a concrete example.
3. **Explain to a non-programmer:** Explain your algorithm to someone unfamiliar with code. If you cannot, you do not understand it yet.

---

## 10. Coding Execution

**Write code that is clean and readable. Interviews value clarity.**

### Coding Standards

```
□ Use meaningful variable names (left, right, maxLen, freq, visited)
   NOT: l, r, m, x, tmp, a, b
□ Add one-line comments at each logical step
□ Handle edge cases at the top, before the main logic
□ Keep functions short — one function per logical concern
□ Prefer clarity over brevity (no one-liner tricks that obscure intent)
□ Use language idioms correctly (list comprehensions OK; avoid overly clever tricks)
```

### Edge Case Handling Pattern

```python
# Always handle at the top:
if not nums:
    return []
if len(nums) == 1:
    return [nums[0]]
```

### Common Coding Mistakes to Avoid

| Mistake | Prevention |
|---|---|
| Off-by-one errors in loops | Confirm: is end index inclusive or exclusive? |
| Forgetting to update loop variables | Trace through one iteration manually before submitting |
| Wrong return type | Re-read the problem output specification |
| Mutating input unexpectedly | Make a copy if input should not be modified |
| Not handling empty input | Add guard at the top |
| Integer overflow (in Java/C++) | Use `long` for large multiplications |

---

## 11. Manual Dry Run

**Run your code by hand before you submit. Always.**

### Dry Run Protocol

```
Step 1 — Choose the provided example first.
Step 2 — Write out the state of every variable at each step of the loop.
Step 3 — Confirm the output matches the expected output.
Step 4 — Then test an edge case: empty input, single element, all same values.
Step 5 — Then test a tricky case: values at boundaries, negative numbers if allowed.
```

### Dry Run Format

```
Input: nums = [1, 3, 2, 5], target = 4

Iteration 1: i=0, val=1, complement=3 → not in map → map={1:0}
Iteration 2: i=1, val=3, complement=1 → found at index 0 → return [0,1]

Output: [0, 1] ✓
```

Write this on paper or in comments. Do not trace in your head only.

---

## 12. Self-Debugging

**When your solution is wrong, debug systematically — not randomly.**

### Debugging Protocol

```
Step 1 — Read the failing test case carefully.
   What is the input? What is the expected output? What did you return?

Step 2 — Trace the code manually for that specific input.
   Do not change code yet. Find the exact line where the output diverges.

Step 3 — Identify the category of bug:
   □ Logic error (wrong algorithm)
   □ Off-by-one (boundary conditions)
   □ Missing edge case (empty, single element)
   □ Wrong variable updated (stale state)
   □ Wrong return value (type mismatch)

Step 4 — Fix precisely. Do not rewrite the entire function.

Step 5 — Re-run the original test case after fixing to verify you did not break it.
```

### Common Bug Patterns

| Bug Type | Symptom | Fix |
|---|---|---|
| Off-by-one | Loop misses last or first element | Change `< n` to `<= n` or vice versa |
| Stale state | Second test case fails but first passes | Reset variables between iterations/calls |
| Wrong sign | Subtraction gives negative unexpectedly | Check `a - b` vs `b - a` |
| Missing base case | Stack overflow or returns None | Add explicit base case in recursion |
| Unvisited node | Infinite loop in graph traversal | Mark node visited before pushing to queue |

---

## 13. Interview Follow-ups

**Interviewers often ask follow-up questions. Expect them. Prepare for them.**

### Common Follow-up Questions and How to Answer

| Question | How to Answer |
|---|---|
| "Can you optimize further?" | State what the current bottleneck is. Name the next technique that could reduce it. |
| "What if the input doesn't fit in memory?" | Propose external sort, streaming algorithms, or distributed processing. |
| "What if we need to handle concurrent updates?" | Mention locking, thread-safe data structures, or CAS operations. |
| "What is the space complexity if we don't count the output?" | Restate complexity excluding the result array. |
| "What if k can be larger than n?" | Add a guard: if k > n, handle as boundary case. |
| "What if the values are very large?" | Consider using `long` instead of `int` or BigInteger. |
| "How would you test this?" | State: unit tests for normal case, edge cases, large inputs for performance. |

### Follow-up Mindset

- Never say "I don't know" and stop. Say: "I haven't worked with that directly, but my instinct would be to [reasonable approach]. I'd want to verify that."
- Follow-ups are not traps. They test intellectual honesty and adaptability.
- It is acceptable to say: "That's a great point. I'd need to think more carefully about the distributed case. In a single-threaded environment my solution handles it as follows..."

---

## 14. Timed Practice Modes

**Use structured practice modes to build speed and composure under pressure.**

### Mode 1 — Blind Solve (Primary Mode)

```
1. Open a random LeetCode problem you have NOT seen before.
2. Set a timer: 35 minutes for Medium, 20 minutes for Easy, 50 minutes for Hard.
3. No hints. No editorial. No looking at tags.
4. Talk out loud through every step.
5. After submitting, review:
   □ What was the correct pattern?
   □ Did you identify it? When?
   □ What slowed you down?
   □ What would you do differently?
```

### Mode 2 — Pattern Drill

```
1. Pick one pattern (e.g., sliding window).
2. Solve 3 problems back-to-back in that pattern.
3. After each: explain out loud how the pattern manifested in this problem.
4. Goal: 15 minutes per medium problem, including explanation.
```

### Mode 3 — Complexity-First Drill

```
1. Read only the problem statement and constraints.
2. Before solving: state the target complexity (using the constraint map).
3. Name the approach that achieves that complexity.
4. Then solve.
5. Goal: pattern + approach identified within 3 minutes of reading.
```

### Mode 4 — Explanation-Only Drill

```
1. Pick any problem you have already solved.
2. Without re-reading your code: explain the full solution out loud.
3. Include: brute force, bottleneck, optimization, complexity, edge cases.
4. Time yourself: target under 3 minutes for a clear, complete explanation.
```

### Mode 5 — Mock Interview (Weekly)

```
1. Partner with a peer, or use a mock interview platform.
2. Take turns: one interviewer, one candidate.
3. Interviewer asks a problem without revealing the topic.
4. Candidate uses the full execution system from Step 2 through Step 13.
5. Debrief: interviewer gives feedback on communication, correctness, and composure.
```

### Weekly Practice Rhythm (Recommended)

| Day | Mode | Duration |
|---|---|---|
| Monday | Pattern Drill (pick one pattern) | 45 min |
| Tuesday | Blind Solve (2 mediums) | 60 min |
| Wednesday | Complexity-First Drill (3 problems) | 45 min |
| Thursday | Explanation-Only Drill (5 problems you've solved) | 30 min |
| Friday | Blind Solve (1 medium + 1 hard) | 60 min |
| Saturday | Mock Interview (full simulation) | 60 min |
| Sunday | Review week's errors + update pattern notes | 30 min |

---

## Quick Reference Card

```
When you receive a problem in an interview:

1. READ   — Understand what is asked. State it back in your own words.
2. CLARIFY — Ask about input type, edge cases, constraints, return format.
3. CONSTRAIN — Identify n. Determine acceptable complexity from the table.
4. BRUTE FORCE — Name the naive solution. State its complexity.
5. BOTTLENECK — Identify what makes it slow. Name the repeated work.
6. PATTERN — Match the problem to a known pattern.
7. APPROACH — Choose data structure / algorithm. State it with complexity.
8. CODE — Write clean, commented, readable code.
9. DRY RUN — Trace through the example manually.
10. EDGE CASES — Test empty, single element, and extreme values.
11. EXPLAIN — Use the 4-part explanation template.
12. FOLLOW-UPS — Answer with intellectual honesty and structured reasoning.
```
