# Pattern 05: Composite Hashing

> A HashMap or HashSet is one component of a larger algorithm or data structure.
> The challenge is not whether to use a HashMap — it is making **multiple structures work together coherently**, maintaining consistency across all operations.

---

## Why This Pattern Exists

In all previous patterns, the HashMap was the central structure: you built it, queried it, and it gave you the answer.

In composite hashing, the HashMap is a **supporting component** inside a more complex design. The questions in this pattern require you to:

1. Identify **why the HashMap alone is insufficient** for the problem's requirements.
2. Identify **what other structure** solves what the HashMap cannot.
3. **Combine the structures** while preserving correctness across all operations.

This is the pattern where data structure design thinking begins. The problems here are not "apply hashing" — they are "design a system that uses hashing as one part."

---

## When to Use This Pattern

Use this pattern when:

- You need $O(1)$ lookup **and** $O(1)$ random access simultaneously.
- You need to track state across multiple independent constraint dimensions simultaneously.
- A HashMap stores one type of information that must be synchronized with another structure that stores a different type.
- The problem asks you to design a class with multiple operations that must each run in constant time.
- The problem involves simultaneous multi-structure constraints (rows, columns, and boxes simultaneously).

**Recognition Triggers:**
```text
"design a data structure with insert, delete, and getRandom all in O(1)"
  ──► HashMap gives O(1) lookup, List gives O(1) random access → combine them

"validate a Sudoku board"
  ──► Multiple simultaneous HashSets: one per row, one per column, one per 3×3 box

"find all concatenated-word substrings"
  ──► Sliding window over words + frequency HashMap tracking window word counts

"clone a linked list with random pointers"
  ──► HashMap maps old nodes to new nodes for O(1) cross-reference
```

---

## The Core Mental Model

```text
Question: "What does this HashMap alone NOT provide that the problem requires?"

Identify the gap:
  HashMap gives O(1) get/put     BUT not O(1) random access by position
  HashMap gives O(1) lookup      BUT not cross-structure constraint checking
  HashMap gives O(1) membership  BUT not pointer referencing between new/old objects

Fill the gap with the appropriate second structure:
  ArrayList  → O(1) indexing and random access by position
  Multiple HashSets → simultaneous constraint tracking
  Another HashMap → bidirectional mapping or cross-reference

Maintain consistency:
  Every operation that modifies one structure must update the other
```

---

## ☕ Standard Java Templates

### 1. HashMap + ArrayList (Insert Delete GetRandom O(1))
```java
class RandomizedSet {
    Map<Integer, Integer> map; // value → index in list
    List<Integer> list;        // list[index] = value
    Random rand = new Random();

    public boolean insert(int val) {
        if (map.containsKey(val)) return false;
        list.add(val);
        map.put(val, list.size() - 1);
        return true;
    }

    public boolean remove(int val) {
        if (!map.containsKey(val)) return false;
        int idx = map.get(val);
        int last = list.get(list.size() - 1);
        list.set(idx, last);   // swap target with last element
        map.put(last, idx);    // update last element's index in map
        list.remove(list.size() - 1);
        map.remove(val);
        return true;
    }

    public int getRandom() {
        return list.get(rand.nextInt(list.size()));
    }
}
```

### 2. Multiple HashSets (Valid Sudoku)
```java
public boolean isValidSudoku(char[][] board) {
    Set<String> seen = new HashSet<>();
    for (int r = 0; r < 9; r++) {
        for (int c = 0; c < 9; c++) {
            char ch = board[r][c];
            if (ch == '.') continue;
            // Encode three distinct constraints as strings
            if (!seen.add(ch + " in row " + r)) return false;
            if (!seen.add(ch + " in col " + c)) return false;
            if (!seen.add(ch + " in box " + (r/3) + "-" + (c/3))) return false;
        }
    }
    return true;
}
```

### 3. HashMap for Node Cross-Reference (Copy List with Random Pointer)
```java
public Node copyRandomList(Node head) {
    Map<Node, Node> map = new HashMap<>(); // old node → new node
    Node curr = head;
    while (curr != null) {
        map.put(curr, new Node(curr.val));
        curr = curr.next;
    }
    curr = head;
    while (curr != null) {
        map.get(curr).next   = map.get(curr.next);
        map.get(curr).random = map.get(curr.random);
        curr = curr.next;
    }
    return map.get(head);
}
```

---

## 🔍 Step-by-Step Dry Run

**Insert Delete GetRandom O(1) — Remove Operation**

State: `list = [1, 2, 3, 4]`, `map = {1:0, 2:1, 3:2, 4:3}`

Remove `val = 2` (at index 1):

| Step | Operation | `list` state | `map` state |
|------|-----------|--------------|-------------|
| 1 | `idx = map.get(2)` = 1 | — | — |
| 2 | `last = list.get(3)` = 4 | — | — |
| 3 | `list.set(1, 4)` → swap | `[1, 4, 3, 4]` | — |
| 4 | `map.put(4, 1)` → update last | — | `{1:0, 2:1, 3:2, 4:1}` |
| 5 | `list.remove(3)` → remove tail | `[1, 4, 3]` | — |
| 6 | `map.remove(2)` → remove target | — | `{1:0, 4:1, 3:2}` |

Result: `val = 2` removed. All indices consistent. `getRandom()` picks from `[1, 4, 3]` uniformly.

---

## 🛑 Pattern Boundary: What Does NOT Belong Here

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **LRU Cache** | Doubly Linked List + HashMap for ordered eviction. Dominant pattern: **Linked List / Cache Design**. |
| **Time Based Key-Value Store** | Binary search on timestamp arrays. Dominant pattern: **Binary Search / Design**. |
| **Subarrays with K Different Integers** | HashMap + Sliding Window — dominant pattern is Sliding Window, not composite structure design. |
| **Find All Anagrams in a String** | Frequency array + Sliding Window. Dominant pattern: **String Sliding Window**. |

> **Rule:** Don't put every problem that uses a HashMap into composite hashing. The defining feature is that the HashMap is one component of a deliberately designed multi-structure system.

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Medium** | 3 |
| **Hard** | 1 |
| **Total** | **4** |

---

## 🎯 Question Set (4 Questions)

### Q1. Insert Delete GetRandom O(1)
<a href="https://leetcode.com/problems/insert-delete-getrandom-o1/" target="_blank">LeetCode 380</a> — **Medium**

**Target Skill:** HashMap + ArrayList — combining lookup speed with random access.

**Core Reasoning:**
- HashMap alone: $O(1)$ insert/delete/lookup but **cannot get random in $O(1)$** (no positional index).
- ArrayList alone: $O(1)$ random access by index but **$O(N)$ lookup and deletion**.
- **Combined:** HashMap stores `value → list index`. ArrayList stores values by position.
- **The critical insight for O(1) remove:** Swap the target element with the last element, then remove the last. Update both structures consistently.

**Why it belongs here:** This is the canonical composite hashing design problem. It directly teaches the "HashMap fills one gap, ArrayList fills another" reasoning and requires maintaining **bidirectional consistency** across every operation.

---

### Q2. Copy List with Random Pointer
<a href="https://leetcode.com/problems/copy-list-with-random-pointer/" target="_blank">LeetCode 138</a> — **Medium**

**Target Skill:** HashMap as a cross-reference registry between old and new objects.

**Core Reasoning:**
- The problem: create a deep copy of a linked list where each node has a `.next` and a `.random` pointer.
- The challenge: `.random` can point to any node in the list — you cannot set it during a single-pass creation without first having all new nodes created.
- **HashMap role:** Map every old node to its new copy in Pass 1. Set `.next` and `.random` pointers using the map in Pass 2.
- **The question:** *"Given an old node reference, which new node does it correspond to?"*

**Why it belongs here:** The HashMap is not counting or doing complement lookups — it is a **node registry** enabling $O(1)$ cross-reference between object graphs. This is a fundamentally different use of HashMap: as a structural bridge between two parallel data structures.

---

### Q3. Valid Sudoku
<a href="https://leetcode.com/problems/valid-sudoku/" target="_blank">LeetCode 36</a> — **Medium**

**Target Skill:** Multiple simultaneous HashSets for multi-constraint state tracking.

**Core Reasoning:**
- You must simultaneously validate three independent constraints: no digit repeats in any row, any column, or any 3×3 sub-box.
- A single HashSet cannot represent all three constraints without conflation.
- **Strategy:** Encode each constraint as a unique string key (e.g., `"5 in row 2"`, `"5 in col 4"`, `"5 in box 0-1"`) and check insertion into a single shared HashSet.
- If any key was already inserted, the board is invalid.

**Why it belongs here:** The HashSet here is not used for membership detection on a single dimension — it simultaneously tracks state across **multiple parallel constraint spaces**. This introduces the idea of encoding multi-dimensional constraints into hashable keys.

---

### Q4. Substring with Concatenation of All Words
<a href="https://leetcode.com/problems/substring-with-concatenation-of-all-words/" target="_blank">LeetCode 30</a> — **Hard**

**Target Skill:** Sliding window over fixed-length word-blocks + frequency HashMap + comparison HashMap.

**Core Reasoning:**
- You have a string `s` and an array of `words`. Find all starting indices where a concatenation of all words (in any order) appears.
- Each word has the same length `L`. The total window size is `numWords × L`.
- **Strategy:** Maintain a reference frequency map of all words. Slide a window of word-blocks. Track the current window's word frequency with a second HashMap. Compare the two maps at each step.
- When a word exceeds its allowed count, shrink the window.

**Why it belongs here:** This problem requires:
1. A reference frequency HashMap (Pattern 02 idea).
2. A window frequency HashMap updated as the window slides.
3. Coordination between the two HashMaps to determine window validity.

It is composite because the algorithm depends on **two HashMap states in synchronized coordination with a sliding window**. Neither the HashMap alone nor the sliding window alone is sufficient.

**Cross-pattern note:** The sliding window traversal idea comes from Arrays Pattern 06 / String Pattern 05. What makes this problem composite is the dual-HashMap coordination requirement.

---

## 🏆 Mastery Criteria

You have mastered Pattern 05 when you can, **without being told the pattern name**:

- [ ] Identify the gap that a HashMap alone cannot fill for a given problem.
- [ ] Name the second structure that fills that gap and explain why.
- [ ] Implement Insert Delete GetRandom with O(1) remove using the swap-with-last technique.
- [ ] Explain why the swap-with-last technique maintains correctness.
- [ ] Design Copy List with Random Pointer using a two-pass approach without recursion.
- [ ] Encode multi-dimensional constraints as unique hashable keys for Valid Sudoku.
- [ ] Describe the two-HashMap coordination for Substring with Concatenation of All Words.
- [ ] State time and space complexity for all four problems.
- [ ] Explain why LRU Cache, Time Based Key-Value Store, and Subarrays with K Different Integers do NOT belong here.

---

## ➡️ Next Step

You have completed the Hashing & Hash Maps module.

Return to **[Module 03 README](./README.md)** for a full curriculum overview, the First-Seen / Last-Seen / Index State technique guide, and cross-pattern relationships.
