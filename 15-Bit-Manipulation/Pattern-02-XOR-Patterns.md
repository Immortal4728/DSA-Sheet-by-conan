# Pattern 02 — XOR Patterns

The **Exclusive OR (XOR)** operator `^` has unique algebraic properties that make it exceptionally powerful for parity, cancellation, and missing element problems:
1. **Self-Cancellation**: $x \oplus x = 0$
2. **Identity**: $x \oplus 0 = x$
3. **Commutativity & Associativity**: $A \oplus B \oplus A = (A \oplus A) \oplus B = 0 \oplus B = B$

---

## Learning Progression

```text
XOR Self-Cancellation (LC 136) ──► Index-Value Cancellation (LC 268) ──► Bitwise Modulo Frequency State (LC 137) ──► Distinguishing Bit Partitioning (LC 260)
```

---

## Canonical Question Set (4 Core Questions)

### 1. Single Number (Understand & Self-Cancellation)
- **Problem:** <a href="https://leetcode.com/problems/single-number/" target="_blank">LeetCode 136</a>
- **Difficulty:** Easy
- **Core Bitwise Idea:** Cumulative XOR cancellation of paired numbers.
- **Decision / Operation:** Initialize `acc = 0`. XOR all elements in `nums`: `acc ^= num`. Return `acc`.
- **Invariant / Proof Intuition:** Every number appearing twice satisfies $x \oplus x = 0$. By commutativity, all duplicate pairs cancel out to 0, leaving only the unique single number ($0 \oplus S = S$).
- **Recognition Clue:** Array where every element appears twice except one element which appears once, requiring $O(N)$ time and $O(1)$ space.
- **Common Wrong Approach:** Using a `HashSet` or frequency `HashMap` taking $O(N)$ extra memory.
- **Complexity:** Time: $O(N)$, Space: $O(1)$.
- **Cross-Pattern Connection:** Baseline XOR self-cancellation property.

---

### 2. Missing Number (Apply & Index Cancellation)
- **Problem:** <a href="https://leetcode.com/problems/missing-number/" target="_blank">LeetCode 268</a>
- **Difficulty:** Easy
- **Core Bitwise Idea:** Dual XOR cancellation across indices `0...n` and array values.
- **Decision / Operation:** Initialize `xorSum = n`. For each index $i$ from 0 to $n-1$, compute `xorSum ^= i ^ nums[i]`. Return `xorSum`.
- **Invariant / Proof Intuition:** Combine array values with complete index list $[0, 1, \dots, n]$. All present values cancel out with their matching indices, leaving only the missing number.
- **Recognition Clue:** Array of length $n$ containing distinct numbers in range $[0, n]$ with 1 missing element.
- **Common Wrong Approach:** Sorting ($O(N \log N)$) or arithmetic sum $n(n+1)/2$ which can cause 32-bit integer overflow if $n$ is huge.
- **Complexity:** Time: $O(N)$, Space: $O(1)$.
- **Cross-Pattern Connection:** Index-value dual cancellation.

---

### 3. Single Number II (Recognize & Bitwise Modulo 3 State)
- **Problem:** <a href="https://leetcode.com/problems/single-number-ii/" target="_blank">LeetCode 137</a>
- **Difficulty:** Medium
- **Core Bitwise Idea:** Bitwise mod-3 counting across each 32-bit position.
- **Decision / Operation:** For each bit position $i$ from 0 to 31, sum bit $i$ across all numbers in `nums`. If `sum % 3 != 0`, set bit $i$ in `result` (`result |= (1 << i)`).
- **Invariant / Proof Intuition:** Numbers appearing 3 times contribute $3 \times 1 = 3$ (or 0) to each bit position sum. Taking `sum % 3` cancels out all triple occurrences, leaving only the bit contributions of the single number.
- **Recognition Clue:** Array where every element appears 3 times except one element appearing once ($O(1)$ space required).
- **Common Wrong Approach:** Attempting standard XOR `x ^ x ^ x = x`, which fails because XOR operates mod-2, not mod-3.
- **Complexity:** Time: $O(32 \times N) = O(N)$, Space: $O(1)$.
- **Cross-Pattern Connection:** Bitwise modulo frequency state tracking.

---

### 4. Single Number III (Transfer & Distinguishing Bit Partition)
- **Problem:** <a href="https://leetcode.com/problems/single-number-iii/" target="_blank">LeetCode 260</a>
- **Difficulty:** Medium
- **Core Bitwise Idea:** Distinguishing bit partitioning using `diff & (-diff)`.
- **Decision / Operation:**
  1. Compute `xorSum` of all elements. `xorSum` equals $A \oplus B$ (the two unique numbers).
  2. Find lowest set bit in `xorSum`: `diff = xorSum & (-xorSum)`.
  3. Divide numbers into two groups based on whether bit `diff` is set (`num & diff != 0`).
  4. XOR each group independently to isolate $A$ and $B$.
- **Invariant / Proof Intuition:** Since $A \neq B$, $A \oplus B$ has at least one bit set to `1`. This bit `diff` differs between $A$ and $B$. Partitioning the array by `diff` separates $A$ and $B$ into different groups while sending paired duplicates into the same group, reducing the problem to two instances of Single Number I.
- **Recognition Clue:** Array where every element appears twice except **two** elements appearing once ($O(1)$ space required).
- **Common Wrong Approach:** Attempting multi-pass extractions without isolating a distinguishing bit.
- **Complexity:** Time: $O(N)$, Space: $O(1)$.
- **Cross-Pattern Connection:** Partitioning via lowest set bit `n & (-n)`.
