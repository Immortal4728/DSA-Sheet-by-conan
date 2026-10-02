# 📦 Arrays — Patterns & Fundamentals

> The foundation of data structures. Master arrays and half of DSA intuition clicks automatically.

---

## 📖 Essential Vocabulary (Read First!)

Before diving into problems, make sure you know the exact difference between these terms. Interviewers use them strictly!

| Term | Rule | Example from `[10, 20, 30, 40]` | Valid or Invalid? |
|------|------|-----------------------------------|-------------------|
| **Subarray** | **Contiguous** block (no skipping, order preserved) | `[20, 30]` | ✅ Valid |
| | | `[10, 30]` | ❌ Invalid *(skipped 20)* |
| **Subsequence** | Can skip elements, but **order MUST be preserved** | `[10, 30, 40]` | ✅ Valid |
| | | `[30, 10]` | ❌ Invalid *(order changed)* |
| **Subset** | Any selection of elements, **order DOES NOT matter** | `[40, 10]` | ✅ Valid |
| **Prefix** | Subarray that **starts at index 0** | `[10, 20, 30]` | ✅ Valid |
| **Suffix** | Subarray that **ends at the last index ($N-1$)** | `[30, 40]` | ✅ Valid |

---

## ☕ Java Syntax Quick Reference

### 1. Frequency Tracking
```java
Map<Integer, Integer> map = new HashMap<>();
map.put(num, map.getOrDefault(num, 0) + 1);
```

### 2. Guarding Range Sum Boundaries ($L=0$)
When computing `prefix[R] - prefix[L - 1]`, if $L=0$, `L - 1` goes negative (Index Out of Bounds!). Always guard it:
```java
int sum = (L == 0) ? prefix[R] : prefix[R] - prefix[L - 1];
```

---

## 🎯 Pattern Roadmap

| Pattern | Focus | Main Question |
|---------|-------|---------------|
| [Pattern 01: Basic Traversal & Simulation](./Array-Patterns/Pattern-01-Basic-Traversal-and-Simulation.md) | Single pass, visiting every element | *"Can I solve this by processing elements one by one?"* |
| [Pattern 02: Min/Max Tracking](./Array-Patterns/Pattern-02-Min-Max-Tracking.md) | Running extremes & state accumulation | *"Can I keep the best/worst value seen so far?"* |
| [Pattern 03: Frequency Counting](./Array-Patterns/Pattern-03-Frequency-Counting.md) | Trading memory for fast count lookups | *"Do I need to remember how often each value appears?"* |
| [Pattern 04: Prefix Sum](./Array-Patterns/Pattern-04-Prefix-Sum.md) | Precomputing cumulative totals for $O(1)$ range queries | *"Am I recalculating overlapping range sums?"* |
| [Pattern 05: Two Pointers](./Array-Patterns/Pattern-05-Two-Pointers.md) | Moving two index positions intelligently | *"Can two positions move toward the answer safely?"* |
| [Pattern 06: Sliding Window](./Array-Patterns/Pattern-06-Sliding-Window.md) | Dynamic contiguous subarray/substring tracking | *"Does the next range overlap heavily with the current range?"* |
| [Pattern 07: Kadane's Algorithm](./Array-Patterns/Pattern-07-Kadanes-Algorithm.md) | Dynamic extend vs. restart decision at each index | *"At this index, should I extend the previous subarray or start fresh?"* |
| [Pattern 08: Matrix / 2D Array Traversal](./Array-Patterns/Pattern-08-Matrix-2D-Array-Traversal.md) | 2D Grid navigation, boundaries, and rotations | *"How do I systematically navigate rows, cols, or boundaries?"* |
| [Pattern 09: Prefix & Suffix Accumulation](./Array-Patterns/Pattern-09-Prefix-and-Suffix-Accumulation.md) | Precomputing info from left and right of each element | *"Can I precompute what is to the left and right of each element?"* |

---

## 🔎 Searching Sub-Module

| Pattern | Focus | Main Question |
|---------|-------|---------------|
| [Searching Pattern 01: Linear Search](./Searching/Pattern-01-Linear-Search.md) | Single pass scan on unsorted data | *"Is data unsorted, requiring me to check elements one by one?"* |
