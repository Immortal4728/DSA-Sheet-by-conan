# Pattern 05: Scheduling & Resource Allocation

> Scheduling and resource allocation problems sort tasks/events by arrival time and use **Priority Queues** to dynamically select the next available CPU, server, meeting room, or cooling slot based on multi-attribute priorities.

---

## Why This Pattern Exists

In operating systems, cloud server pools, and calendar scheduling:
- Tasks arrive at different timestamps $T_{start}$.
- Multiple resources (CPUs, servers, rooms) become busy and free up at different end times $T_{end}$.

Sorting alone cannot handle dynamic resource availability. A **Heap** is required to track:
1. **Busy Resources:** Min Heap ordered by earliest finish time (`freeTime`).
2. **Available Resources:** Min Heap ordered by resource priority (smallest server index, highest weight, etc.).

```text
               [ Sorted Tasks (by arrival time) ]
                               │
               Current Time = T
                               │
      ┌────────────────────────┴────────────────────────┐
      ▼                                                 ▼
[ Busy Heap ] (ordered by freeTime)       [ Available Heap ] (ordered by priority)
   - Pop resources whose freeTime <= T  ──►  - Push to Available Heap
                                             - Pop highest-priority resource to run task
```

---

## ☕ Standard Java Templates

### Resource Allocation Pattern (Process Tasks / Meeting Rooms III)
```java
// PriorityQueue 1: Available servers (ordered by server weight/index)
PriorityQueue<int[]> available = new PriorityQueue<>((a, b) -> a[0] != b[0] ? a[0] - b[0] : a[1] - b[1]);

// PriorityQueue 2: Busy servers (ordered by freeTime ascending)
PriorityQueue<int[]> busy = new PriorityQueue<>((a, b) -> a[0] != b[0] ? a[0] - b[0] : a[1] - b[1]);

// In simulation loop at time T:
// 1. Move all busy servers with freeTime <= T back into available heap
while (!busy.isEmpty() && busy.peek()[0] <= time) {
    int[] server = busy.poll();
    available.offer(new int[]{server[1], server[2]}); // [weight, index]
}

// 2. Assign pending task to highest priority available server
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Task Scheduler (LC 621)** | Belongs **exclusively to Pattern 05** (cooling interval resource allocation). (Not duplicated in Pattern 07). |
| **Reorganize String (LC 767)** | Frequency-based character placement — belongs to **Custom Priority (Pattern 07)**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Medium** | 4 |
| **Hard** | 1 |
| **Total** | **5** |

---

## 🎯 Question Set (5 Questions)

### Q1. Meeting Rooms II
<a href="https://leetcode.com/problems/meeting-rooms-ii/" target="_blank">LeetCode 253</a> — **Medium**

**Target Skill:** Min Heap tracking room end-times.

**Core Reasoning:**
- Sort meetings by start time.
- Maintain `PriorityQueue<Integer> minHeap` storing end times of active meetings.
- For meeting `[start, end]`:
  - If `minHeap.peek() <= start`, a room freed up! Pop earliest end time (`minHeap.poll()`).
  - Push current meeting's `end` time into `minHeap`.
- Answer = `minHeap.size()` (max simultaneous rooms required).

**Why it belongs here:** Canonical foundation for dynamic resource end-time tracking.

**Complexity:** Time: $O(N \log N)$, Space: $O(N)$.

---

### Q2. Single-Threaded CPU
<a href="https://leetcode.com/problems/single-threaded-cpu/" target="_blank">LeetCode 1834</a> — **Medium**

**Target Skill:** Event arrival loop with processing-time priority queue.

**Core Reasoning:**
- Tasks `[enqueueTime, processingTime]`.
- Sort tasks by `enqueueTime`.
- `availableTasks` Min Heap ordered by: `processingTime` asc $\to$ `taskIndex` asc.
- Simulation loop at `time`:
  1. Add all tasks with `enqueueTime <= time` into `availableTasks`.
  2. If `availableTasks` is empty, advance `time` to next task's `enqueueTime`.
  3. Pop shortest task, append `taskIndex` to result, add `processingTime` to `time`.

**Why it belongs here:** Classic OS CPU scheduling algorithm.

**Complexity:** Time: $O(N \log N)$, Space: $O(N)$.

---

### Q3. Task Scheduler
<a href="https://leetcode.com/problems/task-scheduler/" target="_blank">LeetCode 621</a> — **Medium**

**Target Skill:** Frequency Max Heap with cooling period queue simulation.

**Core Reasoning:**
- Tasks must be executed with at least $N$ cooling units between identical task types.
- Max Heap stores frequencies of available tasks.
- In each cycle (size $N + 1$): pop most frequent tasks, execute, decrement count.
- Store cooling tasks in temporary list. Re-enqueue non-zero tasks into Max Heap.
- Count total time slots (including idle CPU slots).

**Why it belongs here:** **Canonical home for LC 621.** Demonstrates scheduling tasks with mandatory cooling intervals.

**Complexity:** Time: $O(N \log \Sigma)$ (where $\Sigma = 26$), Space: $O(1)$.

---

### Q4. Process Tasks Using Servers
<a href="https://leetcode.com/problems/process-tasks-using-servers/" target="_blank">LeetCode 1882</a> — **Medium**

**Target Skill:** Dual Min Heap state tracking (`availableServers` vs `busyServers`).

**Core Reasoning:**
- Servers `[weight, index]`. Tasks arrive at second `t` requiring `tasks[t]` processing time.
- `available` Min Heap ordered by `weight` asc $\to$ `index` asc.
- `busy` Min Heap ordered by `freeTime` asc $\to$ `weight` asc $\to$ `index` asc.
- For each task at time $t$: release all busy servers with `freeTime <= t`. Assign task to top available server. If none available, advance time to `busy.peek().freeTime`.

**Why it belongs here:** Advanced dual-heap server pool allocation simulation.

**Complexity:** Time: $O((M + N) \log N)$, Space: $O(N)$.

---

### Q5. Meeting Rooms III
<a href="https://leetcode.com/problems/meeting-rooms-iii/" target="_blank">LeetCode 2402</a> — **Hard**

**Target Skill:** Room delay simulation and usage frequency counting.

**Core Reasoning:**
- Sort meetings by start time.
- `unusedRooms` Min Heap ordered by room `number` asc.
- `usedRooms` Min Heap ordered by `endTime` asc $\to$ room `number` asc.
- For meeting `[start, end]`:
  - Release rooms with `endTime <= start` into `unusedRooms`.
  - If `unusedRooms` non-empty: assign smallest room index, end time = `end`.
  - Else: delay meeting! Pop earliest ending room `[earliestEnd, room]`. End time = `earliestEnd + (end - start)`. Re-offer to `usedRooms`.
- Count room frequencies, return room with max meetings.

**Why it belongs here:** Peak scheduling problem combining delayed task execution, multi-attribute tie breaking, and resource frequency tracking.

**Complexity:** Time: $O(M \log M + M \log N)$, Space: $O(N)$.

---

## ⚡ Mastery Checklist

- [ ] Can you set up dual heaps (`busy` vs `available`) for resource allocation problems?
- [ ] Why does `Single-Threaded CPU` advance `time` to the next task's `enqueueTime` when `availableTasks` is empty?
- [ ] How does `Meeting Rooms III` handle delayed meetings when all rooms are currently occupied?
- [ ] Can you implement multi-field Comparators (`time` asc $\to$ `weight` asc $\to$ `index` asc) without bug-prone logic?
