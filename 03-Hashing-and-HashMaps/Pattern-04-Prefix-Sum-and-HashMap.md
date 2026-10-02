# Pattern 04: Prefix Sum + HashMap

> Extend the complement lookup idea from individual elements to **running prefix states**.
> For each prefix sum up to index $i$, use a HashMap to ask in $O(1)$: *"Has a previous prefix state existed such that the subarray between then and now satisfies my condition?"*

---

## Why This Pattern Exists

You already know basic Prefix Sum from the Arrays module:
- `prefix[i]` = sum of all elements from index `0` to `i-1`.
- To find the sum of any range `[l, r]`: `prefix[r+1] - prefix[l]` in $O(1)$.

But many subarray problems ask questions like:
- *"Does any subarray sum to exactly $K$?"*
- *"What is the longest subarray with sum divisible by $P$?"*
- *"What is the longest subarray with equal 0s and 1s?"*

These cannot be answered by simple prefix difference — you need to look up *which previous prefix index* satisfies the required relationship. That lookup is the HashMap's role.

> **The core equation:** If `prefix[i] - prefix[j] == K`, then the subarray `[j, i-1]` sums to `K`.
> Rewritten: `prefix[j] = prefix[i] - K`.
> So for each current prefix, you look up `prefix[i] - K` in the HashMap.

This is complement lookup applied to prefix states — Pattern 03's insight, elevated to running sums.

---

## When to Use This Pattern

Use this pattern when:

- You need to count or find subarrays satisfying an exact sum condition.
- You need the length of the longest/shortest subarray with a sum or modular property.
- You need to detect whether any subarray sum satisfies a divisibility condition.
- You are transforming a 0/1 array into a ±1 form to find balanced subarrays.
- The problem involves prefix sums and a secondary condition that requires a historical lookup.

**Recognition Triggers:**
```text
"subarray with sum equal to K"             ──► prefix[i] - K → HashMap count lookup
"subarray sum divisible by K"              ──► prefix[i] % K → modular complement lookup
"longest subarray with equal 0s and 1s"   ──► remap 0→-1, prefix=0 anchor lookup
"longest well-performing interval"        ──► remap scores, prefix state → first-seen index lookup
"does any subarray sum to a multiple of K" ──► prefix % K, check if seen before (pigeonhole)
```

---

## The Core Mental Model

```text
prefixSum = 0
map = new HashMap<>()
map.put(0, 1)   // Base case: prefix sum 0 exists once before index 0

for each element nums[i]:
  prefixSum += nums[i]

  required = prefixSum - K         // What previous prefix sum would give us sum K?
  answer  += map.getOrDefault(required, 0)

  map.put(prefixSum, map.getOrDefault(prefixSum, 0) + 1)
```

> **The base case `map.put(0, 1)` is critical.** It handles the case where the subarray starts from index 0.

### Longest Subarray Variant (store first-seen index)
```text
map = new HashMap<>()
map.put(0, -1)   // prefix state 0 first seen at index -1 (before array starts)

for each index i:
  update prefixState
  if map.containsKey(prefixState):
    length = i - map.get(prefixState)   // distance from first occurrence
  else:
    map.put(prefixState, i)             // store FIRST occurrence only
```

---

## ☕ Standard Java Templates

### 1. Count Subarrays with Sum = K
```java
public int subarraySum(int[] nums, int k) {
    Map<Integer, Integer> map = new HashMap<>();
    map.put(0, 1); // prefix sum 0 seen once
    int prefix = 0, count = 0;
    for (int num : nums) {
        prefix += num;
        count += map.getOrDefault(prefix - k, 0);
        map.put(prefix, map.getOrDefault(prefix, 0) + 1);
    }
    return count;
}
```

### 2. Longest Subarray with Sum = 0 (or Contiguous Array)
```java
public int maxSubarrayLen(int[] nums, int k) {
    Map<Integer, Integer> firstSeen = new HashMap<>();
    firstSeen.put(0, -1);
    int prefix = 0, maxLen = 0;
    for (int i = 0; i < nums.length; i++) {
        prefix += nums[i];
        if (firstSeen.containsKey(prefix - k)) {
            maxLen = Math.max(maxLen, i - firstSeen.get(prefix - k));
        } else {
            firstSeen.put(prefix, i); // store FIRST occurrence for longest span
        }
    }
    return maxLen;
}
```

### 3. Prefix Modulo (Subarray Sum Divisible by K)
```java
public int subarraysDivByK(int[] nums, int k) {
    Map<Integer, Integer> map = new HashMap<>();
    map.put(0, 1);
    int prefix = 0, count = 0;
    for (int num : nums) {
        prefix = ((prefix + num) % k + k) % k; // normalize to positive
        count += map.getOrDefault(prefix, 0);
        map.put(prefix, map.getOrDefault(prefix, 0) + 1);
    }
    return count;
}
```

---

## 🔍 Step-by-Step Dry Run

**Subarray Sum Equals K (`LeetCode 560`)** — `nums = [1, 1, 1]`, `k = 2`

Initial state: `prefix = 0`, `map = {0: 1}`, `count = 0`

| i | `nums[i]` | `prefix` after | `required = prefix - k` | `map.get(required)` | `count` | `map` after |
|---|-----------|----------------|--------------------------|---------------------|---------|-------------|
| 0 | `1` | `1` | `1 - 2 = -1` | `0` | `0` | `{0:1, 1:1}` |
| 1 | `1` | `2` | `2 - 2 = 0` | `1` | `1` | `{0:1, 1:1, 2:1}` |
| 2 | `1` | `3` | `3 - 2 = 1` | `1` | `2` | `{0:1, 1:1, 2:1, 3:1}` |

**Answer: 2** (subarrays `[1,1]` at indices `[0,1]` and `[1,2]`)

---

## 🛑 Pattern Boundary: What Does NOT Belong Here

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Two Sum** | Direct element-pair complement lookup, no prefix states. Belongs to **Pattern 03**. |
| **Maximum Subarray** | Kadane's Algorithm — dynamic programming on running sums. Belongs to **Arrays Pattern 07**. |
| **Prefix Sum Range Queries** | Static precomputed prefix array lookups with no HashMap. Belongs to **Arrays Pattern 04**. |
| **Range Sum Query 2D** | 2D prefix sums with static lookup. Belongs to **Arrays Pattern 08**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Medium** | 6 |
| **Hard** | 1 |
| **Total** | **7** |

---

## 🎯 Question Set (7 Questions)

### Q1. Subarray Sum Equals K
<a href="https://leetcode.com/problems/subarray-sum-equals-k/" target="_blank">LeetCode 560</a> — **Medium**

**Target Skill:** Prefix sum + HashMap count lookup — the canonical problem of this pattern.

**Core Reasoning:**
- For every prefix sum `P` at index `i`, any subarray ending at `i` with sum `K` started at an index where the prefix sum was `P - K`.
- Store prefix sum frequencies. For each new prefix, look up `prefix - K`.
- **The question:** *"How many previous prefix sums equal exactly `prefix - K`?"*

**Why it belongs here:** This is the first and most important prefix-sum + HashMap problem. It bridges the Arrays Prefix Sum pattern with Hashing by showing that a HashMap can store historical prefix states, not just element counts.

**Key discipline:** The HashMap stores count of occurrences (because multiple indices can have the same prefix sum). The base case `map.put(0, 1)` handles subarrays that start from index 0.

---

### Q2. Continuous Subarray Sum
<a href="https://leetcode.com/problems/continuous-subarray-sum/" target="_blank">LeetCode 523</a> — **Medium**

**Target Skill:** Prefix modulo + HashMap existence check (pigeonhole reasoning).

**Core Reasoning:**
- A subarray `[l, r]` has sum divisible by `k` iff `prefix[r] % k == prefix[l-1] % k`.
- Remap the problem: store modular remainders. If the same remainder appears twice with at least one element between them, the interval between is divisible by `k`.
- Store `remainder → first-seen index`. Check if current index − stored index ≥ 2.

**Why it belongs here:** Introduces modular prefix state — the same complement lookup structure, but in the modular domain. The "pigeonhole" reasoning (same remainder twice → divisible subarray) is the key insight.

---

### Q3. Contiguous Array
<a href="https://leetcode.com/problems/contiguous-array/" target="_blank">LeetCode 525</a> — **Medium**

**Target Skill:** Prefix state transformation + earliest-index storage for longest-subarray reasoning.

**Core Reasoning:**
- Remap: 0 → -1, 1 → +1. Now finding the longest subarray with equal 0s and 1s becomes: find the longest subarray with sum = 0.
- A running prefix sum that returns to a previously-seen value means the subarray between those two indices has sum 0.
- Store `prefix → first occurrence index`. For each repeat, compute the span.

**Why it belongs here:** Introduces two crucial extensions:
1. Input transformation that makes the prefix-sum approach applicable.
2. Storing indices instead of counts (because you want the longest span, not the count).

**Cross-pattern note:** This is the "longest subarray" variant of Subarray Sum Equals K. Same structure, different stored information in the HashMap.

---

### Q4. Subarray Sums Divisible by K
<a href="https://leetcode.com/problems/subarray-sums-divisible-by-k/" target="_blank">LeetCode 974</a> — **Medium**

**Target Skill:** Prefix modulo + frequency map — count subarrays divisible by K.

**Core Reasoning:**
- Two prefix sums with the same modular remainder define a subarray divisible by `K`.
- Build a frequency map of prefix remainders. For each new remainder, add its current frequency to the answer.
- Handle negative modulo: `remainder = ((prefix % k) + k) % k`.

**Why it belongs here:** The counting version of Continuous Subarray Sum (Q2). Extends modular prefix reasoning to counting all valid subarrays instead of checking existence.

---

### Q5. Binary Subarrays With Sum
<a href="https://leetcode.com/problems/binary-subarrays-with-sum/" target="_blank">LeetCode 930</a> — **Medium**

**Target Skill:** Prefix sum count lookup on binary arrays.

**Core Reasoning:**
- Count subarrays with sum exactly `goal` in a binary array (0s and 1s only).
- Direct application of Subarray Sum Equals K on a binary input. The structure is identical; the binary constraint may suggest alternative sliding window approaches, but prefix + HashMap is the cleaner general solution.
- **Why this after Q1?** It reinforces the pattern on a restricted input domain and introduces the candidate to recognize the same structure in different problem framings.

**Why it belongs here:** Structural twin of Q1, but the binary domain makes it appear different at first glance. Teaches pattern-recognition: recognize the same core structure under different surface descriptions.

---

### Q6. Make Sum Divisible by P
<a href="https://leetcode.com/problems/make-sum-divisible-by-p/" target="_blank">LeetCode 1590</a> — **Medium**

**Target Skill:** Prefix modulo + smallest-removal reasoning with index lookup.

**Core Reasoning:**
- Total sum `S` has remainder `r = S % p`. We need to remove the shortest subarray whose sum ≡ r (mod p).
- For each prefix, compute `current_mod % p`. The subarray to remove has remainder `r`. So look for a previous prefix with remainder `(current_mod - r + p) % p`.
- Store `remainder → most recent index` to minimize subarray length.

**Why it belongs here:** Elevates the pattern: you're not counting subarrays — you're minimizing the length of a subarray to remove. The HashMap stores the most recent (not first) occurrence, reversing the index-storage strategy from Q3.

---

### Q7. Longest Well-Performing Interval
<a href="https://leetcode.com/problems/longest-well-performing-interval/" target="_blank">LeetCode 1124</a> — **Hard**

**Target Skill:** Transformed prefix state + earliest-index storage for longest-subarray reasoning on a derived state.

**Core Reasoning:**
- A "tiring day" scores +1; a non-tiring day scores -1.
- A "well-performing interval" has more tiring days than non-tiring days → running sum > 0.
- If `prefix > 0`, the answer candidate is the entire prefix from the start (length = `i + 1`).
- Otherwise, look for the earliest index where `prefix - 1` was seen (finding the longest span with sum ≥ 1).
- Store `prefix → first occurrence index`.

**Why it belongs here:** This is the hardest problem in this set. It requires:
1. Translating the problem into a prefix state.
2. Recognizing when the entire prefix qualifies vs. when a span-maximizing lookup is needed.
3. Combining multiple cases in one pass.

**Why it's here and not elsewhere:** The entire solution is prefix-state tracking + earliest-index HashMap lookup. No other dominant pattern applies.

---

## 🏆 Mastery Criteria

You have mastered Pattern 04 when you can, **without being told the pattern name**:

- [ ] State the core equation: `prefix[i] - prefix[j] = K` → look up `prefix[j] = prefix[i] - K`.
- [ ] Explain why `map.put(0, 1)` is necessary and what subarray it handles.
- [ ] Distinguish between storing **counts** (for counting problems) vs. **first-seen indices** (for longest-subarray problems).
- [ ] Apply modular prefix reasoning: two equal remainders define a divisible subarray.
- [ ] Handle negative modulo correctly in Java: `((prefix % k) + k) % k`.
- [ ] Remap a 0/1 array to ±1 and explain why it converts the problem to "sum = 0".
- [ ] Recognize when to store the most-recent index (minimize removal) vs. first-seen index (maximize span).
- [ ] State time $O(N)$ and space $O(N)$ for all seven problems.

---

## ➡️ Next Pattern

Move to **[Pattern 05: Composite Hashing](./Pattern-05-Composite-Hashing.md)**.

In composite hashing, a HashMap or HashSet is one component of a larger data structure or algorithm. The challenge is not using a HashMap — it is making multiple structures work together coherently.
