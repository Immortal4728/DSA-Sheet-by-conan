# Pattern 05 — Bitwise Trie & XOR

A **Bitwise Trie** (or Binary Trie) stores numbers as fixed-length binary bit sequences (e.g., 31-bit integers from Most Significant Bit to Least Significant Bit). Bitwise Tries serve as the crucial bridge between **Tries** and **Bit Manipulation**.

---

## The Max XOR Decision Principle

To maximize the Bitwise XOR result ($A \oplus B$), we want the highest possible bits of the result to be `1`.
For each bit `b` of number $A$ (starting from bit 31 down to bit 0):
- If $A$ has bit `0` at position `b`, greedily search for a child node with bit `1`.
- If $A$ has bit `1` at position `b`, greedily search for a child node with bit `0`.
- If the opposite bit branch exists, take it! (Gives `1` at current bit position).
- If the opposite bit branch does not exist, take the matching bit branch (Gives `0` at current bit position).

```text
                  (Root Node)
                 /           \
             Bit 0           Bit 1
            /     \         /     \
          Bit 0  Bit 1   Bit 0   Bit 1
```

---

## Pattern Questions (1 Core + 1 Selective)

### 1. Maximum XOR of Two Numbers in an Array (Understand & Apply) [CORE]
- **Problem:** <a href="https://leetcode.com/problems/maximum-xor-of-two-numbers-in-an-array/" target="_blank">LeetCode 421</a>
- **Difficulty:** Medium
- **Core Idea:** Binary Trie with 31-bit greedy opposite-bit traversal.
- **Decision / Mechanics:** Build a Binary Trie where each node has `children[0]` and `children[1]`.
  1. Insert all numbers in binary representation (31 bits from MSB to LSB).
  2. For each number `num`, traverse the Trie from bit 31 down to bit 0. Greedily try to follow the opposite bit branch `1 - bit`. If opposite bit exists, add `1 << i` to current XOR sum and move to opposite node; else move to matching node.
  3. Track global maximum XOR.
- **Invariant / Proof Intuition:** Greedily maximizing higher-order bits (MSB) guarantees a larger total integer value than any combination of lower-order bits, since $2^k > \sum_{i=0}^{k-1} 2^i$.
- **Recognition Clue:** Maximize XOR sum of any two numbers in an array without checking all $O(N^2)$ pairs.
- **Common Wrong Approach:** Pairwise $O(N^2)$ XOR checks causing TLE for $N = 2 \times 10^5$.
- **Complexity:** Time: $O(31 \times N) = O(N)$, Space: $O(31 \times N) = O(N)$.
- **Cross-Pattern Connection:** Bridge between Tries (Section 14) and Bit Manipulation (Section 15).

---

### 2. Maximum XOR With an Element From Array (Selective / Advanced Exposure) [SELECTIVE]

> [!NOTE]
> **Selective / Advanced Status**: LC 1707 extends Binary Trie XOR reasoning by adding offline query sorting to satisfy threshold constraints.

- **Problem:** <a href="https://leetcode.com/problems/maximum-xor-with-an-element-from-array/" target="_blank">LeetCode 1707</a>
- **Difficulty:** Hard
- **Core Idea:** Offline Query Sorting + Binary Trie for threshold-constrained Max XOR.
- **Decision / Mechanics:** Given queries `[x_i, m_i]` asking for max `x_i ^ num` where `num <= m_i`:
  1. Sort `nums` ascending.
  2. Sort queries by threshold `m_i` ascending while retaining original query indices.
  3. Process queries sequentially: dynamically insert into the Binary Trie only those `nums` that are `<= m_i`.
  4. Query the Binary Trie for max XOR for `x_i`. If Trie is empty, result is `-1`.
- **Invariant / Proof Intuition:** Offline query processing ensures that elements are inserted into the Binary Trie incrementally up to threshold $m_i$, guaranteeing that all nodes in the Trie satisfy `num <= m_i` without needing complex range pruning.
- **Recognition Clue:** Range or threshold-constrained maximum XOR queries over an array.
- **Common Wrong Approach:** Rebuilding a new Binary Trie for each query from scratch ($O(Q \times N)$ TLE).
- **Complexity:** Time: $O(N \log N + Q \log Q + (N + Q) \times 31)$, Space: $O(N \times 31 + Q)$.
- **Cross-Pattern Connection:** Combines Pattern 05 (Bitwise Trie) with Offline Query Processing techniques.
