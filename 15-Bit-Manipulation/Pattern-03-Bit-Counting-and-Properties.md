# Pattern 03 — Bit Counting & Bit Properties

Bit Counting treats the number of set bits (Hamming Weight) and difference in set bit positions (Hamming Distance) as reusable properties. Instead of recomputing bit counts from scratch, dynamic programming relationship properties like `ans[i] = ans[i >> 1] + (i & 1)` allow $O(N)$ linear generation of bit properties.

---

## Learning Progression

```text
Hamming Distance (LC 461) ──► Dynamic Bit Counting (LC 338)
```

1. **Hamming Distance (<a href="https://leetcode.com/problems/hamming-distance/" target="_blank">LeetCode 461</a>)**: Compute bit differences between two numbers using $X \oplus Y$.
2. **Dynamic Bit Counting (<a href="https://leetcode.com/problems/counting-bits/" target="_blank">LeetCode 338</a>)**: Reuse bit counts of smaller numbers `ans[i >> 1]` to generate continuous bit counts in $O(N)$ time.

---

## Canonical Question Set (2 Core Questions)

### 1. Hamming Distance (Understand & Apply)
- **Problem:** <a href="https://leetcode.com/problems/hamming-distance/" target="_blank">LeetCode 461</a>
- **Difficulty:** Easy
- **Core Bitwise Idea:** Count set bits in the XOR difference $X \oplus Y$.
- **Decision / Operation:** Calculate `xor = x ^ y`. Count the number of `1` bits in `xor` using `xor & (xor - 1)` (or `Integer.bitCount(xor)`).
- **Invariant / Proof Intuition:** Bitwise XOR returns `1` at position $i$ if and only if bit $i$ differs between $x$ and $y$. The Hamming distance equals the Hamming weight of $x \oplus y$.
- **Recognition Clue:** Calculating total number of positions at which two integers have different bits.
- **Common Wrong Approach:** Comparing bits using string formatting or integer array conversions.
- **Complexity:** Time: $O(1)$ (at most 32 bits), Space: $O(1)$.
- **Cross-Pattern Connection:** Connects Pattern 01 (Set-Bit Clearing) with Pattern 02 (XOR Properties).

---

### 2. Counting Bits (Recognize & DP Reuse)
- **Problem:** <a href="https://leetcode.com/problems/counting-bits/" target="_blank">LeetCode 338</a>
- **Difficulty:** Easy
- **Core Bitwise Idea:** $O(N)$ Linear DP generation of set bit counts using bit shift properties.
- **Decision / Operation:** Create array `ans` of size `n + 1`. For $i$ from 1 to $n$: `ans[i] = ans[i >> 1] + (i & 1)`.
- **Invariant / Proof Intuition:** Shifting number $i$ right by 1 (`i >> 1`) removes its LSB. The number of set bits in $i$ is equal to the set bits in `i >> 1` plus 1 if $i$ is odd (`i & 1`). Since `i >> 1 < i`, `ans[i >> 1]` is already computed!
- **Recognition Clue:** Generating set bit counts for all numbers in range $[0, n]$ in $O(N)$ linear time without calling `bitCount` per number.
- **Common Wrong Approach:** Running $O(32)$ set-bit count per number, resulting in $O(N \log N)$ total time instead of optimal $O(N)$ DP.
- **Complexity:** Time: $O(N)$, Space: $O(N)$ for output array.
- **Cross-Pattern Connection:** Bit Manipulation + 1D DP state reuse (Section 10).
