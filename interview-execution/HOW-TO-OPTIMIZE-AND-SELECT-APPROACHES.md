# SDE-1 Optimization, Complexity & Fast Approach Selection

## Objective

Train the ability to take an unfamiliar coding problem and quickly determine:

1. What the problem is actually asking.
2. What constraints matter — and what they silently imply.
3. What hidden edge cases exist even before writing code.
4. What the brute-force approach would be.
5. Why the brute-force approach is too slow or inefficient.
6. What repeated work or bottleneck exists.
7. Which pattern, data structure, algorithm, or optimization removes that bottleneck.
8. The **time complexity** and **space complexity** of every important approach.
9. Which approach is the best choice given the constraints — not just a valid one.
10. How to explain the reasoning clearly in an SDE-1 interview.

The goal is **not** to memorize solutions.

The goal is to develop this mental process:

```text
Problem
   ↓
Understand (restate in your own words)
   ↓
Read Constraints → Decode hidden signals
   ↓
Identify Hidden Edge Cases
   ↓
Brute Force
   ↓
Complexity
   ↓
Find Bottleneck / Repeated Work
   ↓
Recognize Pattern
   ↓
Choose Best Approach (not just a valid one)
   ↓
Optimize
   ↓
Verify Complexity
   ↓
Code
   ↓
Test
   ↓
Explain
```

---

## 1. Complexity Analysis

For every meaningful approach, analyze:

### Time Complexity

Determine how the running time grows with input size.

Recognize:
- $O(1)$
- $O(\log N)$
- $O(N)$
- $O(N \log N)$
- $O(N^2)$
- $O(N^3)$
- $O(2^N)$
- $O(N!)$
- and combinations such as $O(N \log K)$, $O(N + M)$, $O(V + E)$, etc.

Do not merely memorize these categories. Learn to derive them from the operations performed.

Ask:
```text
How many times does this loop execute?
Are loops nested?
Does the input shrink?
Does each pointer move only forward?
Is something being recomputed?
Is sorting involved?
Is a logarithmic data structure being used?
Is recursion branching?
How many states exist?
```

---

## 2. Space Complexity

Always distinguish:

### Auxiliary Space
Extra memory created by the algorithm.

Examples:
- HashMap / HashSet $\to O(N)$
- Array $\to O(N)$
- Recursion stack $\to O(\text{depth})$
- Queue $\to O(\text{width})$
- Stack $\to O(N)$
- Heap $\to O(K)$

Do not automatically count the input itself as extra space unless the problem requires a copied/modified structure.

For recursive algorithms, explicitly consider:
```text
recursion depth
+
temporary data structures
+
stored states / memoization
```

Also recognize when an algorithm is $O(N)$ time, $O(1)$ extra space versus $O(N)$ time, $O(N)$ extra space.

---

## 3. Brute Force First

Before optimizing, understand the straightforward solution.

For every problem ask:
```text
What would I naturally do if performance did not matter?
```

Then determine:
```text
Brute Force:
Time = ?
Space = ?
```

Do not immediately jump to a known pattern. The brute-force solution helps reveal the bottleneck.

---

## 4. Find the Bottleneck

This is the most important optimization skill.

After finding the brute-force approach, ask:

### What is making it slow?

Common bottlenecks:
```text
Repeated searching
Repeated scanning
Repeated calculation
Nested loops
Repeated sorting
Repeated traversal
Recomputing the same subproblem
Checking every pair
Checking every substring
Checking every possible answer
Repeated min/max lookup
Repeated frequency calculation
Repeated graph traversal
```

Then ask:
> **Can I avoid doing this work repeatedly?**

This question should become automatic.

---

## 5. Optimization Thinking

Learn to transform:

```text
Repeated Work
      ↓
Store / Organize / Eliminate / Restrict
      ↓
Faster Algorithm
```

### Common Transformations

#### Repeated Lookup
```text
O(N) search  ──►  HashSet / HashMap  ──►  O(1) average lookup
```

#### Repeated Range Calculation
```text
Recalculate every range  ──►  Prefix Sum  ──►  O(1) range query
```

#### Repeated Pair Checking
```text
Nested loops  ──►  Hashing / Two Pointers / Sorting  ──►  Reduced complexity
```

#### Repeated Window Calculation
```text
Recalculate each window  ──►  Sliding Window  ──►  Reuse previous window state
```

#### Repeated Previous-Element Comparison
```text
Repeated scanning  ──►  Monotonic Stack / Deque  ──►  Process each element efficiently
```

#### Repeated Selection of Minimum / Maximum
```text
Repeated scan  ──►  Heap / Priority Queue  ──►  Efficient selection
```

#### Repeated Subproblems
```text
Recursive recomputation  ──►  Memoization / Tabulation  ──►  Dynamic Programming
```

#### Sorted Structure Search
```text
Linear search  ──►  Binary Search  ──►  O(log N)
```

#### Unknown Optimal Answer
Ask: *"Can I test whether an answer X is possible?"*
If feasibility is monotonic:
```text
Search answer space  ──►  Binary Search on Answer
```

---

## 6. Pattern Identification Without Being Told the Topic

During interview simulation, NEVER assume the interviewer tells you: *"This is a sliding-window problem."*

Identify the pattern from the problem's structure. Look for signals.

### Sliding Window
```text
contiguous subarray / substring
+
longest / shortest / count
+
window can expand/shrink
```

### Two Pointers
```text
ordered array
+
pair relationship
+
left/right movement
```

### Hashing
```text
fast lookup
+
frequency
+
duplicates
+
complement
+
grouping
```

### Prefix Sum
```text
repeated range sums
+
subarray sum
+
cumulative contribution
```

### Monotonic Stack
```text
next greater/smaller
+
previous greater/smaller
+
nearest boundary
+
maintain increasing/decreasing structure
```

### Heap
```text
repeatedly need smallest/largest
+
Top K
+
dynamic ordering
+
scheduling
```

### Binary Search
```text
sorted structure
OR
monotonic condition
OR
minimum/maximum feasible answer
```

### Greedy
```text
local choice
+
future feasibility remains intact
+
exchange / ordering argument
+
optimize without exploring every combination
```

### Dynamic Programming
```text
overlapping subproblems
+
optimal substructure
+
state can represent previous decisions
```

### Graph
Recognize graphs hidden inside:
```text
connections / dependencies / routes / transformations / relationships / states / grids / networks
```

### Trie
```text
prefix
+
dictionary
+
word lookup
+
prefix matching
+
character-by-character branching
```

### Bit Manipulation
```text
binary representation
+
XOR properties
+
bit states
+
bitmask
+
power-of-two properties
```

---

## 7. Understanding Constraints & Reading Hidden Signals

Constraints are not just limits — they are **design instructions**. Every constraint tells you something about what the intended approach must be.

### The Constraint Decoding Table

| Constraint | What It Implies | Likely Approach |
|---|---|---|
| $n \le 10$ | Exponential is fine | Brute force, bitmask, recursion |
| $n \le 20$ | $O(2^n)$ is acceptable | Bitmask DP, backtracking |
| $n \le 100$ | $O(n^3)$ is acceptable | Triple loop, Floyd-Warshall |
| $n \le 1{,}000$ | $O(n^2)$ is acceptable | Two nested loops, DP |
| $n \le 10{,}000$ | $O(n^2)$ borderline | Try to reach $O(n \log n)$ |
| $n \le 10^5$ | Must be $O(n \log n)$ or $O(n)$ | Sorting, heap, two pointers, sliding window |
| $n \le 10^6$ | Must be $O(n)$ or very tight $O(n \log n)$ | Linear scan, hashmap, prefix sum |
| $n \le 10^7$ | Must be $O(n)$ with low constant factor | Pure linear, avoid extra allocations |
| Values in $[1, n]$ | In-place indexing is possible | Cyclic sort, index marking |
| Values in $[0, 10^9]$ | Cannot use array-indexed frequency | Use hashmap instead |
| Only lowercase letters | 26-size frequency array is $O(1)$ space | Fixed-size array, bitmask |
| $k$ is small (given explicitly) | Sliding window of size $k$, or heap of size $k$ | Fixed-window, top-K heap |
| Weights are positive | Safe to use Dijkstra | Dijkstra's algorithm |
| Weights can be negative | Dijkstra is wrong | Bellman-Ford |
| Graph has no cycles (DAG) | Topological order applies | Topological sort + DP |
| Sorted input | Binary search is a strong candidate | Binary search, two pointers |
| "At most one move" / "exactly one" | Usually requires comparing original and modified state | Before/after comparison, diff tracking |
| Multiple queries on the same data | Precompute offline | Prefix sum, sparse table, segment tree |
| String of length $n$ with $q$ queries | O(n + q) must be the target | Preprocessing + O(1) per query |

### Reading Hidden Signals in the Problem Statement

Even without explicit constraints, the **problem's phrasing** encodes the approach:

| Phrase / Structure | Hidden Signal |
|---|---|
| "Find any valid answer" | Greedy often works; no need for global optimal |
| "Find the minimum / maximum" | DP, binary search on answer, or greedy |
| "Return all possible" | Backtracking / recursion |
| "Count the number of ways" | DP (not greedy) |
| "Contiguous subarray" | Sliding window or prefix sum |
| "Non-contiguous subsequence" | DP or greedy |
| "Two arrays" / "two sorted lists" | Two pointers or merge logic |
| "At most K" | Binary search on answer, or sliding window with budget |
| "Connected components" | Union-Find or BFS/DFS |
| "Reaches from source to target" | BFS shortest path or DFS reachability |
| "Transform word A to word B" | BFS on implicit graph (word ladder) |
| "No two adjacent" | Greedy + ordering, or DP |
| "Minimum cost to reach" | Dijkstra or DP |
| "All pairs" | Floyd-Warshall or repeated single-source |
| Input is a tree | Recursive DFS, postorder thinking |
| Input is a BST | Inorder traversal gives sorted order |

### Constraint Combinations That Force a Specific Approach

```
n ≤ 10⁵  +  "subarray"  +  "max sum"     → Kadane's / Sliding Window
n ≤ 10⁵  +  "sorted"    +  "pair sum"    → Two Pointers
n ≤ 10⁵  +  "frequency" +  "top K"       → Heap (size K)
n ≤ 10⁵  +  "prefix"   +  "range query" → Prefix Sum
n ≤ 10⁶  +  "unique"    +  "lookup"      → HashSet in O(1)
n ≤ 20   +  "subset"   +  "optimal"     → Bitmask DP
graph    +  "shortest path" + positive weights → Dijkstra
graph    +  "ordering"  +  "dependency"  → Topological Sort
string   +  "prefix"    +  "search"      → Trie
```

---

## 8. Identifying Hidden Edge Cases

Edge cases are not surprises — they are **predictable categories**. Train yourself to mentally scan all of them before writing a single line of code.

### The Edge Case Checklist

```
Input Structure:
  □ Empty input (empty array, empty string, empty graph)
  □ Single element
  □ Two elements (minimal non-trivial case)
  □ All elements identical
  □ All elements distinct
  □ Already sorted (ascending)
  □ Reverse sorted (descending)
  □ Large input at the constraint boundary (n = 10⁵ or 10⁶)

Value Range:
  □ Minimum possible value (0, 1, or INT_MIN)
  □ Maximum possible value (INT_MAX, 10⁹)
  □ Negative numbers (if allowed)
  □ Zero (if allowed)
  □ Duplicate values (if allowed)
  □ Values that sum or multiply to overflow (int → use long)

Structure-Specific:
  Array:    □ Length 0, 1, 2  □ All same  □ Sorted/reverse sorted
  String:   □ Empty string  □ Single char  □ All same chars  □ Unicode/special chars
  Tree:     □ Null root  □ Single node  □ Skewed (linear chain)
  Graph:    □ No edges  □ Disconnected  □ Cycle  □ Self-loop
  Linked List: □ Null head  □ Single node  □ Cycle  □ Even vs odd length
  Matrix:   □ 1×1  □ Single row  □ Single column  □ All same value

Logical Edge Cases:
  □ Target not found (return -1, [], or null)
  □ All elements qualify (return full array)
  □ No element qualifies (return 0 or empty)
  □ Constraints are tightly at the boundary (k = n, window = array length)
  □ Overflow: does any sum/product exceed int range?
  □ The answer is the first element
  □ The answer is the last element
  □ The answer is the whole array/string
```

### How to Use This at Interview Time

**Before coding:** Spend 30 seconds scanning the checklist mentally. Name 2–3 edge cases out loud.

> "Before I start, let me note the edge cases I'll handle: empty array returns -1, single element is always valid, and I need to check for integer overflow since values can reach 10⁹."

**After coding:** Run at least two edge cases manually — one normal example, one boundary example.

### Common Bugs That Come From Missing Edge Cases

| Missing Edge Case | Resulting Bug |
|---|---|
| Empty array | Index out of bounds on `arr[0]` |
| Single element | Loop body never executes, returns wrong default |
| All duplicates | Incorrect count or uniqueness assumption fails |
| Negative numbers | Wrong comparison in min/max, incorrect prefix sums |
| Target = first element | Off-by-one in binary search |
| Target = last element | Loop exits before checking last element |
| Integer overflow | Sum of two 10⁹ values overflows int silently |
| Disconnected graph | BFS/DFS misses unreachable components |
| Skewed tree | Stack overflow in recursive DFS |
| All elements in window | Window equals full array, boundary check fails |

---

## 9. Approach Selection Under Constraints

Do not ask: *"What is the most advanced algorithm I know?"*

Ask: **"What is the simplest approach that comfortably satisfies the constraints?"**

For example:
- $N \le 20$: Exponential $O(2^N)$ or $O(N!)$ solutions may be completely acceptable.
- $N \le 10^3$: $O(N^2)$ is usually acceptable.
- $N \le 10^5$: $O(N^2)$ is too slow; target $O(N \log N)$ or $O(N)$.
- $N \le 10^6$: Even $O(N \log N)$ requires careful attention; target $O(N)$ or $O(\log N)$.

---

## 10. Choosing the Best Optimized Approach

Having a valid approach is not enough. The best approach is the one that is **correct, efficient, and safe to implement** under interview constraints.

### The Best-Approach Decision Framework

```
Step 1 — Is the brute force already within the constraint?
    Yes → Implement it. Don't over-engineer.
    No  → Continue.

Step 2 — What is the bottleneck in the brute force?
    Name it precisely: "nested loop", "repeated scan", "recomputed subproblem"

Step 3 — Which transformation removes that bottleneck?
    See Section 5 (Optimization Thinking) for the full list.

Step 4 — Do multiple valid approaches exist?
    Yes → Use the comparison table below.
    No  → Implement the one that works.

Step 5 — Choose the approach that is:
    □ Correct (passes all cases including edge cases)
    □ Within complexity budget
    □ Simplest to implement correctly in ~20 minutes
    □ Easiest to explain
```

### Comparing Multiple Valid Approaches

When more than one approach satisfies the constraints, use this table:

| Criterion | Why It Matters |
|---|---|
| **Correctness** | A faster wrong solution scores zero |
| **Implementation complexity** | A simpler $O(n \log n)$ beats a buggy $O(n)$ |
| **Constant factor** | $O(n)$ with heavy memory allocation can be slower than clean $O(n \log n)$ in practice |
| **Space usage** | If memory is limited, prefer lower-space alternatives |
| **Explainability** | You must articulate the choice; pick what you can explain clearly |

### When Two Approaches Have the Same Complexity

```
Same O(n log n)?
  → Choose the one with lower implementation risk.
  → Sorting + linear scan is usually safer than segment tree.

Same O(n)?
  → Choose the one with lower space.
  → Two pointers over hashmap if sorted input allows it.

Same correctness?
  → Choose the approach you can implement and explain in under 20 minutes.
```

### Common "Good vs Best" Approach Traps

| Problem Type | Common Valid Approach | Best Approach | Why |
|---|---|---|---|
| Two sum in sorted array | HashMap O(n) space | Two Pointers O(1) space | Sorted input allows in-place |
| Top K frequent elements | Sort all + take K | Heap of size K | O(n log K) vs O(n log n) |
| Subarray sum = target | Prefix sum + hashmap | Same — already optimal | No trap here |
| Shortest path unweighted | DFS | BFS | BFS gives shortest path; DFS does not |
| Detect cycle in directed graph | DFS with visited | DFS with 3-color | 3-color catches back edges correctly |
| Range min query with many queries | Linear scan per query | Sparse table | O(1) per query after O(n log n) build |
| Find median of stream | Sort each time | Two heaps | O(log n) insert vs O(n log n) |

### The Final Sanity Check Before You Start Coding

```
□ My approach handles the empty input case.
□ My approach handles single element.
□ My approach handles all-duplicates input.
□ My time complexity is within the constraint budget.
□ My space complexity is acceptable (no unnecessary O(n) data structure).
□ I can implement this in ~20 minutes.
□ I can explain the trade-off if asked.
```

---

## 11. Compare Approaches

For difficult problems, explicitly compare:

| Approach | Time Complexity | Space Complexity | Main Bottleneck / Issue |
| :--- | :--- | :--- | :--- |
| **Brute Force** | $O(N^2)$ or $O(2^N)$ | $O(1)$ | Repeated scanning / exponential branching |
| **Improved** | $O(N \log N)$ | $O(N)$ | Sorting overhead |
| **Optimal** | $O(N)$ | $O(N)$ or $O(1)$ | Comfortably passes constraints |

Then ask:
- Which approach satisfies the constraints?
- Which approach is simplest and safest to implement?
- Is the extra space worth the time reduction?

---

## 12. Time vs Space Trade-offs

Learn to recognize:
```text
More Space  ──►  Less Time
Less Space  ──►  More Time
```

Examples:
- Brute force: $O(N^2)$ time, $O(1)$ space.
- Hashing: $O(N)$ average time, $O(N)$ space.
- Recursive subproblems: $O(2^N)$ time vs DP Memoization: $O(\text{states})$ time with state space storage.

---

## 13. Optimization Ladder

For every problem, mentally climb this ladder:

```text
Level 0  ──► Understand the problem
Level 1  ──► Read constraints → decode hidden signals
Level 2  ──► Identify hidden edge cases
Level 3  ──► Brute force
Level 4  ──► Analyze complexity
Level 5  ──► Find bottleneck
Level 6  ──► Remove repeated work
Level 7  ──► Recognize pattern / data structure
Level 8  ──► Choose the best approach (not just a valid one)
Level 9  ──► Optimize complexity
Level 10 ──► Implement cleanly
Level 11 ──► Verify complexity
Level 12 ──► Explain the solution
```

Become fast at moving from **Level 4 $\to$ Level 8**.

---

## 14. Fast Interview Decision Process

Train this mental checklist until it becomes automatic:

```text
1.  What exactly must I return?
2.  What are the constraints? (n = ? values = ? sorted? unique?)
3.  What does the constraint silently imply about the approach?
4.  What are the likely hidden edge cases? (empty, single, overflow, no answer)
5.  What does the input structure tell me?
6.  What is the obvious brute force?
7.  What is its complexity?
8.  What part is expensive?
9.  What work is repeated?
10. Can I store it?
11. Can I sort it?
12. Can two pointers eliminate work?
13. Can a window reuse work?
14. Can hashing give faster lookup?
15. Can a stack/deque maintain useful state?
16. Can a heap maintain the required extreme?
17. Is there a monotonic property?
18. Can I binary-search the answer?
19. Are there overlapping subproblems?
20. Is this secretly a graph/tree?
21. What time complexity do the constraints allow?
22. Of all valid approaches, which is simplest, safest, and most explainable?
```

---

## 15. Interview Explanation Template

When explaining your solution in an interview, structure your verbal delivery:

```text
"I'll start with the straightforward approach."

"The brute-force complexity is O(...)."

"The bottleneck is ..."

"We are repeatedly doing ..."

"We can eliminate that repeated work by using ..."

"That gives us O(...) time and O(...) extra space."

"Given the constraints, this is sufficient."

"Now I'll implement it and test the important edge cases."
```

---

## 16. Complexity Verification After Coding

After writing the code, inspect it again. Ask:
- Did I accidentally introduce a nested loop?
- Did I call a linear operation inside a loop?
- Did I sort more than once?
- Did recursion branch unexpectedly?
- What is the maximum depth of the call stack?
- How much memory can the HashMap/Heap contain?

Then state: **Time: $O(...)$, Space: $O(...)$** with concise justification.

---

## 17. Recognition Training Progression

```text
Round 1: Pattern Known       ──► Learn the technique deeply.
Round 2: Pattern Hidden      ──► Mix problems; identify the technique yourself.
Round 3: Section Mixed       ──► Choose between data structures and algorithms.
Round 4: Unseen Problems     ──► Derive the approach rather than recall it.
Round 5: Timed Interview     ──► Understand -> Recognize -> Optimize -> Implement -> Explain.
```

---

## Final Mastery Standard

A problem is not considered mastered merely because it was solved. You should be able to:
- Derive the brute-force solution and calculate its time and space complexity.
- Identify the exact bottleneck and explain the repeated work.
- Recognize the applicable pattern and choose the appropriate data structure.
- Derive the optimized complexity and explain the time/space trade-off.
- Implement from a blank editor without tutorial dependency.
- Handle edge cases cleanly and explain the final solution verbally.

> **Final Principle:** Don't memorize the optimization. Understand what makes the brute-force solution slow, then learn to recognize the structure that removes that cost.
