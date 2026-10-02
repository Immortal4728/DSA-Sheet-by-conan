# Pattern 04 — Sorting & Selection Greedy

Sorting exposes underlying mathematical relationships (such as value density, height dominance, or extreme weight pairings) that make optimal greedy selections obvious. Preprocessing data with custom sorting parameters transforms complex combinatorial choices into linear two-pointer scans or structured list insertions.

---

## Learning Progression

```text
Understand (Density Sorting) ──► Apply (Extreme Pair Matching) ──► Recognize (Relative Height Placement) ──► Transfer (Resource Trade-Off Selection)
```

1. **Understand (Density Sorting)**: Sort items by unit value density (<a href="https://leetcode.com/problems/maximum-units-on-a-truck/" target="_blank">LeetCode 1710</a>).
2. **Apply (Extreme Pair Matching)**: Pair smallest and largest elements using two pointers (<a href="https://leetcode.com/problems/boats-to-save-people/" target="_blank">LeetCode 881</a>).
3. **Recognize (Height Placement)**: Sort tallest-first to make insertion positions independent of shorter elements (<a href="https://leetcode.com/problems/queue-reconstruction-by-height/" target="_blank">LeetCode 406</a>).
4. **Transfer (Resource Trade-Off)**: Buy cheap score with low power, sell score for high power (<a href="https://leetcode.com/problems/bag-of-tokens/" target="_blank">LeetCode 948</a>).

> [!NOTE]
> **Canonical Ownership Rule:** <a href="https://leetcode.com/problems/largest-number/" target="_blank">LeetCode 179 (Largest Number)</a> remains canonically owned by **Section 01 Arrays / Sorting**. It is cross-referenced here as an example of `sorting + custom comparator + greedy interpretation`, but is not duplicated.

---

## Canonical Question Set (4 Questions)

### 1. Maximum Units on a Truck (Understand)
- **Problem:** <a href="https://leetcode.com/problems/maximum-units-on-a-truck/" target="_blank">LeetCode 1710</a>
- **Difficulty:** Easy
- **Core Greedy Idea:** Value density sorting (Fractional Knapsack logic).
- **Greedy Decision:** Sort `boxTypes` descending by `units_per_box`. Load boxes from the highest unit density box type until `truckSize` capacity is exhausted.
- **Invariant / Proof Intuition:** Selecting boxes with higher unit density yields strictly more total units per unit of truck capacity used compared to lower-density boxes.
- **Recognition Clue:** Capacity-constrained loading problem where fractional or whole items can be selected by unit value density.
- **Common Wrong Approach:** Sorting by total number of boxes instead of units per box.
- **Complexity:** Time: $O(N \log N)$, Space: $O(1)$.
- **Cross-Pattern Connection:** Fractional Knapsack greedy paradigm.

---

### 2. Boats to Save People (Apply)
- **Problem:** <a href="https://leetcode.com/problems/boats-to-save-people/" target="_blank">LeetCode 881</a>
- **Difficulty:** Medium
- **Core Greedy Idea:** Extreme pairing (pairing heaviest person with lightest person).
- **Greedy Decision:** Sort `people` ascending. Use two pointers (`left`, `right`). If `people[left] + people[right] <= limit`, pair them in 1 boat (`left++, right--`). Otherwise, place heaviest person alone (`right--`).
- **Invariant / Proof Intuition:** If the heaviest person can pair with anyone, they can pair with the lightest person. If they cannot pair with the lightest person, they cannot pair with anyone and must travel alone.
- **Recognition Clue:** Matching elements into capacity-constrained pairs (max 2 items per container).
- **Common Wrong Approach:** Trying to pair consecutive elements without sorting, or pairing two heavy elements together.
- **Complexity:** Time: $O(N \log N)$, Space: $O(1)$.
- **Cross-Pattern Connection:** Two-pointer greedy pairing.

---

### 3. Queue Reconstruction by Height (Recognize)
- **Problem:** <a href="https://leetcode.com/problems/queue-reconstruction-by-height/" target="_blank">LeetCode 406</a>
- **Difficulty:** Medium
- **Core Greedy Idea:** Tallest-first placement invariant.
- **Greedy Decision:** Sort `people` **descending by height** $h$; if heights are equal, sort **ascending by $k$**. Iterate sorted people and insert each person at index $k$ into the output list.
- **Invariant / Proof Intuition:** When inserting a shorter person at index $k$, all previously inserted people are taller or equal. Thus, placing the current person at index $k$ satisfies their exact $k$-count without affecting the $k$-counts of taller people already placed.
- **Recognition Clue:** Reconstructing relative ordering given height and count of taller elements in front.
- **Common Wrong Approach:** Sorting shortest-first, which requires complex dynamic slot reservations.
- **Complexity:** Time: $O(N^2)$, Space: $O(N)$.
- **Cross-Pattern Connection:** Relative order construction via sorting.

---

### 4. Bag of Tokens (Transfer)
- **Problem:** <a href="https://leetcode.com/problems/bag-of-tokens/" target="_blank">LeetCode 948</a>
- **Difficulty:** Medium
- **Core Greedy Idea:** Dual-endpoint resource trade-off optimization.
- **Greedy Decision:** Sort `tokens`. Use two pointers (`left`, `right`).
  - Face Up: Buy 1 score using smallest token `tokens[left]` when `power >= tokens[left]`.
  - Face Down: Trade 1 score for largest token `tokens[right]` when `power` is insufficient and `score >= 1`.
- **Invariant / Proof Intuition:** Buying score with the smallest token minimizes power cost; trading score for the largest token maximizes power gain for future score purchases.
- **Recognition Clue:** Two distinct modes of exchange (buying low, selling high) to maximize score.
- **Common Wrong Approach:** Playing tokens in arbitrary order without sorting.
- **Complexity:** Time: $O(N \log N)$, Space: $O(1)$.
- **Cross-Pattern Connection:** Connects to two-pointer extreme choice patterns.
