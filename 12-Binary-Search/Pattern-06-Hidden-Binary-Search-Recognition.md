# Pattern 06 — Hidden Binary Search Recognition

In technical interviews, problems rarely explicitly instruct: *"Use binary search."* Instead, they present un-sorted sequences, mountain array properties, or threshold queries.

Pattern Recognition First allows you to detect binary search opportunities in unlabeled problem statements.

---

## The Hidden Binary Search Diagnostic Test

Ask these 4 diagnostic questions when encountering an unfamiliar problem:

```text
1. Does the problem ask for an exact boundary, threshold, minimum, or maximum?
                        +
2. Is the candidate answer space monotonic (e.g., if x works, all x' > x also work)?
                        +
3. Can we eliminate half of the remaining search space with a single O(1) or O(N) evaluation?
                        +
4. Can sorting one input parameter unlock fast boundary lookups?

If YES to 1, 2, and 3 (or 4)  ==►  USE BINARY SEARCH
```

---

## Pattern Questions (2 Canonical)

### 1. Peak Index in a Mountain Array — <a href="https://leetcode.com/problems/peak-index-in-a-mountain-array/" target="_blank">LeetCode 852</a>

#### Initial Surface Impression
Appears to require linear scanning $O(N)$ to find the maximum element in an unsorted-looking array.

#### Hidden Monotonicity Signal
The array is guaranteed to be a "mountain":
`arr[0] < arr[1] < ... < arr[i] > arr[i+1] > ... > arr[N-1]`
- To the left of the peak: `arr[mid] < arr[mid + 1]` (slope is going **UP**).
- At or to the right of the peak: `arr[mid] > arr[mid + 1]` (slope is going **DOWN**).

This slope condition forms a **hidden monotonic boolean sequence**:
`[UP, UP, UP, DOWN, DOWN, DOWN]`
We need to find the **First DOWN** index (the Peak)!

#### Java Implementation
```java
public class PeakIndexInMountainArray {
    public int peakIndexInMountainArray(int[] arr) {
        int low = 0, high = arr.length - 1;

        while (low < high) {
            int mid = low + (high - low) / 2;

            if (arr[mid] < arr[mid + 1]) {
                low = mid + 1; // Ascending slope; peak must be to the right
            } else {
                high = mid;    // Descending slope; peak is at mid or to the left
            }
        }

        return low;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(\log N)$ — Binary search on mountain slope invariants.
- **Space Complexity:** $O(1)$ constant memory.

---

### 2. Successful Pairs of Spells and Potions — <a href="https://leetcode.com/problems/successful-pairs-of-spells-and-potions/" target="_blank">LeetCode 2300</a>

#### Initial Surface Impression
Appears to require nested loops $O(N \times M)$ checking every `spells[i] * potions[j] >= success`.

#### Why Naive Pairwise Scan Fails
For $N = 10^5$ spells and $M = 10^5$ potions, $O(N \times M) = 10^{10}$ operations causing TLE (Time Limit Exceeded).

#### Hidden Binary Search Recognition
1. Sorting `potions` takes $O(M \log M)$ time.
2. For each `spell`, we need to find the **minimum potion strength** `minPotion` such that:
   $$\text{minPotion} \ge \lceil \text{success} / \text{spell} \rceil$$
3. Finding the first potion $\ge \text{minPotion}$ in sorted `potions` is a standard **First-True Binary Search** taking $O(\log M)$ time!
4. Total pairs for `spell` = $M - \text{firstValidIndex}$.

#### Java Implementation
```java
import java.util.Arrays;

public class SuccessfulPairsOfSpellsAndPotions {
    public int[] successfulPairs(int[] spells, int[] potions, long success) {
        Arrays.sort(potions);
        int n = spells.length;
        int m = potions.length;
        int[] pairs = new int[n];

        for (int i = 0; i < n; i++) {
            long spell = spells[i];
            // Required minimum potion strength (ceiling division)
            long minPotion = (success + spell - 1) / spell;

            // Binary search for first potion >= minPotion
            int index = binarySearchFirstGreaterOrEqual(potions, minPotion);
            pairs[i] = m - index;
        }

        return pairs;
    }

    private int binarySearchFirstGreaterOrEqual(int[] potions, long target) {
        int low = 0, high = potions.length - 1;
        int ans = potions.length;

        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (potions[mid] >= target) {
                ans = mid;       // Potential first valid potion; search left
                high = mid - 1;
            } else {
                low = mid + 1;   // Potion strength too small; search right
            }
        }

        return ans;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(M \log M + N \log M)$ — Sorting potions + $N$ binary searches.
- **Space Complexity:** $O(\log M)$ sorting space (plus output array).

---

## Pattern Summary

| Problem | Surface Disguise | Hidden Monotonic Invariant | Binary Search Role |
| :--- | :--- | :--- | :--- |
| **LC 852 (Peak Index)** | Unsorted Mountain Array | Slope direction: `arr[mid] < arr[mid+1]` | Find first `DOWN` slope index |
| **LC 2300 (Spells & Potions)** | Quadratic $O(N \times M)$ pairwise product | Sorted `potions` array threshold | Find first potion $\ge \lceil \text{success}/\text{spell} \rceil$ |
