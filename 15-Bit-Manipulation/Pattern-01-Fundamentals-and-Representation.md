# Pattern 01 — Bitwise Fundamentals & Representation

Bit manipulation operates directly on the binary representation of integers. Understanding fundamental bitwise operators (`AND &`, `OR |`, `XOR ^`, `NOT ~`, `Shift << >>`) and bitwise trick invariants (such as `n & (n - 1)`) makes low-level binary state tracking automatic.

---

## Learning Progression

```text
Basic Bit Operators ──► Bit Clearing Invariant n & (n - 1) ──► Bit Reversal & Traversal ──► Power-of-Two Validation
```

---

## Foundational Bitwise Identities & Invariants

| Operation | Expression | Effect / Invariant |
| :--- | :--- | :--- |
| **Check i-th Bit** | `(n & (1 << i)) != 0` | Returns `true` if bit $i$ is set (`1`) |
| **Set i-th Bit** | `n | (1 << i)` | Forces bit $i$ to be `1` |
| **Clear i-th Bit** | `n & ~(1 << i)` | Forces bit $i$ to be `0` |
| **Toggle i-th Bit** | `n ^ (1 << i)` | Flips bit $i$ (`0 -> 1` or `1 -> 0`) |
| **Clear Lowest Set Bit** | `n & (n - 1)` | Drops the lowest set bit (`1 -> 0`) |
| **Isolate Lowest Set Bit** | `n & (-n)` | Keeps only the lowest set bit |

---

## Canonical Question Set (3 Core Questions)

### 1. Number of 1 Bits (Understand & Apply Invariant)
- **Problem:** <a href="https://leetcode.com/problems/number-of-1-bits/" target="_blank">LeetCode 191</a>
- **Difficulty:** Easy
- **Core Bitwise Idea:** Fast set-bit counting using the `n & (n - 1)` clearing invariant (Brian Kernighan's Algorithm).
- **Decision / Operation:** Repeatedly execute `n = n & (n - 1)` and increment `count++` until `n == 0`.
- **Invariant / Proof Intuition:** Subtracting 1 flips the lowest set bit `1` and all trailing zeros to `1`s. ANDing `n` with `n - 1` clears exactly 1 set bit per iteration, allowing time complexity to depend only on the number of set bits $K$ rather than all 32 bit positions.
- **Recognition Clue:** Counting total `1` bits in binary integer representation.
- **Common Wrong Approach:** Shifting bit-by-bit 32 times with `n >>>= 1` regardless of how many set bits exist.
- **Complexity:** Time: $O(K)$ where $K$ is number of set bits (at most 32). Space: $O(1)$.
- **Cross-Pattern Connection:** Baseline set-bit clearing technique for Pattern 03 (Bit Counting).

---

### 2. Reverse Bits (Apply & Bit Traversal)
- **Problem:** <a href="https://leetcode.com/problems/reverse-bits/" target="_blank">LeetCode 190</a>
- **Difficulty:** Easy
- **Core Bitwise Idea:** Bitwise traversal and reverse-shifting across 32 fixed positions.
- **Decision / Operation:** Iterate $i$ from 0 to 31. Extract the LSB `(n & 1)` from `n`, append it to `result` via `result = (result << 1) | (n & 1)`, and unsigned right shift `n >>>= 1`.
- **Invariant / Proof Intuition:** Shifting `result` left while shifting `n` right transfers the least significant bit of `n` into the most significant position of `result` progressively across 32 steps.
- **Recognition Clue:** Reversing binary representations of fixed-width 32-bit unsigned integers.
- **Common Wrong Approach:** Converting integer to String, reversing String, and parsing back ($O(N)$ string overhead vs $O(1)$ native bitwise shifts).
- **Complexity:** Time: $O(32) = O(1)$, Space: $O(1)$.
- **Cross-Pattern Connection:** Positional bit traversal foundation.

---

### 3. Power of Two (Recognize & Invariant)
- **Problem:** <a href="https://leetcode.com/problems/power-of-two/" target="_blank">LeetCode 231</a>
- **Difficulty:** Easy
- **Core Bitwise Idea:** Single set bit verification via `n & (n - 1) == 0`.
- **Decision / Operation:** Return `n > 0 && (n & (n - 1)) == 0`.
- **Invariant / Proof Intuition:** An integer is a power of 2 if and only if its binary representation contains **exactly one `1` bit**. Clearing its lowest set bit via `n & (n - 1)` must result in 0.
- **Recognition Clue:** Checking whether an integer is $2^k$ without loops or floating-point logarithm comparisons.
- **Common Wrong Approach:** Loop division `while (n % 2 == 0) n /= 2`, or using floating-point `Math.log2()` susceptible to precision rounding errors.
- **Complexity:** Time: $O(1)$, Space: $O(1)$.
- **Cross-Pattern Connection:** Instant constant-time binary state validation.
