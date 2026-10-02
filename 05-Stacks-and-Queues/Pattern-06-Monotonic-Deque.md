# Pattern 06: Monotonic Deque

> A Monotonic Deque (Double-Ended Queue) maintains elements in a monotonic order while supporting $O(1)$ evictions from **both ends**. It is the optimal structure for finding sliding window max/min values and prefix-sum constrained optimal subarrays.

---

## Why This Pattern Exists

In a standard sliding window, we need to find the maximum (or minimum) element inside a moving range $[i - K + 1, i]$.

Why doesn't a Monotonic Stack work?
- A stack only pops from the top (back). It cannot remove elements from the bottom (front) when they slide out of the left boundary of the window.

Why doesn't a Heap (Priority Queue) achieve $O(N)$?
- A heap finds max in $O(1)$, but removing an arbitrary expired element takes $O(\log K)$, leading to $O(N \log K)$ overall.

### The Monotonic Deque Mechanism

A Monotonic Deque stores candidate **indices** in monotonic order and performs two distinct evictions:
1. **Evict Dominated Candidates (From Back):** When incoming element `arr[i]` arrives, pop elements from the back of the deque that are smaller (or larger) than `arr[i]`. They can never be the max/min in any window containing `arr[i]`.
2. **Evict Expired Candidates (From Front):** Remove indices from the front of the deque if they fall outside the current window boundary (`deque.peekFirst() < i - K + 1`).

> **Result:** The front of the deque `deque.peekFirst()` **always** holds the index of the optimal element for the current window in $O(1)$ time.

---

## ☕ Standard Java Template

### Sliding Window Maximum ($O(N)$)
```java
public int[] maxSlidingWindow(int[] nums, int k) {
    int n = nums.length;
    int[] result = new int[n - k + 1];
    Deque<Integer> deque = new ArrayDeque<>(); // Stores indices

    for (int i = 0; i < n; i++) {
        // 1. Remove expired elements outside current window [i - k + 1, i]
        if (!deque.isEmpty() && deque.peekFirst() < i - k + 1) {
            deque.pollFirst();
        }

        // 2. Remove dominated elements from back (smaller than incoming nums[i])
        while (!deque.isEmpty() && nums[deque.peekLast()] <= nums[i]) {
            deque.pollLast();
        }

        // 3. Add current index
        deque.offerLast(i);

        // 4. Record result once first full window is reached
        if (i >= k - 1) {
            result[i - k + 1] = nums[deque.peekFirst()];
        }
    }
    return result;
}
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Daily Temperatures** | Fixed array right-scan without sliding window eviction — belongs to **Monotonic Stack (Pattern 03)**. |
| **Number of Recent Calls** | Simple queue expiration without monotonic ordering — belongs to **Queue Fundamentals (Pattern 05)**. |
| **Longest Substring Without Repeating Characters** | Two-pointer window tracking without max/min candidate eviction — belongs to **Array/String Sliding Window**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Hard** | 2 |
| **Total** | **2** |

---

## 🎯 Question Set (2 Questions)

### Q1. Sliding Window Maximum
<a href="https://leetcode.com/problems/sliding-window-maximum/" target="_blank">LeetCode 239</a> — **Hard**

**Target Skill:** Dual-ended eviction mechanism for sliding window maximum tracking.

**Core Reasoning:**
- Array of size $N$ and sliding window of size $K$.
- Maintain a monotonic **decreasing** deque of indices.
- For each element `nums[i]`:
  - Evict expired indices from front: `peekFirst() < i - k + 1`.
  - Evict smaller elements from back: `nums[peekLast()] <= nums[i]`.
  - Add index `i` to back.
  - Max for current window is `nums[peekFirst()]`.

**Why it belongs here:** Canonical, quintessential Monotonic Deque problem. Directly demonstrates why $O(N)$ beats $O(N \log K)$ Heap solutions.

**Complexity:** Time: $O(N)$, Space: $O(K)$.

---

### Q2. Shortest Subarray with Sum at Least K
<a href="https://leetcode.com/problems/shortest-subarray-with-sum-at-least-k/" target="_blank">LeetCode 862</a> — **Hard**

**Target Skill:** Monotonic deque over prefix sums with negative number handling.

**Core Reasoning:**
- Standard sliding window fails when numbers can be negative because prefix sums are not monotonic.
- Compute prefix sums `P[i]` where subarray sum $A[i..j-1] = P[j] - P[i] \ge K$.
- Maintain a monotonic **increasing** deque of prefix sum indices.
- For current index $j$ with prefix sum $P[j]$:
  1. **Shrink Window from Left:** While `P[j] - P[deque.peekFirst()] >= K`, record valid length `j - deque.pollFirst()`. (Popped indices are optimal for current $j$ and can never yield a *shorter* valid subarray for future $j'$).
  2. **Maintain Monotonicity from Right:** While `P[j] <= P[deque.peekLast()]`, pop from back (a larger prefix sum at a later index is dominated by $P[j]$).
  3. Push $j$ to deque.

**Why it belongs here:** Advanced application combining prefix sums with monotonic deque eviction to solve non-monotonic window constraints in $O(N)$ time.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

## ⚡ Mastery Checklist

- [ ] Can you articulate why a Monotonic Stack fails for sliding window max, but a Monotonic Deque succeeds?
- [ ] Do you know which end of the deque evicts **expired** elements vs **dominated** elements?
- [ ] Why must we store array **indices** in the deque rather than values?
- [ ] Can you explain why `Shortest Subarray with Sum at Least K` requires a monotonic deque on prefix sums when negative numbers are present?
