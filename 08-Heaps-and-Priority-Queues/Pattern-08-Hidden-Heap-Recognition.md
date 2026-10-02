# Pattern 08: Hidden Heap Recognition (Recognition Layer Only)

> In technical interviews, problems are rarely titled "PriorityQueue Practice". Your primary challenge is recognizing when a dynamic candidate set requires $O(1)$ access to the current best choice as state changes.

---

## 🛑 Important Note on Curriculum Architecture

> **This is a RECOGNITION ONLY pattern.** It does NOT introduce new LeetCode questions to the syllabus. It uses canonical problems introduced in earlier patterns as unlabeled recognition exercises:
> 1. **Furthest Building You Can Reach** (<a href="https://leetcode.com/problems/furthest-building-you-can-reach/" target="_blank">LeetCode 1642</a> — Pattern 06)
> 2. **Course Schedule III** (<a href="https://leetcode.com/problems/course-schedule-iii/" target="_blank">LeetCode 630</a> — Pattern 06)
> 3. **Single-Threaded CPU** (<a href="https://leetcode.com/problems/single-threaded-cpu/" target="_blank">LeetCode 1834</a> — Pattern 05)
> 4. **Smallest Range Covering Elements from K Lists** (<a href="https://leetcode.com/problems/smallest-range-covering-elements-from-k-lists/" target="_blank">LeetCode 632</a> — Pattern 04)
> 5. **Maximum Number of Events That Can Be Attended** (<a href="https://leetcode.com/problems/maximum-number-of-events-that-can-be-attended/" target="_blank">LeetCode 1353</a> — Pattern 06)
> 6. **Reorganize String** (<a href="https://leetcode.com/problems/reorganize-string/" target="_blank">LeetCode 767</a> — Pattern 07)

---

## The First-Principles Recognition Question

When reading an unfamiliar interview problem, never ask: *"Do I see the word Heap in the prompt?"*

Instead, ask:
> *"What candidate do I repeatedly need access to as state changes?"*

```text
               Candidate Set Changes Dynamically
                               │
            Repeatedly Need Current Best Choice
                               │
                               ▼
            [ Priority Queue / Heap Selection ]
```

---

## 🧠 Recognition Diagnostic Table

| Problem Statement Hint | Uncovered Constraint | Derived Strategy | Canonical Reference |
|------------------------|----------------------|------------------|---------------------|
| *"Climb heights using bricks or limited ladders"* | Ladders must cover largest climbs seen so far. | Min Heap tracks ladder climbs; pay smaller climbs with bricks. | <a href="https://leetcode.com/problems/furthest-building-you-can-reach/" target="_blank">LC 1642</a> |
| *"Maximize courses taken given deadline constraints"* | Exceeding deadline requires evicting longest course taken. | Max Heap stores durations of taken courses; poll longest when over deadline. | <a href="https://leetcode.com/problems/course-schedule-iii/" target="_blank">LC 630</a> |
| *"CPU executes available tasks by shortest processing time"* | Tasks arrive over time; available pool changes dynamically. | Sort by arrival; Min Heap picks shortest processing time among arrived. | <a href="https://leetcode.com/problems/single-threaded-cpu/" target="_blank">LC 1834</a> |
| *"Find smallest range covering at least one element from K lists"* | Must track minimum and maximum among active list heads. | Min Heap of capacity $K$ holds current list elements; advance min pointer. | <a href="https://leetcode.com/problems/smallest-range-covering-elements-from-k-lists/" target="_blank">LC 632</a> |
| *"Attend max events on a 1D timeline"* | Multiple events active on day $D$; priority goes to earliest expiring. | Min Heap ordered by event end day; attend top event each day. | <a href="https://leetcode.com/problems/maximum-number-of-events-that-can-be-attended/" target="_blank">LC 1353</a> |
| *"Rearrange string so no adjacent chars are equal"* | Most frequent character must be placed with alternating blocks. | Max Heap by character frequency; poll top 2 or block current choice. | <a href="https://leetcode.com/problems/reorganize-string/" target="_blank">LC 767</a> |

---

## ⚡ Recognition Mastery Checklist

- [ ] Can you identify when a problem requires dynamic candidate eviction vs static array sorting?
- [ ] Why does "finding the optimal subset under changing constraints" almost always require a Heap?
- [ ] How do you distinguish a Two-Heaps problem (LC 295) from a Single-Heap scheduling problem (LC 1834)?
