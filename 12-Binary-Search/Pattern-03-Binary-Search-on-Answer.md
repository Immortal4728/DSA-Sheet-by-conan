# Pattern 03 — Binary Search on Answer

Binary Search on Answer is an optimization paradigm applied when a problem asks us to find the **minimum** or **maximum** value of a numerical parameter that satisfies a specific condition.

Instead of constructing the optimal solution directly, we **search over the range of possible answers** `[low, high]` and convert the optimization problem into a boolean decision problem: `"Is candidate answer x feasible?"`

---

## The Search-on-Answer Framework

```text
1. Define Answer Space:
   Identify minimum possible value (low) and maximum possible value (high).

2. Verify Monotonicity:
   If x is feasible -> All values > x are feasible (for Minimization).
   If x is feasible -> All values < x are feasible (for Maximization).

3. Apply Binary Search:
   Binary search over numerical interval [low, high] using helper function feasible(mid).
```

> [!NOTE]
> **Cross-Section References**: Section 01 established foundational Search-on-Answer problems (<a href="https://leetcode.com/problems/koko-eating-bananas/" target="_blank">LeetCode 875 (Koko Eating Bananas)</a>, <a href="https://leetcode.com/problems/split-array-largest-sum/" target="_blank">LeetCode 410 (Split Array Largest Sum)</a>, and <a href="https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/" target="_blank">LeetCode 1011 (Ship Packages)</a>). Section 12 expands this architecture with advanced numerical answer space formulations.

---

## Pattern Questions (4 Canonical)

### 1. Magnetic Force Between Two Balls — <a href="https://leetcode.com/problems/magnetic-force-between-two-balls/" target="_blank">LeetCode 1552</a>

#### Problem Statement
Given an array `position` representing basket locations and an integer `m` representing `m` balls. The magnetic force between two balls at `p1` and `p2` is `|p1 - p2|`. Distribute all `m` balls into baskets such that the **minimum magnetic force between any two balls is maximized**.

#### Key Insight & Answer Space Construction
- **Goal**: Maximize the minimum distance $D$.
- **Answer Space**: $D \in [1, \text{position}[N-1] - \text{position}[0]]$.
- **Decision Function `canPlace(d)`**: Sort `position`. Place the 1st ball at `position[0]`. Greedily place subsequent balls at the next basket at least distance $d$ away. If we can place $\ge m$ balls, $d$ is feasible.

#### Java Implementation
```java
import java.util.Arrays;

public class MagneticForceBetweenBalls {
    public int maxDistance(int[] position, int m) {
        Arrays.sort(position);
        int n = position.length;
        int low = 1;
        int high = position[n - 1] - position[0];
        int ans = 1;

        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (canPlace(position, m, mid)) {
                ans = mid;       // Feasible! Try to maximize distance
                low = mid + 1;
            } else {
                high = mid - 1;  // Distance too large; shrink
            }
        }

        return ans;
    }

    private boolean canPlace(int[] position, int m, int dist) {
        int count = 1;
        int lastPos = position[0];

        for (int i = 1; i < position.length; i++) {
            if (position[i] - lastPos >= dist) {
                count++;
                lastPos = position[i];
                if (count >= m) return true;
            }
        }
        return false;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(N \log N + N \log(\text{max\_pos}))$ — Sorting takes $O(N \log N)$; binary search range takes $\log(\text{range})$ feasibility checks of $O(N)$.
- **Space Complexity:** $O(1)$ auxiliary space.

---

### 2. Minimum Limit of Balls in a Bag — <a href="https://leetcode.com/problems/minimum-limit-of-balls-in-a-bag/" target="_blank">LeetCode 1760</a>

#### Problem Statement
Given an integer array `nums` where `nums[i]` represents the number of balls in the `i`-th bag, and an integer `maxOperations`. You can divide any bag into two new bags. Minimize the **maximum number of balls in a bag** after at most `maxOperations`.

#### Key Insight & Division Count Formula
- **Goal**: Minimize maximum bag size $S$.
- **Answer Space**: $S \in [1, \max(\text{nums})]$.
- **Operations Required for Bag `num`**: To reduce a bag of size `num` so no piece exceeds $S$, we need $\lceil \text{num} / S \rceil - 1 = (\text{num} - 1) / S$ operations.

#### Java Implementation
```java
public class MinimumLimitOfBalls {
    public int minimumSize(int[] nums, int maxOperations) {
        int low = 1;
        int high = 0;
        for (int num : nums) high = Math.max(high, num);

        int ans = high;

        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (isFeasible(nums, maxOperations, mid)) {
                ans = mid;        // Feasible; try to find a smaller maximum bag size
                high = mid - 1;
            } else {
                low = mid + 1;    // Operations exceeded; increase candidate size
            }
        }

        return ans;
    }

    private boolean isFeasible(int[] nums, int maxOps, int maxBagSize) {
        long ops = 0;
        for (int num : nums) {
            ops += (num - 1) / maxBagSize;
            if (ops > maxOps) return false;
        }
        return true;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(N \log(\max(\text{nums})))$ — Feasibility check takes $O(N)$ for each binary search step.
- **Space Complexity:** $O(1)$ space.

---

### 3. Minimized Maximum of Products Distributed to Any Store — <a href="https://leetcode.com/problems/minimized-maximum-of-products-distributed-to-any-store/" target="_blank">LeetCode 2064</a>

#### Problem Statement
You are given `n` specialty stores and an array `quantities` where `quantities[i]` is the quantity of the `i`-th product type. Distribute all products to stores such that **no store gets more than one product type** and the **maximum number of products given to any store is minimized**.

#### Key Insight & Ceiling Division Store Requirement
- **Goal**: Minimize maximum products $K$ assigned to any store.
- **Answer Space**: $K \in [1, \max(\text{quantities})]$.
- **Stores Needed for Quantity $Q$**: $\lceil Q / K \rceil = (Q + K - 1) / K$.

#### Java Implementation
```java
public class MinimizedMaximumProducts {
    public int minimizedMaximum(int n, int[] quantities) {
        int low = 1;
        int high = 0;
        for (int q : quantities) high = Math.max(high, q);

        int ans = high;

        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (canDistribute(n, quantities, mid)) {
                ans = mid;        // Feasible; try smaller max quantity per store
                high = mid - 1;
            } else {
                low = mid + 1;    // Needs too many stores; increase max capacity
            }
        }

        return ans;
    }

    private boolean canDistribute(int n, int[] quantities, int maxPerStore) {
        long storesNeeded = 0;
        for (int q : quantities) {
            storesNeeded += (q + maxPerStore - 1) / maxPerStore; // Ceiling division
            if (storesNeeded > n) return false;
        }
        return true;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(N \log(\max(\text{quantities})))$ — Linear scan of quantities during search.
- **Space Complexity:** $O(1)$ constant space.

---

### 4. Minimum Speed to Arrive on Time — <a href="https://leetcode.com/problems/minimum-speed-to-arrive-on-time/" target="_blank">LeetCode 1870</a>

#### Problem Statement
Given an integer array `dist` where `dist[i]` is the distance of the `i`-th train ride, and a floating-point number `hour`. Each train must depart at an integer hour. Return the minimum positive integer speed (in km/h) that all trains must travel at to reach on time, or `-1` if impossible.

#### Key Insight & Floating-Point Time Accumulation
- **Goal**: Find minimum integer speed $S$.
- **Answer Space**: $S \in [1, 10^7]$.
- **Travel Time Formula for Speed $S$**:
  - For all intermediate trains $i < N - 1$: $\lceil \text{dist}[i] / S \rceil$.
  - For the final train $i = N - 1$: exact floating division $\text{dist}[N-1] / (double)S$.

#### Java Implementation
```java
public class MinimumSpeedToArriveOnTime {
    public int minSpeedOnTime(int[] dist, double hour) {
        int n = dist.length;
        if (hour <= n - 1) return -1; // Impossible if hour is <= intermediate trains count

        int low = 1;
        int high = 10000000; // 10^7 speed upper bound
        int ans = -1;

        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (timeRequired(dist, mid) <= hour) {
                ans = mid;       // Feasible speed; try to find a slower valid speed
                high = mid - 1;
            } else {
                low = mid + 1;   // Too slow; increase speed
            }
        }

        return ans;
    }

    private double timeRequired(int[] dist, int speed) {
        double totalTime = 0.0;
        for (int i = 0; i < dist.length - 1; i++) {
            totalTime += Math.ceil((double) dist[i] / speed);
        }
        // Final train does not require integer ceiling waiting time
        totalTime += (double) dist[dist.length - 1] / speed;
        return totalTime;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(N \log(10^7))$ — Fixed logarithmic steps with $O(N)$ distance checks.
- **Space Complexity:** $O(1)$ auxiliary space.

---

## Pattern Comparison Matrix

| Problem | Optimization Direction | Search Range `[low, high]` | Decision Function Evaluation |
| :--- | :--- | :--- | :--- |
| **LC 1552 (Magnetic Force)** | Maximize Min Distance | $[1, \text{pos}[N-1] - \text{pos}[0]]$ | Greedy placement $\ge m$ balls |
| **LC 1760 (Balls in Bag)** | Minimize Max Balls | $[1, \max(\text{nums})]$ | Total division ops $\le \text{maxOps}$ |
| **LC 2064 (Distribute Products)**| Minimize Max Products | $[1, \max(\text{quantities})]$ | Stores needed $\sum \lceil Q/K \rceil \le n$ |
| **LC 1870 (Min Speed)** | Minimize Integer Speed | $[1, 10^7]$ | Sum intermediate ceilings + final time $\le \text{hour}$ |
