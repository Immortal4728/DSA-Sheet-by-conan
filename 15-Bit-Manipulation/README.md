# Section 15 — Bit Manipulation

> **Core Philosophy**: Bit Manipulation is a collection of reusable representations and invariants, not a collection of clever tricks.

---

## 1. Section Purpose & Mental Model

Bit Manipulation operates directly on the binary representation of data at the hardware level. Rather than treating integers as abstract decimal values, bit manipulation uses integer bit patterns to represent **subsets**, **boolean state masks**, **parity counts**, and **independent bit columns**.

This section builds a structured, fresher/SDE-1-focused foundation to help candidates recognize binary invariants and solve bitwise problems confidently from a blank editor.

---

## 2. Recognition Strategy: Thinking in Binary

Train yourself to ask these diagnostic questions when analyzing a problem:

```text
1. Binary Representation ──► Can the problem state be represented in binary (0s and 1s)?
2. Cancellation         ──► Does XOR self-cancellation (x ^ x = 0) eliminate duplicate pairs?
3. Fixed Frequency      ──► Does every element appear K times except one? (Use bitwise mod-K counting)
4. Independent Bits     ──► Can each bit position be optimized independently with zero carry-over?
5. Subset Masking       ──► Can a 26-bit or 32-bit integer replace set operations (AND & / OR |)?
6. Common Prefix        ──► Does range Bitwise AND reduce to isolating the common binary prefix?
```

---

## 3. Final Curriculum Architecture

This module contains **12 canonical core questions + 1 selective/advanced question**, organized across 6 structured pattern files:

```text
15-Bit-Manipulation/
├── Pattern-01-Fundamentals-and-Representation.md
├── Pattern-02-XOR-Patterns.md
├── Pattern-03-Bit-Counting-and-Properties.md
├── Pattern-04-Bitmasking-and-Subset-Representation.md
├── Pattern-05-Bitwise-Construction-and-Optimization.md
├── Pattern-06-Hidden-Bit-Manipulation-Recognition.md
└── README.md
```

### Question Distribution Summary

| Pattern File | Canonical Core Questions | Selective / Advanced | Core Focus & Bitwise Invariants |
| :--- | :--- | :--- | :--- |
| **01 — Fundamentals & Representation** | 3 (<a href="https://leetcode.com/problems/number-of-1-bits/" target="_blank">LeetCode 191</a>, <a href="https://leetcode.com/problems/reverse-bits/" target="_blank">LeetCode 190</a>, <a href="https://leetcode.com/problems/power-of-two/" target="_blank">LeetCode 231</a>) | — | Basic bitwise operators, `n & (n - 1)` clearing invariant, bit reversal & single set bit check |
| **02 — XOR Patterns** | 4 (<a href="https://leetcode.com/problems/single-number/" target="_blank">LeetCode 136</a>, <a href="https://leetcode.com/problems/missing-number/" target="_blank">LeetCode 268</a>, <a href="https://leetcode.com/problems/single-number-ii/" target="_blank">LeetCode 137</a>, <a href="https://leetcode.com/problems/single-number-iii/" target="_blank">LeetCode 260</a>) | — | XOR cancellation $x \oplus x = 0$, index cancellation, mod-3 bit counting & `diff & (-diff)` partition |
| **03 — Bit Counting & Properties** | 2 (<a href="https://leetcode.com/problems/counting-bits/" target="_blank">LeetCode 338</a>, <a href="https://leetcode.com/problems/hamming-distance/" target="_blank">LeetCode 461</a>) | — | Hamming weight of $x \oplus y$ & dynamic bit count reuse `ans[i] = ans[i >> 1] + (i & 1)` |
| **04 — Bitmasking & Subset Representation** | 1 (<a href="https://leetcode.com/problems/maximum-product-of-word-lengths/" target="_blank">LeetCode 318</a>) | — | 26-bit integer character set masks & $O(1)$ disjoint check `(maskA & maskB) == 0` |
| **05 — Bitwise Construction & Optimization** | 1 (<a href="https://leetcode.com/problems/minimum-flips-to-make-a-or-b-equal-to-c/" target="_blank">LeetCode 1318</a>) | — | Independent bit column inspection & flip minimization |
| **06 — Hidden Bit Manipulation Recognition** | 1 (<a href="https://leetcode.com/problems/bitwise-and-of-numbers-range/" target="_blank">LeetCode 201</a>) | 1 (<a href="https://leetcode.com/problems/minimum-one-bit-operations-to-make-integers-zero/" target="_blank">LeetCode 1611</a>) | Common binary prefix isolation `left >>= 1, right >>= 1` & Gray-code decoding (Selective) |
| **TOTAL** | **12 Core Questions** | **1 Selective Question** | — |

---

## 4. Cross-Section Ownership & Boundaries

The repository maintains strict single canonical ownership for every problem:

- **Section 09 — Recursion & Backtracking**: Canonically owns <a href="https://leetcode.com/problems/subsets/" target="_blank">LeetCode 78 (Subsets)</a> and <a href="https://leetcode.com/problems/letter-case-permutation/" target="_blank">LeetCode 784 (Letter Case Permutation)</a>. Pattern 04 cross-references them as bitmask subset state representations without duplicating them.
- **Section 14 — Tries**: Canonically owns <a href="https://leetcode.com/problems/maximum-xor-of-two-numbers-in-an-array/" target="_blank">LeetCode 421 (Maximum XOR)</a> via Binary Trie. Section 15 cross-references LC 421 as the bridge between Bitwise Representation, XOR, and Binary Tries.
- **Selective / Advanced Exposure**: <a href="https://leetcode.com/problems/minimum-one-bit-operations-to-make-integers-zero/" target="_blank">LeetCode 1611</a> is marked Selective/Advanced to expose specialized Gray-code relationships without expanding the 12 core fresher questions.

---

## 5. Fresher / SDE-1 Scope Constraints

This section is strictly scoped for **SDE-1 technical interview success**. It intentionally avoids competitive-programming hacks:
- ❌ **Excluded**: Random bit hacks, XOR swap as a canonical problem, obscure bit tricks, advanced Gray-code core patterns, advanced bitmask DP, heavy contest bit optimization.

---

## Mastery Criteria

Section 15 is mastered when you can:

1. **Explain binary representation** and signed 2's complement representation.
2. **Use AND, OR, XOR, NOT, and bit shifts** confidently.
3. **Check, set, clear, and toggle individual bits** using masks.
4. **Recognize powers of two** in $O(1)$ time using `n & (n - 1) == 0`.
5. **Count set bits efficiently** using Kernighan's `n & (n - 1)` invariant.
6. **Calculate Hamming distance** using the set bits of $x \oplus y$.
7. **Use XOR cancellation** ($x \oplus x = 0$) to solve element frequency problems.
8. **Solve missing and unique element problems** in $O(N)$ time and $O(1)$ space.
9. **Use a bitmask to represent a small set** of up to 32 elements.
10. **Use masks to detect set overlap** in $O(1)$ CPU time `(maskA & maskB) == 0`.
11. **Reason independently about each bit column** without carry-over assumptions.
12. **Recognize hidden bitwise structures** like common binary prefixes in ranges.
13. **Explain why the bitwise invariant works** mathematically.
14. **Solve core bitwise problems from a blank editor** in Java.
15. **State time and space complexity** accurately for bitwise operations.
16. **Distinguish ordinary arithmetic reasoning** from low-level bitwise reasoning.
