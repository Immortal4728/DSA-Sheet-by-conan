# Pattern 01: Membership & Duplicate Detection

> The simplest and most fundamental hashing question: *"Have I seen this element before?"*
> A HashSet converts an $O(N)$ repeated scan into an $O(1)$ membership check.

---

## Why This Pattern Exists

In brute force, checking whether a value was previously seen requires scanning everything you've already processed — $O(N)$ per element, $O(N^2)$ total.

A HashSet stores every element you've visited. Future membership queries cost $O(1)$ average time regardless of how many elements are stored.

This is not just about finding duplicates. The deeper skill is:

> **Recognizing when your algorithm needs to ask "have I visited this state/value before?" and answering that in $O(1)$ time.**

This mental model extends from simple duplicate detection to cycle detection, sequence starts, and set-based intersection reasoning.

---

## When to Use This Pattern

Use Membership & Duplicate Detection when:

- You need to detect repeated elements in a single traversal.
- You need to check whether a computed value already exists in a visited set.
- You need to find elements present in both or one of two collections.
- You need to determine whether a value is a *starting point* of a structure (e.g., sequence start).
- You need to detect a cycle by checking if a computed state was already visited.

**Recognition Triggers:**
```text
"return true if any element appears more than once"  ──► HashSet membership
"have I arrived at a state I've seen before?"        ──► Cycle detection via HashSet
"find elements common to two arrays"                 ──► Set intersection
"is this value the start of a sequence?"             ──► Anchor check via HashSet
```

---

## The Core Mental Model

```text
Brute Force:
  For each element, scan all previous elements → O(N²)

HashSet Optimization:
  seen = new HashSet<>()

  for each element x:
    if seen.contains(x) → answer found in O(1)
    else                → seen.add(x)

Total time: O(N)  |  Space: O(N)
```

The tradeoff: you spend $O(N)$ space to buy $O(1)$ per lookup.

---

## ☕ Standard Java Template

```java
// Pattern: HashSet Membership (Duplicate Detection)
public boolean hasDuplicate(int[] nums) {
    Set<Integer> seen = new HashSet<>();
    for (int num : nums) {
        if (!seen.add(num)) return true; // add() returns false if already present
    }
    return false;
}
```

```java
// Pattern: HashSet Membership (Visited-state cycle detection)
public boolean isHappy(int n) {
    Set<Integer> seen = new HashSet<>();
    while (n != 1) {
        if (!seen.add(n)) return false; // State revisited → cycle
        n = sumOfSquares(n);
    }
    return true;
}
```

```java
// Pattern: Set Intersection (cross-collection membership)
public int[] intersection(int[] nums1, int[] nums2) {
    Set<Integer> set1 = new HashSet<>();
    for (int n : nums1) set1.add(n);

    Set<Integer> result = new HashSet<>();
    for (int n : nums2) {
        if (set1.contains(n)) result.add(n);
    }
    return result.stream().mapToInt(i -> i).toArray();
}
```

---

## 🔍 Step-by-Step Dry Run

**Contains Duplicate (`LeetCode 217`)** — `nums = [1, 3, 4, 2, 2]`

| Step | `num` | `seen.contains(num)` | Action | `seen` after step |
|------|-------|----------------------|--------|-------------------|
| i=0 | `1` | No | Add `1` | `{1}` |
| i=1 | `3` | No | Add `3` | `{1, 3}` |
| i=2 | `4` | No | Add `4` | `{1, 3, 4}` |
| i=3 | `2` | No | Add `2` | `{1, 3, 4, 2}` |
| i=4 | `2` | **YES** | Return `true` | — |

---

## 🛑 Pattern Boundary: What Does NOT Belong Here

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Find the Duplicate Number** | Dominant reasoning: cycle detection via Floyd's algorithm or binary search on index constraints. HashSet membership is a valid but suboptimal approach that misses the $O(1)$ space insight. |
| **Isomorphic Strings** | Bidirectional character mapping — belongs to **String Pattern 02**. |
| **Subarray Sum Equals K** | Prefix state + HashMap — belongs to **Pattern 04: Prefix Sum + HashMap**. |
| **Two Sum** | Complement lookup — belongs to **Pattern 03: Complement & Relationship Lookup**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 3 |
| **Medium** | 2 |
| **Total** | **5** |

---

## 🎯 Question Set (5 Questions)

### Q1. Contains Duplicate
<a href="https://leetcode.com/problems/contains-duplicate/" target="_blank">LeetCode 217</a> — **Easy**

**Target Skill:** HashSet membership — the foundation of every visited-state lookup.

**Core Reasoning:**
- Brute force: nested loop checking every pair — $O(N^2)$.
- Optimized: add each element to a HashSet. If `add()` returns `false`, the element already exists.
- **The question to ask at every element:** *"Have I seen this before?"*

**Why it belongs here:** This is the cleanest, most direct expression of the membership pattern. One structure. One question. One check.

---

### Q2. Contains Duplicate II
<a href="https://leetcode.com/problems/contains-duplicate-ii/" target="_blank">LeetCode 219</a> — **Easy**

**Target Skill:** Sliding HashSet window — membership constrained to a window of size $k$.

**Core Reasoning:**
- You need to know if the same value appeared within the last $k$ indices.
- Maintain a HashSet of the current window. When the window exceeds size $k$, evict the oldest element.
- **The upgrade from Q1:** Membership is no longer global. It is window-local.

**Why it belongs here:** Same membership question, but constrained to a range. This teaches that the "seen" set does not have to be unlimited — it can be managed as a sliding structure.

**Cross-pattern note:** This is a lightweight variant of Sliding Window thinking applied to a HashSet.

---

### Q3. Happy Number
<a href="https://leetcode.com/problems/happy-number/" target="_blank">LeetCode 202</a> — **Easy**

**Target Skill:** Cycle detection via visited-state HashSet.

**Core Reasoning:**
- Repeatedly apply a transformation (`sum of squares of digits`).
- If the result reaches `1`, return `true`. If you arrive at a state you've seen before — a cycle — return `false`.
- **The question to ask at every state:** *"Have I computed this intermediate value before?"*

**Why it belongs here:** The membership structure is not operating on input elements — it is tracking computed transformation states. This broadens the pattern: you are checking *"have I visited this state?"* not just *"have I seen this number?"*

---

### Q4. Intersection of Two Arrays
<a href="https://leetcode.com/problems/intersection-of-two-arrays/" target="_blank">LeetCode 349</a> — **Easy**

**Target Skill:** Cross-collection membership — convert one collection into a HashSet, query the other against it.

**Core Reasoning:**
- Convert `nums1` into a HashSet.
- For each element in `nums2`, check if it exists in the set. Collect unique matches.
- **The question to ask:** *"Is this element present in the other collection?"*

**Why it belongs here:** This is membership lookup across two separate data sources, not within one array. Teaches how a HashSet enables $O(1)$ inter-collection queries without sorting.

**Cross-pattern note:** Intersection of Two Arrays II (LeetCode 350) uses frequency counts → belongs to **Pattern 02**.

---

### Q5. Longest Consecutive Sequence
<a href="https://leetcode.com/problems/longest-consecutive-sequence/" target="_blank">LeetCode 128</a> — **Medium**

**Target Skill:** HashSet + sequence anchor detection to avoid redundant traversal.

**Core Reasoning:**
- Load all values into a HashSet for $O(1)$ lookup.
- For each element, only begin counting a sequence if it is a **sequence start**: `!set.contains(num - 1)`.
- Extend each sequence by checking `set.contains(num + 1)`, `set.contains(num + 2)`, etc.
- This guarantees each element is visited at most twice — $O(N)$ total.

**Why it belongs here:** The main algorithmic insight is **membership-based sequence anchor detection**, not sorting or dynamic programming. The HashSet is the entire optimization. Without it, you'd need $O(N \log N)$ sorting or $O(N^2)$ brute force.

**The question to ask:** *"Is this value the start of a sequence? (i.e., is `num - 1` absent from the set?)"*

---

## 🏆 Mastery Criteria

You have mastered Pattern 01 when you can, **without being told the pattern name**:

- [ ] Recognize that "have I seen this before?" requires a HashSet, not a sorted search.
- [ ] Explain why adding to a HashSet and checking `add()` return value is more idiomatic than two separate calls.
- [ ] Distinguish global membership from window-scoped membership (Q1 vs Q2).
- [ ] Recognize cycle detection as a membership pattern on computed states, not input values.
- [ ] Explain why Longest Consecutive Sequence uses a HashSet instead of sorting.
- [ ] Identify the "sequence start anchor" condition and explain why it prevents $O(N^2)$ behavior.
- [ ] State time $O(N)$ and space $O(N)$ complexity for all five problems.
- [ ] Recognize when Find the Duplicate Number is *not* a membership problem.

---

## ➡️ Next Pattern

Once membership and duplicate detection is natural, move to **[Pattern 02: Frequency, Grouping & Canonical Keys](./Pattern-02-Frequency-Grouping-and-Canonical-Keys.md)**.

The next step is not just *"have I seen this?"* but *"how many times have I seen this, and can I group elements that share the same character composition?"*
