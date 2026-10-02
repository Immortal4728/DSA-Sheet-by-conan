# Pattern 03: Monotonic Stack

> A Monotonic Stack maintains its elements in a strictly increasing or decreasing order. It is the single most powerful pattern for solving $O(N^2)$ nearest-element and boundary-finding problems in $O(N)$ time.

---

## Why This Pattern Exists

Consider finding the **next greater element** for every item in an array of size $N$.
A brute-force search looks right from each index, requiring $O(N^2)$ time.

Why is brute-force wasteful? Because it repeatedly re-evaluates elements that can never serve as answers for future queries.

### The Underlying Invariant

When an incoming element arrives:
1. We identify existing elements in our stack that are **permanently dominated or rendered useless** by this new arrival.
2. We **pop** those useless elements.
3. The incoming element is added, preserving a strictly monotonic order (increasing or decreasing).

> **Core Insight:** An element is popped only when the incoming element provides the definitive answer for it, or makes it impossible for the popped element to ever be the answer for any future element.

Every element is pushed once and popped at most once. Thus, the amortized time complexity across the entire array is strictly **$O(N)$**.

---

## Types of Monotonic Stacks

| Type | Ordering (Bottom to Top) | What triggers a Pop? | Used to Find |
|------|---------------------------|----------------------|--------------|
| **Monotonic Decreasing** | Large $\to$ Small (`[9, 7, 5, 3]`) | Incoming element is **greater** than stack top (`item > top`) | **Next Greater Element** / **Previous Greater Element** |
| **Monotonic Increasing** | Small $\to$ Large (`[2, 4, 6, 8]`) | Incoming element is **smaller** than stack top (`item < top`) | **Next Smaller Element** / **Previous Smaller Element** |

> **Do NOT memorize these as fixed templates.** Always ask: *"Which elements are rendered useless when this new element arrives?"*

---

## ☕ Standard Java Templates

### 1. Next Greater Element (Monotonic Decreasing Stack)
```java
int[] result = new int[arr.length];
Arrays.fill(result, -1);
Deque<Integer> stack = new ArrayDeque<>(); // stores indices

for (int i = 0; i < arr.length; i++) {
    // Current element is GREATER than elements at stack top indices
    // Current element IS the next greater element for those popped indices!
    while (!stack.isEmpty() && arr[i] > arr[stack.peek()]) {
        int poppedIdx = stack.pop();
        result[poppedIdx] = arr[i];
    }
    stack.push(i);
}
return result;
```

### 2. Boundary Determination for Contribution Counting (Sum of Subarray Minimums)
```java
// For each index i, find distance to Previous Smaller Element (PLE) and Next Smaller Element (NLE)
int n = arr.length;
int[] ple = new int[n];
int[] nle = new int[n];
Deque<Integer> stack = new ArrayDeque<>();

// Find Previous Less or Equal (strict/non-strict inequality avoids duplicate counting)
for (int i = 0; i < n; i++) {
    while (!stack.isEmpty() && arr[stack.peek()] >= arr[i]) {
        stack.pop();
    }
    ple[i] = stack.isEmpty() ? i + 1 : i - stack.peek();
    stack.push(i);
}

stack.clear();

// Find Next Less
for (int i = n - 1; i >= 0; i--) {
    while (!stack.isEmpty() && arr[stack.peek()] > arr[i]) {
        stack.pop();
    }
    nle[i] = stack.isEmpty() ? n - i : stack.peek() - i;
    stack.push(i);
}
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Sliding Window Maximum** | Requires popping expired elements from the **front** as window shifts — belongs to **Monotonic Deque (Pattern 06)**. |
| **Min Stack** | Uses auxiliary min tracking for $O(1)$ stack ops — belongs to **Stack Design (Pattern 07)**. |
| **Car Fleet** | Hidden stack recognition based on sorting and trajectory arrival times — belongs to **Pattern 08**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 2 |
| **Medium** | 4 |
| **Hard** | 2 |
| **Total** | **8** |

---

## 🎯 Question Set (8 Questions)

### Q1. Next Greater Element I
<a href="https://leetcode.com/problems/next-greater-element-i/" target="_blank">LeetCode 496</a> — **Easy**

**Target Skill:** Basic monotonic decreasing stack with hash map lookup.

**Core Reasoning:**
- Process array `nums2` with a monotonic decreasing stack storing elements.
- When an incoming element `x` is greater than `stack.peek()`, pop `stack.peek()` and record `map.put(popped, x)`.
- Push `x` onto the stack. For elements remaining in the stack at the end, next greater is `-1`.
- Map results back to `nums1`.

**Why it belongs here:** The fundamental entry problem for monotonic stack. Teaches how popping directly answers the query for popped elements.

**Complexity:** Time: $O(N + M)$, Space: $O(N)$.

---

### Q2. Daily Temperatures
<a href="https://leetcode.com/problems/daily-temperatures/" target="_blank">LeetCode 739</a> — **Medium**

**Target Skill:** Distance/index gap calculation using monotonic stack.

**Core Reasoning:**
- Store indices on a monotonic decreasing stack.
- For current day `i` with temperature `T[i]`: while `T[i] > T[stack.peek()]`, pop index `prevIdx`. The number of days waited is `i - prevIdx`.
- Push current day index `i`.

**Why it belongs here:** Standard next greater element variation where the required answer is the **index distance** rather than the element value.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

### Q3. Next Greater Element II
<a href="https://leetcode.com/problems/next-greater-element-ii/" target="_blank">LeetCode 503</a> — **Medium**

**Target Skill:** Monotonic stack on circular/wrap-around arrays.

**Core Reasoning:**
- Array is circular, meaning elements can wrap around to find a greater element.
- Virtualize array of length `2 * N` using modulo operator `i % N`.
- Run monotonic decreasing stack for `2 * N` iterations. Only push indices onto stack when `i < N`.

**Why it belongs here:** Extends monotonic stack reasoning to circular arrays without physically duplicating the array.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

### Q4. Online Stock Span
<a href="https://leetcode.com/problems/online-stock-span/" target="_blank">LeetCode 901</a> — **Medium**

**Target Skill:** Online state aggregation and previous greater element tracking.

**Core Reasoning:**
- Stock span is the number of consecutive days prior (including today) where price was $\le$ today's price.
- Maintain a stack storing pairs of `[price, span]`.
- When new price arrives: while `stack.peek().price <= price`, pop top pair and add its span to `currentSpan`.
- Push `[price, currentSpan]` and return `currentSpan`.

**Why it belongs here:** Demonstrates online stream processing where popping collapses smaller historical spans into a single aggregated node.

**Complexity:** Time: Amortized $O(1)$ per call, Space: $O(N)$.

---

### Q5. Final Prices With a Special Discount in a Shop
<a href="https://leetcode.com/problems/final-prices-with-a-special-discount-in-a-shop/" target="_blank">LeetCode 1475</a> — **Easy**

**Target Skill:** Next smaller element invariant application.

**Core Reasoning:**
- Discount for item `i` is `prices[j]`, where `j` is the first index `j > i` such that `prices[j] <= prices[i]`.
- Maintain a monotonic **increasing** stack of indices.
- When `prices[i]` is $\le$ `prices[stack.peek()]`, `prices[i]` is the discount for `stack.pop()`. Subtract `prices[i]` from `prices[popped]`.

**Why it belongs here:** Direct application of monotonic increasing stack to locate the **next smaller or equal element**.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

### Q6. Largest Rectangle in Histogram
<a href="https://leetcode.com/problems/largest-rectangle-in-histogram/" target="_blank">LeetCode 84</a> — **Hard**

**Target Skill:** Left/right boundary determination for area maximization.

**Core Reasoning:**
- For a bar at height `h`, the maximum rectangle using height `h` extends left to the first bar shorter than `h` and right to the first bar shorter than `h`.
- Maintain a monotonic increasing stack of indices.
- When an incoming bar is shorter than `heights[stack.peek()]`, pop the stack.
  - Height of rectangle is `heights[popped]`.
  - Right boundary is current index `i`.
  - Left boundary is new `stack.peek()` (or `-1` if empty).
  - Width = `right - left - 1`. Area = `height * width`.

**Why it belongs here:** Canonical hard monotonic stack problem. Teaches how stack popping naturally identifies both left and right boundaries for the popped element simultaneously.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

### Q7. Sum of Subarray Minimums
<a href="https://leetcode.com/problems/sum-of-subarray-minimums/" target="_blank">LeetCode 907</a> — **Medium**

**Target Skill:** Contribution counting via Previous Less and Next Less boundaries.

**Core Reasoning:**
- Every element `arr[i]` acts as the minimum for a number of subarrays.
- Find distance to Previous Less Element (`left_count`) and Next Less Element (`right_count`).
- Total subarrays where `arr[i]` is minimum = `left_count * right_count`.
- Total contribution of `arr[i]` = `arr[i] * left_count * right_count`.
- **Handling Duplicates:** Use strict inequality (`<`) for left boundary and non-strict (`<=`) for right boundary to prevent double-counting.

**Why it belongs here:** Teaches contribution counting math combined with boundary detection via monotonic stack.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

### Q8. Trapping Rain Water
<a href="https://leetcode.com/problems/trapping-rain-water/" target="_blank">LeetCode 42</a> — **Hard**

**Target Skill:** Horizontal layer-by-layer water trapping via monotonic stack.

**Core Reasoning:**
- Maintain a monotonic decreasing stack of bar indices.
- When current bar `height[i]` > `height[stack.peek()]`:
  - Pop `bounded_index = stack.pop()` (this is the floor of the water container).
  - If stack is empty, break (no left wall).
  - `left_wall = stack.peek()`, `right_wall = i`.
  - `bounded_height = min(height[left_wall], height[right_wall]) - height[bounded_index]`.
  - `bounded_width = right_wall - left_wall - 1`.
  - Add `bounded_height * bounded_width` to total water.

**Why it belongs here:** Demonstrates horizontal integration of trapped areas between bounding walls using a monotonic stack.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

## ⚡ Mastery Checklist

- [ ] Can you explain the invariant behind popping useless elements in a monotonic stack?
- [ ] Do you know when to store indices vs actual values in the stack?
- [ ] Can you handle circular array traversals without duplicating memory?
- [ ] Do you understand how strict vs non-strict inequalities prevent duplicate counting in contribution problems?
- [ ] Can you derive the height and width boundaries in `Largest Rectangle in Histogram` from stack pops?
