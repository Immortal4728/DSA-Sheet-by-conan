# Pattern 05 — Bitwise Construction & Optimization

Bitwise Construction & Optimization evaluates each bit position **independently**. Because bitwise operations (`AND`, `OR`, `XOR`) operate without arithmetic carry-over between adjacent bit columns, global optimization reduces to 32 independent bit-level decision checks.

---

## The Independent Bit Construction Framework

```text
For bit position i from 0 to 31:
  Extract current bit of A  (bitA)
  Extract current bit of B  (bitB)
  Extract required bit of C (bitC)

  Compare (bitA | bitB) against bitC:
    • If bitC == 1 and (bitA == 0 && bitB == 0)  ──► Requires 1 flip (set either bitA or bitB)
    • If bitC == 0 and bitA == 1 && bitB == 1     ──► Requires 2 flips (clear both bitA and bitB)
    • If bitC == 0 and (bitA == 1 || bitB == 1)   ──► Requires 1 flip (clear set bit)
```

---

## Canonical Question Set (1 Core Question)

### 1. Minimum Flips to Make a OR b Equal to c (Understand & Bit Construction)
- **Problem:** <a href="https://leetcode.com/problems/minimum-flips-to-make-a-or-b-equal-to-c/" target="_blank">LeetCode 1318</a>
- **Difficulty:** Medium
- **Core Bitwise Idea:** Independent bit-by-bit comparison and flip counting across 32 bit columns.
- **Decision / Operation:** Iterate $i$ from 0 to 30. Extract bit `aBit = (a >> i) & 1`, `bBit = (b >> i) & 1`, `cBit = (c >> i) & 1`.
  - If `cBit == 0`: If `aBit == 1` increment `flips++`; if `bBit == 1` increment `flips++` (both must be 0).
  - If `cBit == 1`: If `aBit == 0 && bBit == 0`, increment `flips++` (at least one must be 1).
- **Invariant / Proof Intuition:** Bitwise OR operates independently per bit column with zero carry-over. Minimizing total flips across 32 independent columns guarantees the overall global minimum flips required.
- **Recognition Clue:** Minimum bit flips/changes required to transform bitwise expressions $A \text{ op } B$ into target $C$.
- **Common Wrong Approach:** Trying arithmetic difference operations or full integer simulation instead of independent bit-column inspection.
- **Complexity:** Time: $O(32) = O(1)$, Space: $O(1)$.
- **Cross-Pattern Connection:** Independent bit-column optimization framework.
