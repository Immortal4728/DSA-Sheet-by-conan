# Pattern 04 — Bitmasking & Subset Representation

A **Bitmask** uses individual bits of an integer (32-bit or 64-bit) to represent a subset or state over a domain of size $K \le 32$. Bit $i = 1$ indicates that element $i$ is present in the set; bit $i = 0$ indicates absence.

Bitmasking converts set operations into fast $O(1)$ hardware instructions:
- **Set Union**: `maskA | maskB`
- **Set Intersection**: `maskA & maskB`
- **Set Disjoint Check**: `(maskA & maskB) == 0`
- **Element Membership**: `(mask & (1 << i)) != 0`

> [!NOTE]
> **Canonical Ownership Rule:** Section 09 (Recursion & Backtracking) canonically owns <a href="https://leetcode.com/problems/subsets/" target="_blank">LeetCode 78 (Subsets)</a> and <a href="https://leetcode.com/problems/letter-case-permutation/" target="_blank">LeetCode 784 (Letter Case Permutation)</a> via decision-tree backtracking. They are cross-referenced here as state representation techniques.

---

## Canonical Question Set (1 Core Question)

### 1. Maximum Product of Word Lengths (Understand & Fast Disjoint Mask Check)
- **Problem:** <a href="https://leetcode.com/problems/maximum-product-of-word-lengths/" target="_blank">LeetCode 318</a>
- **Difficulty:** Medium
- **Core Bitwise Idea:** Represent character set of each word as a 26-bit integer bitmask for $O(1)$ disjoint character validation.
- **Decision / Operation:** Convert each word `words[i]` into a 26-bit integer mask `masks[i] |= (1 << (ch - 'a'))`. For every pair $(i, j)$, if `(masks[i] & masks[j]) == 0`, words $i$ and $j$ share no common letters $\Rightarrow$ update `maxProd = max(maxProd, len[i] * len[j])`.
- **Invariant / Proof Intuition:** Bitwise AND `(maskA & maskB) == 0` proves in a single $O(1)$ CPU instruction that two words share zero common letters, replacing $O(L_1 \times L_2)$ character set comparisons.
- **Recognition Clue:** Pairwise string comparisons checking for character overlap across a dictionary.
- **Common Wrong Approach:** Building `HashSet<Character>` for each word and running `Collections.disjoint()`, incurring high object allocation and lookup overhead.
- **Complexity:** Time: Bitmask precomputation $O(N \times L) +$ Pairwise checks $O(N^2)$, Space: $O(N)$ for mask storage.
- **Cross-Pattern Connection:** Replaces HashMap character sets with 26-bit integer bitmasks.
