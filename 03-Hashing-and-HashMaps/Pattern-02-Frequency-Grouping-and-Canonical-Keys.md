# Pattern 02: Frequency, Grouping & Canonical Keys

> Count how many times each element appears, then use that frequency state to compare, group, rank, or select.
> The central question shifts from *"have I seen this?"* to *"how many times have I seen this — and what does that tell me?"*

---

## Why This Pattern Exists

Many problems require you to reason about the distribution of elements, not just their presence. A HashSet cannot answer "how many times?" or "which elements share the same character composition?" — but a HashMap or frequency array can.

This pattern covers three related but distinct ideas:

1. **Frequency counting** — Build a count map, then query it (anagram checks, uniqueness detection).
2. **Canonical key grouping** — Transform different-looking inputs into the same key when they share an equivalence property (e.g., same character set → same sorted string key).
3. **Frequency + selection/ordering** — Build the frequency map first, then use a secondary mechanism (heap, bucket sort, sorted map) to select or rank by frequency.

---

## When to Use This Pattern

Use this pattern when:

- You need to verify that two collections have exactly the same distribution of elements.
- You need to find the element that appears most, least, or first as unique.
- You need to group strings or arrays that are "equivalent" under some transformation.
- You need to select the top-$K$ most/least frequent elements.
- You need to rank or sort elements by their occurrence count.

**Recognition Triggers:**
```text
"are these two strings anagrams?"                  ──► Frequency comparison
"find the first character that appears only once"   ──► Frequency + second pass
"group words that are rearrangements of each other" ──► Canonical key + HashMap grouping
"find the K most frequent elements"                 ──► Frequency map + selection (heap/bucket)
"sort characters by how often they appear"          ──► Frequency map + ordering
"find the majority element"                         ──► Frequency tracking + threshold check
```

---

## The Core Mental Model

```text
Phase 1 — Build the frequency map:
  freq = new HashMap<>()
  for each element x:
    freq.put(x, freq.getOrDefault(x, 0) + 1)

Phase 2 — Use the frequency map to answer the question:
  for each key in freq:
    if freq.get(key) == 1  → first unique element
    if freq.get(key) > n/2 → majority element
    if freq.get(key) >= K  → top-K candidate
    canonical key          → group anagram
```

> **Key discipline:** Always separate Phase 1 (information gathering) from Phase 2 (information use). Never mix them unless you have an explicit reason.

---

## ☕ Standard Java Templates

### 1. Frequency Array (bounded character set)
```java
// Valid Anagram check using int[26]
public boolean isAnagram(String s, String t) {
    if (s.length() != t.length()) return false;
    int[] freq = new int[26];
    for (char c : s.toCharArray()) freq[c - 'a']++;
    for (char c : t.toCharArray()) {
        if (--freq[c - 'a'] < 0) return false;
    }
    return true;
}
```

### 2. Frequency HashMap (general)
```java
// Build frequency map, then second pass for first unique
public int firstUniqChar(String s) {
    Map<Character, Integer> freq = new HashMap<>();
    for (char c : s.toCharArray())
        freq.put(c, freq.getOrDefault(c, 0) + 1);

    for (int i = 0; i < s.length(); i++)
        if (freq.get(s.charAt(i)) == 1) return i;

    return -1;
}
```

### 3. Canonical Key Grouping
```java
// Group Anagrams: sort each word to get its canonical key
public List<List<String>> groupAnagrams(String[] strs) {
    Map<String, List<String>> map = new HashMap<>();
    for (String s : strs) {
        char[] chars = s.toCharArray();
        Arrays.sort(chars);
        String key = new String(chars); // Canonical key
        map.computeIfAbsent(key, k -> new ArrayList<>()).add(s);
    }
    return new ArrayList<>(map.values());
}
```

### 4. Frequency + Bucket Sort (Top K Frequent)
```java
public int[] topKFrequent(int[] nums, int k) {
    Map<Integer, Integer> freq = new HashMap<>();
    for (int n : nums) freq.put(n, freq.getOrDefault(n, 0) + 1);

    // Bucket: index = frequency, value = list of elements with that frequency
    List<Integer>[] bucket = new List[nums.length + 1];
    for (int key : freq.keySet()) {
        int f = freq.get(key);
        if (bucket[f] == null) bucket[f] = new ArrayList<>();
        bucket[f].add(key);
    }

    List<Integer> result = new ArrayList<>();
    for (int i = bucket.length - 1; i >= 0 && result.size() < k; i--)
        if (bucket[i] != null) result.addAll(bucket[i]);

    return result.stream().mapToInt(i -> i).toArray();
}
```

---

## 🔍 Step-by-Step Dry Run

**Group Anagrams (`LeetCode 49`)** — `strs = ["eat", "tea", "tan", "ate", "nat", "bat"]`

| Word | Sorted (Canonical Key) | Mapped To |
|------|-----------------------|-----------|
| `"eat"` | `"aet"` | Group A |
| `"tea"` | `"aet"` | Group A |
| `"tan"` | `"ant"` | Group B |
| `"ate"` | `"aet"` | Group A |
| `"nat"` | `"ant"` | Group B |
| `"bat"` | `"abt"` | Group C |

**Output:** `[["eat","tea","ate"], ["tan","nat"], ["bat"]]`

> The canonical key transformation is the entire algorithmic insight. Without it, you'd need $O(N^2)$ pairwise comparisons.

---

## 🛑 Pattern Boundary: What Does NOT Belong Here

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Isomorphic Strings** | Bidirectional character mapping, not frequency comparison. Belongs to **Strings Pattern 02**. |
| **Find All Anagrams in a String** | Fixed-size sliding window frequency match. Dominant pattern: **String Sliding Window**. |
| **Two Sum** | Complement lookup, not frequency counting. Belongs to **Pattern 03**. |
| **Subarray Sum Equals K** | Prefix sum + HashMap index storage. Belongs to **Pattern 04**. |
| **Longest Consecutive Sequence** | HashSet membership anchor check. Belongs to **Pattern 01**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 4 |
| **Medium** | 3 |
| **Total** | **7** |

---

## 🎯 Question Set (7 Questions)

### Q1. Valid Anagram
<a href="https://leetcode.com/problems/valid-anagram/" target="_blank">LeetCode 242</a> — **Easy**

**Target Skill:** Frequency comparison — do two strings have the exact same character distribution?

**Core Reasoning:**
- Build a frequency array or map for string `s`. Decrement for string `t`. Any negative entry means mismatch.
- **The question:** *"Can both strings be described by the same frequency state?"*

**Why it belongs here:** This is the canonical frequency-comparison problem. It establishes that two collections are "equivalent" not by order but by distribution.

---

### Q2. First Unique Character in a String
<a href="https://leetcode.com/problems/first-unique-character-in-a-string/" target="_blank">LeetCode 387</a> — **Easy**

**Target Skill:** Frequency + second pass — collect all counts first, then query them.

**Core Reasoning:**
- Pass 1: Build frequency map for the entire string.
- Pass 2: Scan the string again, return the first index whose character has frequency exactly `1`.
- **Critical discipline:** You cannot answer this in a single pass — you need complete frequency information before you can determine uniqueness.

**Why it belongs here:** Teaches the *two-phase discipline* that applies to almost all frequency problems. Information gathered in Phase 1 is used to make decisions in Phase 2.

---

### Q3. Majority Element
<a href="https://leetcode.com/problems/majority-element/" target="_blank">LeetCode 169</a> — **Easy**

**Target Skill:** Frequency tracking + threshold condition (frequency > N/2).

**Core Reasoning:**
- Build a frequency map. Return the element whose count exceeds `n / 2`.
- Alternatively, use Boyer-Moore Voting (frequency-based running cancellation) for $O(1)$ space.
- **The question:** *"Which element appears more than half the time?"*

**Why it belongs here:** The dominant algorithmic idea is frequency reasoning. Boyer-Moore Voting is itself a frequency-balance algorithm — it tracks a running "net frequency" score, not a mathematical formula.

**Cross-pattern note:** Boyer-Moore Voting is worth understanding as an $O(1)$ space variant of frequency tracking.

---

### Q4. Intersection of Two Arrays II
<a href="https://leetcode.com/problems/intersection-of-two-arrays-ii/" target="_blank">LeetCode 350</a> — **Easy**

**Target Skill:** Frequency-based intersection — elements can repeat and repetition counts matter.

**Core Reasoning:**
- Build a frequency map for `nums1`. For each element in `nums2`, include it in the result up to its count in the map.
- Differs from LeetCode 349 (set intersection): here, `[1, 1, 2]` ∩ `[1, 1, 3]` = `[1, 1]`, not `[1]`.

**Why it belongs here:** This is frequency-map intersection, not set intersection. You need the count, not just presence.

**Cross-pattern note:** LeetCode 349 uses a HashSet (membership) → belongs to **Pattern 01**. This uses a HashMap (frequency) → belongs here.

---

### Q5. Top K Frequent Elements
<a href="https://leetcode.com/problems/top-k-frequent-elements/" target="_blank">LeetCode 347</a> — **Medium**

**Target Skill:** Frequency map → selection using a secondary mechanism.

**Core Reasoning:**
- Hashing is not the final answer here. Build a frequency map, then use either:
  - **Bucket Sort** ($O(N)$): bucket index = frequency, collect from highest index down.
  - **Min-Heap** ($O(N \log K)$): maintain a heap of size $k$.
- **The insight:** Knowing frequency is only Phase 1. You then need a mechanism to *select* from that frequency information.

**Why it belongs here:** This is the canonical "frequency → selection" problem. The mental model extends to any problem that requires ranking by occurrence count.

---

### Q6. Group Anagrams
<a href="https://leetcode.com/problems/group-anagrams/" target="_blank">LeetCode 49</a> — **Medium**

**Target Skill:** Canonical key transformation + HashMap grouping.

**Core Reasoning:**
- Convert each string to a canonical form (sorted characters, or `int[26]` frequency array as key).
- Use the canonical form as a HashMap key. All equivalent strings map to the same bucket.
- **The central question:** *"How do I represent this input so that all equivalent inputs produce the same key?"*

**Why it belongs here:** This introduces the idea of **canonical representation as a HashMap key** — one of the most powerful and transferable hashing ideas. It appears in graph state memoization, anagram grouping, and equivalence class problems.

---

### Q7. Sort Characters By Frequency
<a href="https://leetcode.com/problems/sort-characters-by-frequency/" target="_blank">LeetCode 451</a> — **Medium**

**Target Skill:** Frequency map + frequency-ordered output construction.

**Core Reasoning:**
- Build a frequency map. Sort characters by frequency (descending). Reconstruct the string.
- Alternative: Use a max-heap to extract characters in frequency order.
- **The question:** *"Given a frequency map, how do I rebuild output ordered by frequency?"*

**Why it belongs here:** This is the output-construction companion to Top K Frequent Elements. Instead of selecting top-$K$, you reorder all elements by their frequency count.

---

## 🏆 Mastery Criteria

You have mastered Pattern 02 when you can, **without being told the pattern name**:

- [ ] Recognize frequency comparison (anagram) vs frequency + action (first unique, top K, sort by freq).
- [ ] Explain why two-phase separation (build then query) matters and when collapsing phases causes errors.
- [ ] Design a canonical key that maps equivalent inputs to the same HashMap bucket.
- [ ] Choose between `int[26]` frequency array vs `HashMap<Character, Integer>` based on input constraints.
- [ ] Extend Top K Frequent Elements to use either bucket sort ($O(N)$) or a min-heap ($O(N \log K)$).
- [ ] Explain why Majority Element is a frequency problem even when solved with Boyer-Moore Voting.
- [ ] Distinguish frequency-based intersection (LeetCode 350) from membership-based intersection (LeetCode 349).
- [ ] State time and space complexity for all seven problems.

---

## ➡️ Next Pattern

Once frequency, grouping, and canonical keys feel natural, move to **[Pattern 03: Complement & Relationship Lookup](./Pattern-03-Complement-and-Relationship-Lookup.md)**.

The next step is not just *"how often?"* but *"for each element, is there a previously-seen element that completes a required relationship?"*
