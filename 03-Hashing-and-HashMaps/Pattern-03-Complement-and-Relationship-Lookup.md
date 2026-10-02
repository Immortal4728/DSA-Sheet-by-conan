# Pattern 03: Complement & Relationship Lookup

> For each element currently being processed, use the HashMap to ask: *"Is there a previously-seen element that completes the required relationship with this one?"*
> The HashMap stores not just what you've seen — it stores what you'll need in the future.

---

## Why This Pattern Exists

Many pair-finding problems look like they require $O(N^2)$ nested loops — checking every pair. The key insight that collapses this to $O(N)$ is:

> **If you're looking for a pair $(a, b)$ that satisfies some relationship $f(a, b) = \text{target}$, then for each $b$ you process, you can compute the required $a$ and look it up in $O(1)$ time.**

This transforms:
```text
Brute force: for each b, scan all previous a's → O(N²)
Optimized:   for each b, compute required a = g(b), check HashMap → O(N)
```

The HashMap's role here is not to count (Pattern 02) or detect duplicates (Pattern 01) — it is to **make a required complement instantly accessible**.

---

## When to Use This Pattern

Use this pattern when:

- You need to find two values that sum to (or satisfy a formula to reach) a target.
- You can compute what the "required partner" of the current element must be.
- Pair relationships can be expressed as a function: `complement = f(current, target)`.
- You need to count pairs satisfying a modular or arithmetic relationship.
- The problem involves pairing elements across two collections based on a shared sum or difference.

**Recognition Triggers:**
```text
"find two numbers that add to target"              ──► Complement = target - current
"count pairs where sum % k == 0"                   ──► Complement = (k - remainder) % k
"find pairs across two arrays with zero total sum" ──► Precompute all sums from two arrays, look up negatives
"count pairs with the same transformed value"      ──► Canonical transformation → frequency lookup
```

---

## The Core Mental Model

```text
seen = new HashMap<>()  // stores: required_value → count_of_occurrences

for each element x:
  complement = transform(x, target)       // compute what we need
  if seen.containsKey(complement):
    answer += seen.get(complement)        // use previously stored information
  seen.put(record(x), seen.getOrDefault(record(x), 0) + 1)  // store for future
```

> The key decision is: **what do you record in the HashMap?** Sometimes it's the element itself (`Two Sum`). Sometimes it's a transformed value (`Pair of Songs`, `Nice Pairs`). The transformation is the skill.

---

## ☕ Standard Java Templates

### 1. Classic Complement Lookup (Two Sum)
```java
public int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> seen = new HashMap<>(); // value → index
    for (int i = 0; i < nums.length; i++) {
        int complement = target - nums[i];
        if (seen.containsKey(complement))
            return new int[]{ seen.get(complement), i };
        seen.put(nums[i], i);
    }
    return new int[0];
}
```

### 2. Modular Complement Counting (Pair of Songs)
```java
public int numPairsDivisibleBy60(int[] time) {
    int[] remainder = new int[60];
    int count = 0;
    for (int t : time) {
        int r = t % 60;
        int complement = (60 - r) % 60;
        count += remainder[complement];
        remainder[r]++;
    }
    return count;
}
```

### 3. Pre-computed Sum Lookup (4Sum II)
```java
public int fourSumCount(int[] a, int[] b, int[] c, int[] d) {
    Map<Integer, Integer> sumAB = new HashMap<>();
    for (int x : a)
        for (int y : b)
            sumAB.put(x + y, sumAB.getOrDefault(x + y, 0) + 1);

    int count = 0;
    for (int x : c)
        for (int y : d)
            count += sumAB.getOrDefault(-(x + y), 0);
    return count;
}
```

---

## 🔍 Step-by-Step Dry Run

**Two Sum (`LeetCode 1`)** — `nums = [2, 7, 11, 15]`, `target = 9`

| Step | `nums[i]` | `complement = 9 - nums[i]` | `seen.contains(complement)` | Action | `seen` after |
|------|-----------|----------------------------|-----------------------------|--------|--------------|
| i=0 | `2` | `7` | No | Store `{2→0}` | `{2: 0}` |
| i=1 | `7` | `2` | **YES** at index 0 | Return `[0, 1]` | — |

---

## 🛑 Pattern Boundary: What Does NOT Belong Here

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **4Sum** | Two nested loops + Two Pointers on sorted array. Dominant pattern: **Sorting + Two Pointers**. |
| **Two Sum II** | Sorted input → opposite-direction two pointers. Dominant pattern: **Two Pointers**. |
| **Subarray Sum Equals K** | Prefix state + HashMap lookup. Dominant pattern: **Pattern 04: Prefix Sum + HashMap**. |
| **Count Pairs With XOR in Range** | Bit manipulation + Trie. Belongs to advanced bit topics. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 2 |
| **Medium** | 4 |
| **Total** | **6** |

---

## 🎯 Question Set (6 Questions)

### Q1. Two Sum
<a href="https://leetcode.com/problems/two-sum/" target="_blank">LeetCode 1</a> — **Easy**

**Target Skill:** Complement lookup — the canonical pair-finding pattern.

**Core Reasoning:**
- For each element `nums[i]`, compute `complement = target - nums[i]`. Check if that complement was already seen.
- Store `value → index` in the HashMap (not just membership, because you need the index).
- **The question:** *"Is there a previously-processed element that, together with this one, satisfies the target relationship?"*

**Why it belongs here:** This is the most direct expression of complement lookup. Every other problem in this pattern is a variation on the same question asked under different conditions (modular arithmetic, multi-array, frequency pairing).

---

### Q2. Number of Good Pairs
<a href="https://leetcode.com/problems/number-of-good-pairs/" target="_blank">LeetCode 1512</a> — **Easy**

**Target Skill:** Frequency map + pair counting — how many times has the complement (same value) been seen before?

**Core Reasoning:**
- A "good pair" is $(i, j)$ where `nums[i] == nums[j]` and `i < j`.
- For each element, the number of new good pairs it forms equals how many times its value was already seen.
- Store `value → count of occurrences so far`. For each new occurrence, add the current count to the answer.
- **The question:** *"How many previously-seen elements are equal to this one?"*

**Why it belongs here:** This is complement lookup where the "complement" is the element itself. It bridges frequency counting with pair relationship reasoning.

---

### Q3. 4Sum II
<a href="https://leetcode.com/problems/4sum-ii/" target="_blank">LeetCode 454</a> — **Medium**

**Target Skill:** Pre-computed sum lookup across two split collections.

**Core Reasoning:**
- Split the 4 arrays into two groups of 2. Compute all $N^2$ pairwise sums from `nums1 + nums2` and store them in a HashMap.
- For each pair sum from `nums3 + nums4`, look up whether `-(sum)` exists in the map.
- This reduces $O(N^4)$ brute force to $O(N^2)$ time.
- **The question:** *"Have I previously computed a sum from the first two arrays that completes the four-element relationship?"*

**Why it belongs here:** The dominant idea is complement lookup, but applied to precomputed sums across split collections rather than single elements.

---

### Q4. Pair of Songs With Total Durations Divisible by 60
<a href="https://leetcode.com/problems/pairs-of-songs-with-total-durations-divisible-by-60/" target="_blank">LeetCode 1010</a> — **Medium**

**Target Skill:** Modular complement lookup.

**Core Reasoning:**
- Two durations `a` and `b` form a valid pair if `(a + b) % 60 == 0`.
- For each duration, compute `remainder = time % 60`. The complement is `(60 - remainder) % 60`.
- Use an `int[60]` frequency array (bounded modular domain) to count how many previous remainders match the complement.
- **The transformation:** The complement relationship is on modular residues, not raw values.

**Why it belongs here:** This is the modular-arithmetic variant of Two Sum. The same complement lookup structure applies — only the domain of values and the definition of "complement" differs.

**Cross-pattern note:** This is analogous to the modular prefix-sum problems in Pattern 04, but the relationship is element-pair-based rather than subarray-based.

---

### Q5. Count Nice Pairs in an Array
<a href="https://leetcode.com/problems/count-nice-pairs-in-an-array/" target="_blank">LeetCode 1814</a> — **Medium**

**Target Skill:** Relationship transformation → canonical key → frequency counting.

**Core Reasoning:**
- A "nice pair" satisfies: `nums[i] + rev(nums[j]) == nums[j] + rev(nums[i])`.
- Rearranging: `nums[i] - rev(nums[i]) == nums[j] - rev(nums[j])`.
- For each element, compute `key = nums[i] - rev(nums[i])`. Count how many prior elements have the same key.
- **The transformation:** A seemingly complex pair condition collapses into a frequency lookup on a single derived value.

**Why it belongs here:** The key skill is **algebraic rearrangement of the pair condition into a canonical value that can be looked up in a HashMap**. This is the generalized version of Two Sum's `complement = target - current` insight.

---

### Q6. Count Number of Bad Pairs
<a href="https://leetcode.com/problems/count-number-of-bad-pairs/" target="_blank">LeetCode 2364</a> — **Medium**

**Target Skill:** Complement via total-pair counting — count good pairs efficiently, then subtract from total.

**Core Reasoning:**
- A bad pair $(i, j)$ satisfies `j - i != nums[j] - nums[i]`.
- Rearranging: a good pair satisfies `nums[i] - i == nums[j] - j`.
- Total pairs = $N \cdot (N-1) / 2$. Count good pairs using a frequency map on `nums[i] - i`. Bad pairs = Total - Good.
- **The transformation:** An inequality condition becomes a complement equality by computing a derived key.

**Why it belongs here:** Same rearrangement strategy as Q5. The relationship condition is transformed into an equality that a HashMap can directly count. Teaches counting the complement and subtracting.

---

## 🏆 Mastery Criteria

You have mastered Pattern 03 when you can, **without being told the pattern name**:

- [ ] Identify that a pair-finding problem can be solved without nested loops by computing the required complement.
- [ ] Decide what value/state to store in the HashMap (raw value, index, modular remainder, derived key).
- [ ] Transform complex pair conditions algebraically into a single lookup key.
- [ ] Distinguish Two Sum (index return) from Good Pairs (frequency count) and understand what changes in the HashMap design.
- [ ] Explain why 4Sum II is a complement lookup problem, not a four-pointer problem.
- [ ] Apply modular arithmetic to complement lookup (Pair of Songs variant).
- [ ] Recognize when "count bad pairs = total - count good pairs" is simpler than directly counting bad pairs.
- [ ] State time and space complexity for all six problems.

---

## ➡️ Next Pattern

Move to **[Pattern 04: Prefix Sum + HashMap](./Pattern-04-Prefix-Sum-and-HashMap.md)**.

The same complement lookup idea now applies not to individual elements, but to **prefix states of running sums** — enabling $O(N)$ subarray queries that would otherwise take $O(N^2)$.
