# Pattern 06: Greedy & Heap

> The Heap is not the greedy algorithm itself — it is the dynamic data structure that maintains the candidate set required to make optimal greedy choices as state changes.

---

## Why This Pattern Exists

In static Greedy problems (Module 01 / 02), sorting an array once is sufficient to make all greedy decisions.

However, in dynamic Greedy problems:
- Selecting a new candidate changes the budget, total duration, ladder usage, or efficiency threshold.
- To maintain an optimal candidate set, we must **undo** previous choices when a better candidate appears.
- A **Heap** allows us to evict the worst choice made so far in $O(\log K)$ time.

```text
                  [ Greedy Decision Strategy ]
                               │
                Candidate Set Changes Dynamically
                               │
                               ▼
        [ Priority Queue / Heap Candidate Container ]
           - Min Heap: Evicts smallest benefit / lowest speed
           - Max Heap: Evicts largest cost / longest duration
```

---

## ☕ Standard Java Templates

### Dynamic Regret / Eviction Pattern (Course Schedule III — LC 630)
```java
// Sort courses by deadline ascending
Arrays.sort(courses, (a, b) -> a[1] - b[1]);

// Max Heap stores durations of taken courses
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
int totalTime = 0;

for (int[] course : courses) {
    int duration = course[0], deadline = course[1];
    
    totalTime += duration;
    maxHeap.offer(duration);
    
    // Greedy Regret: If totalTime exceeds deadline, evict longest course taken so far!
    if (totalTime > deadline) {
        totalTime -= maxHeap.poll(); // Evict longest duration to free up maximum time
    }
}
return maxHeap.size();
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Meeting Rooms II** | Pure time-driven resource allocation — belongs to **Scheduling (Pattern 05)**. |
| **Kth Largest Element** | Pure rank selection without greedy regret swaps — belongs to **Kth Selection (Pattern 02)**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Medium** | 2 |
| **Hard** | 3 |
| **Total** | **5** |

---

## 🎯 Question Set (5 Questions)

### Q1. Maximum Performance of a Team
<a href="https://leetcode.com/problems/maximum-performance-of-a-team/" target="_blank">LeetCode 1383</a> — **Hard**

**Target Skill:** Sorting by bottleneck efficiency + Min Heap for top $K$ speed accumulation.

**Core Reasoning:**
- Performance = $(\sum \text{speeds}) \times \min(\text{efficiencies})$.
- **Greedy Strategy:** Sort engineers by efficiency descending. Current engineer's efficiency is the bottleneck minimum efficiency for all team members chosen so far.
- Maintain a **Min Heap** of selected engineers' speeds (capacity $K$).
- For each engineer: add `speed` to `speedSum`. Push `speed` to Min Heap. If `heap.size() > K`, subtract `heap.poll()` from `speedSum`.
- `maxPerformance = max(maxPerformance, speedSum * efficiency)`.

**Why it belongs here:** Canonical Greedy + Heap problem. Sorting locks the efficiency bottleneck, while Min Heap maintains the largest $K$ speeds.

**Complexity:** Time: $O(N \log N + N \log K)$, Space: $O(N + K)$.

---

### Q2. Furthest Building You Can Reach
<a href="https://leetcode.com/problems/furthest-building-you-can-reach/" target="_blank">LeetCode 1642</a> — **Medium**

**Target Skill:** Min Heap ladder assignment for largest height jumps.

**Core Reasoning:**
- **Greedy Strategy:** Ladders can cover ANY height climb, while bricks cover exact height climbs. Therefore, ladders should ALWAYS be used for the LARGEST climbs!
- Maintain a **Min Heap** storing height climbs allocated to ladders.
- For height climb $d > 0$: push $d$ to Min Heap.
- If `heap.size() > ladders`: the smallest climb in `heap` cannot use a ladder! Pop `smallestClimb = heap.poll()` and pay for it using bricks (`bricks -= smallestClimb`).
- If `bricks < 0`, you cannot reach current building $\to$ return `i - 1`.

**Why it belongs here:** Demonstrates using a Min Heap to dynamically assign finite high-value resources (ladders) to the largest climbs seen so far.

**Complexity:** Time: $O(N \log L)$ (where $L$ is number of ladders), Space: $O(L)$.

---

### Q3. Course Schedule III
<a href="https://leetcode.com/problems/course-schedule-iii/" target="_blank">LeetCode 630</a> — **Hard**

**Target Skill:** Greedy regret eviction using Max Heap.

**Core Reasoning:**
- Courses `[duration, lastDay]`. Maximize number of courses taken.
- **Greedy Strategy:** Sort courses by `lastDay` ascending (earliest deadline first).
- Maintain a **Max Heap** of durations of courses taken so far.
- Add `duration` to `totalTime`. Push `duration` to Max Heap.
- If `totalTime > lastDay`: we exceeded deadline! Evict the course with the **longest duration** taken so far (`totalTime -= maxHeap.poll()`). This frees up maximum time for future courses while keeping total count max.

**Why it belongs here:** Quintessential Greedy Regret pattern where a Max Heap undoes the worst past decision.

**Complexity:** Time: $O(N \log N)$, Space: $O(N)$.

---

### Q4. Minimum Cost to Hire K Workers
<a href="https://leetcode.com/problems/minimum-cost-to-hire-k-workers/" target="_blank">LeetCode 857</a> — **Hard**

**Target Skill:** Wage/Quality ratio sorting + Max Heap quality eviction.

**Core Reasoning:**
- Total cost for group of $K$ workers = $(\sum \text{quality}) \times \max(\text{wage/quality ratio})$.
- **Greedy Strategy:** Sort workers by `ratio = wage / quality` ascending.
- Iterate through workers. Current worker's ratio is the max ratio for the team.
- Maintain a **Max Heap** of worker qualities of size $K$.
- Add `quality` to `qualitySum`, push `quality` to Max Heap.
- If `heap.size() > K`, subtract `heap.poll()` (evict worker with largest quality to minimize `qualitySum`).
- When `heap.size() == K`, `minCost = min(minCost, qualitySum * ratio)`.

**Why it belongs here:** Advanced multi-attribute optimization combining ratio sorting with Max Heap sum minimization.

**Complexity:** Time: $O(N \log N + N \log K)$, Space: $O(N + K)$.

---

### Q5. Maximum Number of Events That Can Be Attended
<a href="https://leetcode.com/problems/maximum-number-of-events-that-can-be-attended/" target="_blank">LeetCode 1353</a> — **Medium**

**Target Skill:** Time-line loop with Min Heap end-day priority.

**Core Reasoning:**
- Events `[startDay, endDay]`. Each event takes 1 day to attend.
- **Greedy Strategy:** At any day $D$, among all active available events, ALWAYS attend the event that **ends earliest**!
- Sort events by `startDay`.
- Day loop $day = 1 \dots 100,000$:
  1. Add all events starting on `day` into Min Heap (ordered by `endDay`).
  2. Evict expired events from Min Heap (`endDay < day`).
  3. If Min Heap non-empty, pop earliest ending event, attend it (`eventsAttended++`).

**Why it belongs here:** Dynamic timeline simulation using Min Heap to prioritize earliest expiring opportunities.

**Complexity:** Time: $O(N \log N + D \log N)$, Space: $O(N)$.

---

## ⚡ Mastery Checklist

- [ ] Can you explain why `Course Schedule III` evicts the **longest duration** course when a deadline is breached?
- [ ] Why does `Furthest Building You Can Reach` use a Min Heap for ladders instead of a Max Heap?
- [ ] What is the greedy choice in `Minimum Cost to Hire K Workers` and why do we sort by ratio `wage / quality`?
- [ ] How does `Maximum Number of Events` use a Min Heap to resolve scheduling conflicts day by day?
