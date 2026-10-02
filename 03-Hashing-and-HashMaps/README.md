# 🔑 Module 03: Hashing & Hash Maps

> Trade $O(N)$ auxiliary space for instant $O(1)$ average lookup speed by storing previously computed states, counts, indices, or relationships while traversing data.

---

## 📌 How HashMap Works Under the Hood

Before solving hashing problems, understand the underlying mechanics.

**Hash Function:** Maps a key (String, Integer, Object) to an integer hash code.
`index = Math.abs(key.hashCode()) % array_length` → determines which bucket the key lands in.

**Collision Resolution (Java's approach):**
- Each bucket stores a LinkedList (or a Red-Black Tree when bucket size > 8 in Java 8+).
- When two distinct keys map to the same bucket, the chain is walked to find/insert the key.

**Load Factor & Rehashing:**
- Load Factor ($\alpha = N/K$, default `0.75`): when exceeded, capacity doubles and all entries are re-hashed.
- Amortized $O(1)$ per operation.

**Time Complexity Summary:**

| Operation | Average | Worst Case |
|-----------|---------|------------|
| `put(k, v)` | $O(1)$ | $O(N)$ (degenerate collisions) |
| `get(k)` / `contains(k)` | $O(1)$ | $O(N)$ |
| `remove(k)` | $O(1)$ | $O(N)$ |
| Space | $O(N)$ | $O(N)$ |

In practice, well-distributed hash functions keep all operations at $O(1)$ average.

---

## 🔍 HashSet vs HashMap vs Frequency Array

```text
Do you only need to know IF an element was seen?       ──► HashSet
Do you need to store a value associated with a key?    ──► HashMap
Is the key domain small and bounded (e.g. a-z, 0-9)?  ──► int[] Frequency Array
```

| Structure | Best For | Space |
|-----------|----------|-------|
| `HashSet<T>` | Membership, duplicate detection, visited states | $O(N)$ |
| `HashMap<K, V>` | Count, index, canonical key, prefix state mapping | $O(N)$ |
| `int[26]` or `int[128]` | ASCII character frequency, digit frequency | $O(\text{range})$ |

---

## 🛠️ First-Seen / Last-Seen / Index State — A Technique, Not a Pattern

Across multiple patterns in this module, the HashMap stores **positional information** rather than counts. This is a critical cross-cutting technique to recognize.

**What gets stored:**

| Technique | HashMap stores | Used for |
|-----------|----------------|----------|
| `value → first index` | Earliest occurrence | Longest subarray reasoning (maximize span) |
| `value → last index` | Most recent occurrence | Minimize removal, sliding window shrink |
| `value → frequency count` | Number of occurrences | Counting pairs, frequency queries |
| `value → historical state` | Prefix sum / computed state | Subarray condition lookups |

**Where this technique appears:**
- **Pattern 01:** `Contains Duplicate II` — HashSet as a window of last-seen positions.
- **Pattern 03:** `Two Sum` — stores `value → index` for pair index return.
- **Pattern 04:** `Contiguous Array`, `Longest Well-Performing Interval` — store `prefix → first-seen index` to maximize span.
- **Pattern 04:** `Make Sum Divisible by P` — store `remainder → most-recent index` to minimize removal.

> **Recognition rule:** When a problem asks for the *length* or *span* of a subarray, you often store an index, not a count. When it asks for the *number of* subarrays, you store a count.

---

## 🗺️ The 5-Pattern Architecture (29 Curated Questions)

| Pattern | Questions | Core Idea |
|---------|-----------|-----------|
| **[Pattern 01: Membership & Duplicate Detection](./Pattern-01-Membership-and-Duplicate-Detection.md)** | 5 | "Have I seen this before?" — HashSet visited-state tracking |
| **[Pattern 02: Frequency, Grouping & Canonical Keys](./Pattern-02-Frequency-Grouping-and-Canonical-Keys.md)** | 7 | "How many times?" — Frequency map build then query |
| **[Pattern 03: Complement & Relationship Lookup](./Pattern-03-Complement-and-Relationship-Lookup.md)** | 6 | "What previously-seen element completes this pair?" — Complement in $O(1)$ |
| **[Pattern 04: Prefix Sum + HashMap](./Pattern-04-Prefix-Sum-and-HashMap.md)** | 7 | "What previous prefix state enables this subarray condition?" — Prefix complement |
| **[Pattern 05: Composite Hashing](./Pattern-05-Composite-Hashing.md)** | 4 | HashMap + another structure, or multiple structures coordinated |
| **Total** | **29** | |

---

## ⚠️ Dominant-Pattern Classification

Not every problem that uses a HashMap belongs in this module. The rule:

> **A problem belongs here only if the primary algorithmic insight involves a HashMap or HashSet. If the HashMap is a supporting detail of another dominant pattern, it belongs to that pattern's module.**

| Problem | Dominant Pattern | Module |
|---------|-----------------|--------|
| **Find All Anagrams in a String** | String Sliding Window | Strings Pattern 05 |
| **Longest Substring Without Repeating Characters** | String Sliding Window | Strings Pattern 05 |
| **Isomorphic Strings** | Bidirectional character mapping | Strings Pattern 02 |
| **LRU Cache** | Doubly Linked List + HashMap | Linked List / Design |
| **Time Based Key-Value Store** | Binary Search | Binary Search / Design |
| **4Sum** | Sorting + Two Pointers | Arrays |
| **Two Sum II** | Two Pointers | Arrays |
| **Maximum Subarray** | Kadane's Algorithm | Arrays Pattern 07 |

---

## 🔗 Cross-Pattern Relationships

```text
Pattern 01 (Membership)
  └─ Shares "have I seen this?" logic with:
       Pattern 03 (was this complement seen?)
       Pattern 04 (was this prefix state seen?)

Pattern 02 (Frequency)
  └─ Frequency map is Phase 1 for:
       Pattern 05 Q4 (reference word frequency for Concatenation problem)

Pattern 03 (Complement Lookup)
  └─ Complement lookup on prefix sums = Pattern 04
  └─ Complement over modular remainders appears in both Pattern 03 and Pattern 04

Pattern 04 (Prefix + HashMap)
  └─ Builds on Arrays Prefix Sum (Module 01 Pattern 04)
  └─ Introduces index-storage distinction (First-Seen / Last-Seen technique)

Pattern 05 (Composite)
  └─ Uses Frequency HashMap idea from Pattern 02 (Concatenation problem)
  └─ Uses node-registry HashMap idea unique to object-graph problems
```

---

## 🎯 Learning Progression

```text
Can I detect if a value was seen before?
  ──► Pattern 01: Membership & Duplicate Detection
        ↓
How many times was each value seen, and can I group by equivalence?
  ──► Pattern 02: Frequency, Grouping & Canonical Keys
        ↓
For this element, is there a previously-seen element completing a pair relationship?
  ──► Pattern 03: Complement & Relationship Lookup
        ↓
For this prefix state, has a prior prefix state existed satisfying a subarray condition?
  ──► Pattern 04: Prefix Sum + HashMap
        ↓
Is one HashMap insufficient? What other structure must work alongside it?
  ──► Pattern 05: Composite Hashing
```

---

## 📊 Difficulty Distribution (All 29 Questions)

| Difficulty | Count |
|------------|-------|
| **Easy** | 9 |
| **Medium** | 16 |
| **Hard** | 4 |
| **Total** | **29** |

---

## 🏆 Module Mastery Criteria

By completing this module, you should be able to, **without being shown the pattern name**:

- [ ] Recognize when a problem requires a HashSet vs HashMap vs frequency array.
- [ ] Identify "have I seen this?" questions and solve them in $O(N)$.
- [ ] Build a frequency map and use it to compare, group, select, or order.
- [ ] Transform a pair relationship condition into a complement that can be looked up in $O(1)$.
- [ ] Apply the prefix-sum + HashMap equation: `prefix[i] - prefix[j] = K → look up prefix[j] = prefix[i] - K`.
- [ ] Distinguish between storing counts vs. indices in the HashMap and know when each applies.
- [ ] Recognize when to use first-seen vs. last-seen index storage.
- [ ] Design composite data structures where HashMap and ArrayList maintain mutual consistency.
- [ ] Identify when a problem that uses a HashMap actually belongs to a different dominant pattern.
- [ ] State time $O(N)$ and space $O(N)$ complexity and handle edge cases for all 29 problems.
