# 🔤 Module 02: Strings

> Master character manipulation, memory mechanics, substring mechanics, sliding windows, parsing, and advanced pattern matching for SDE interviews.

---

## 📌 Java String Fundamentals & Internals

Before diving into algorithmic patterns, you must understand how strings behave in memory, how immutability impacts performance, and when to use specialized string builders.

### 1. Immutability & Memory Layout (String Constant Pool)
- In Java, `String` objects are **immutable**. Once created, their contents cannot be altered.
- String literals are stored in the **String Constant Pool (SCP)** inside the Heap to optimize memory usage.
- Modifying a `String` inside a loop (e.g., `s += ch`) creates a **new `String` instance** on every iteration, leading to an **$O(N^2)$ time complexity** and massive garbage collection overhead.

```java
String s1 = "hello"; // Stored in String Constant Pool
String s2 = "hello"; // Points to same instance in SCP (s1 == s2 is true)
String s3 = new String("hello"); // Explicit heap allocation (s1 == s3 is false, s1.equals(s3) is true)
```

### 2. Standard Comparison Operations
- **`==` Operator:** Compares memory reference addresses, **not** character contents.
- **`.equals()` Method:** Compares character-by-character sequence equality in $O(N)$ time.
- **`.compareTo()` Method:** Performs lexicographical comparison returning `< 0`, `0`, or `> 0`.

### 3. Efficiency Tools: `StringBuilder` vs `StringBuffer` vs `char[]`
- **`char[]` Array:** Best for in-place modifications, indexing, and optimal cache locality when string length is fixed.
- **`StringBuilder`:** Mutable sequence of characters. Unsynchronized (not thread-safe), fast, and ideal for single-threaded string construction ($O(1)$ amortized append).
- **`StringBuffer`:** Thread-safe, synchronized alternative to `StringBuilder`. Slightly slower due to synchronization locks.

---

## 🗺️ The 12 String Patterns Architecture

Unlike generic array problems, string problems introduce unique challenges around character encodings (ASCII/Unicode), frequency tracking, substring boundaries, sliding windows, stack-based parsing, and pattern-matching algorithms.

| # | Pattern Name | Depth | Core Objective & Focus |
|---|---|---|---|
| **01** | **String Traversal & Character Processing** | 🟢 Low | Character iteration, ASCII manipulation, case conversions, and single-pass checks. |
| **02** | **Character Frequency & Hashing** | 🔴 Deep | ASCII frequency arrays (`int[26]`, `int[128]`), anagram counting, character grouping, and map lookups. |
| **03** | **Palindrome Patterns** | 🟠 Medium | Expanding around center, reverse comparisons, half-string checks, and palindromic constraints. |
| **04** | **Two Pointers on Strings** | 🔴 Deep | Opposite-direction pointers, fast-slow filtering, space-optimized sequence matching, and character swapping. |
| **05** | **String Sliding Window** | 🔴 Very Deep | Fixed & dynamic window boundaries, distinct character counts, exact frequency matching, and substring optimization. |
| **06** | **Anagram & Rearrangement Patterns** | 🟠 Medium | Character frequency state matching, canonical sorting keys, sliding window frequency balance, and permutations. |
| **07** | **String Construction & Transformation** | 🟠 Medium | Efficient string building with `StringBuilder`, string compression, encoding/decoding, and in-place transformations. |
| **08** | **Substring & Subsequence Reasoning** | 🔴 Deep | Distinguishing contiguous substrings from non-contiguous subsequences; bridging toward Dynamic Programming. |
| **09** | **Stack-Based String Problems** | 🟠 Medium | Parentheses matching, string decoding/expansion, duplicate removals, and nested expression resolution. |
| **10** | **String Parsing & Simulation** | 🟠 Medium | Processing formatted inputs, tokenization, number conversions (`atoi`), version comparison, and state machines. |
| **11** | **String Pattern Matching** | 🔴 Deep | Substring search algorithms, KMP prefix function ($\pi$ array), Z-algorithm, and rolling hash (Rabin-Karp). |
| **12** | **Advanced String Structures** | 🟡 Later | Trie prefix trees, Suffix Automata, and advanced dictionary lookups *(Covered in Module 15: Tries)*. |

---

## 🚀 Learning Progression

```text
Fundamentals & Traversal (Pattern 01)
               ↓
Frequency Counting & Palindromes (Pattern 02 - 03)
               ↓
Two Pointers & Sliding Windows (Pattern 04 - 05)
               ↓
Anagrams & Transformations (Pattern 06 - 07)
               ↓
Substrings, Stacks & Parsing (Pattern 08 - 10)
               ↓
Advanced Pattern Matching (Pattern 11 - 12)
```

---

## 🎯 Mastery Checklist

By the end of this module, you should be able to:
- Explain Java string memory allocation (SCP vs Heap) and immutability pitfalls.
- Convert character problems into $O(1)$ space using fixed ASCII frequency tables (`int[26]` or `int[128]`).
- Apply Two Pointers and Sliding Window techniques efficiently to string subsegments.
- Distinguish between contiguous **substrings** ($O(N^2)$ total) and non-contiguous **subsequences** ($2^N$ total).
- Evaluate nested string structures using Stacks without recursive function call overhead.
- Implement linear-time substring search algorithms (KMP / Z-Algorithm) when naive matching takes $O(N \cdot M)$.
