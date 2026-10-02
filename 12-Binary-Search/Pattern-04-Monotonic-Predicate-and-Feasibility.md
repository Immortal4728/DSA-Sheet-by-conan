# Pattern 04 — Monotonic Predicate & Feasibility

Pattern 03 focused on constructing the numerical answer space `[low, high]`. Pattern 04 shifts focus to **designing and proving the Monotonic Feasibility Predicate**: `feasible(x) -> boolean`.

---

## Pattern Distinction: Pattern 03 vs Pattern 04

```text
Pattern 03: Searching the Answer Space
  • Goal: Establish numerical bounds [low, high] for optimization parameters.
  • Focus: Answer space construction and boundary shrinking.

Pattern 04: Designing & Proving the Monotonic Predicate
  • Goal: Formulate a decision function feasible(x) that is strictly monotonic.
  • Focus: Proving why x -> feasible(x) produces a contiguous boolean step-function:
          [false, false, false, true, true, true]
```

---

## Proving Monotonicity Framework

A function `feasible(x)` is **monotonically increasing** if:
$$x_1 \le x_2 \implies \text{feasible}(x_1) \le \text{feasible}(x_2)$$

If increasing $x$ (e.g., waiting more days, increasing divisor, or giving more time) can **never** make a previously valid state invalid, the predicate is monotonic. Once monotonicity is proven, binary search is guaranteed to find the exact transition boundary.

---

## Pattern Questions (3 Canonical)

### 1. Minimum Number of Days to Make m Bouquets — <a href="https://leetcode.com/problems/minimum-number-of-days-to-make-m-bouquets/" target="_blank">LeetCode 1482</a>

#### Problem Statement
Given an integer array `bloomDay`, an integer `m` (number of bouquets needed), and an integer `k` (number of **adjacent** flowers needed per bouquet). Return the minimum number of days to make `m` bouquets, or `-1` if impossible.

#### Proving Monotonicity of `canMake(day)`
- If we can make $m$ bouquets on day $D_1$, then on day $D_2 > D_1$ all flowers that bloomed on day $D_1$ remain bloomed. Thus, `canMake(day)` is strictly monotonically increasing: `[F, F, F, T, T]`.
- **Predicate Rule**: Iterate through `bloomDay`. Count consecutive bloomed flowers (`bloomDay[i] <= day`). Whenever count reaches `k`, increment bouquet count and reset consecutive count.

#### Java Implementation
```java
public class MinimumDaysForBouquets {
    public int minDays(int[] bloomDay, int m, int k) {
        // Fast feasibility check: if total flowers needed > total available flowers
        if ((long) m * k > bloomDay.length) return -1;

        int low = Integer.MAX_VALUE;
        int high = Integer.MIN_VALUE;
        for (int day : bloomDay) {
            low = Math.min(low, day);
            high = Math.max(high, day);
        }

        int ans = -1;

        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (canMake(bloomDay, m, k, mid)) {
                ans = mid;       // Feasible; search left for earlier day
                high = mid - 1;
            } else {
                low = mid + 1;   // Not enough bouquets; search right
            }
        }

        return ans;
    }

    private boolean canMake(int[] bloomDay, int m, int k, int day) {
        int bouquets = 0;
        int flowers = 0;

        for (int bloom : bloomDay) {
            if (bloom <= day) {
                flowers++;
                if (flowers == k) {
                    bouquets++;
                    flowers = 0; // Reset consecutive counter
                }
            } else {
                flowers = 0;     // Discontinuity: adjacency broken
            }
        }
        return bouquets >= m;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(N \log(\max(\text{bloomDay})))$ — Feasibility predicate takes linear time $O(N)$.
- **Space Complexity:** $O(1)$ constant space.

---

### 2. Find the Smallest Divisor Given a Threshold — <a href="https://leetcode.com/problems/find-the-smallest-divisor-given-a-threshold/" target="_blank">LeetCode 1283</a>

#### Problem Statement
Given an array of integers `nums` and an integer `threshold`, choose a positive integer `divisor`, divide all elements by it, and sum the division results (rounded UP to nearest integer). Return the smallest `divisor` such that the result is $\le \text{threshold}$.

#### Proving Monotonicity of `computeSum(divisor)`
- As `divisor` increases, $\lceil \text{num} / \text{divisor} \rceil$ decreases or remains equal. Thus, `computeSum(divisor)` is **monotonically decreasing**.
- Therefore, the predicate `computeSum(divisor) <= threshold` is **monotonically increasing**: `[F, F, T, T, T]`.

#### Java Implementation
```java
public class SmallestDivisorThreshold {
    public int smallestDivisor(int[] nums, int threshold) {
        int low = 1;
        int high = 0;
        for (int num : nums) high = Math.max(high, num);

        int ans = high;

        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (computeSum(nums, mid) <= threshold) {
                ans = mid;        // Feasible; try to find a smaller divisor
                high = mid - 1;
            } else {
                low = mid + 1;    // Sum exceeded threshold; increase divisor
            }
        }

        return ans;
    }

    private long computeSum(int[] nums, int divisor) {
        long sum = 0;
        for (int num : nums) {
            sum += (num + divisor - 1) / divisor; // Ceiling division
        }
        return sum;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(N \log(\max(\text{nums})))$ — Feasibility check takes $O(N)$.
- **Space Complexity:** $O(1)$ auxiliary space.

---

### 3. Minimum Time to Complete Trips — <a href="https://leetcode.com/problems/minimum-time-to-complete-trips/" target="_blank">LeetCode 2187</a>

#### Problem Statement
You are given an array `time` where `time[i]` denotes the time taken by the `i`-th bus to complete one trip. Given an integer `totalTrips`, return the **minimum time** required for all buses to complete at least `totalTrips` trips in total.

#### Proving Monotonicity & 64-Bit Upper Bound Calculation
- For a given candidate time $T$, the total trips completed by bus $i$ is $\lfloor T / \text{time}[i] \rfloor$.
- Total trips completed is $\sum \lfloor T / \text{time}[i] \rfloor$, which is strictly monotonically increasing with $T$.
- **Upper Bound Overflow Avoidance**: Max time upper bound can reach $\min(\text{time}) \times \text{totalTrips} = 10^7 \times 10^7 = 10^{14}$. Must use `long` for search bounds!

#### Java Implementation
```java
public class MinimumTimeToCompleteTrips {
    public long minimumTime(int[] time, int totalTrips) {
        long minBusTime = Long.MAX_VALUE;
        for (int t : time) minBusTime = Math.min(minBusTime, t);

        long low = 1;
        long high = minBusTime * totalTrips; // Long upper bound to prevent 32-bit overflow
        long ans = high;

        while (low <= high) {
            long mid = low + (high - low) / 2;
            if (totalTripsCompleted(time, mid) >= totalTrips) {
                ans = mid;        // Feasible; search for smaller valid time
                high = mid - 1;
            } else {
                low = mid + 1;    // Trips insufficient; increase time
            }
        }

        return ans;
    }

    private long totalTripsCompleted(int[] time, long givenTime) {
        long trips = 0;
        for (int t : time) {
            trips += givenTime / t;
        }
        return trips;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(N \log(\min(\text{time}) \times \text{totalTrips}))$ — Binary search over $10^{14}$ space with linear scan $O(N)$.
- **Space Complexity:** $O(1)$ constant auxiliary memory.

---

## Pattern Comparison Matrix

| Problem | Monotonic Property | Feasibility Accumulation Formula | Boundary Transition |
| :--- | :--- | :--- | :--- |
| **LC 1482 (Bouquets)** | Days bloomed $\uparrow \implies$ Bouquets $\uparrow$ | Adjacent bloomed count $\ge k$ | First `true` day |
| **LC 1283 (Smallest Divisor)**| Divisor $\uparrow \implies$ Sum $\downarrow$ | $\sum \lceil \text{num} / \text{divisor} \rceil \le \text{threshold}$ | First `true` divisor |
| **LC 2187 (Complete Trips)** | Time $T \uparrow \implies$ Total Trips $\uparrow$ | $\sum \lfloor T / \text{time}[i] \rfloor \ge \text{totalTrips}$ | First `true` long time |
