# Pattern 04: Partitioning & Recursive Construction

> Partitioning algorithms split a sequence into valid contiguous segments (palindromes, IP octets, math expressions). Construction algorithms build valid structural outputs character-by-character under strict validation rules.

---

## Why This Pattern Exists

Unlike picking elements from arbitrary indices:
- **Partitioning** requires making cut decisions along contiguous boundaries of a string or array: `str[start ... i]`.
- **Recursive Construction** builds outputs incrementally while enforcing prefix validity rules at every recursive step (e.g., maintaining `open < N` for parentheses).

```text
Input String: "aab"
                 /                       \
        Cut "a" (Valid Palindrome)   Cut "aa" (Valid Palindrome)
              │                            │
        Remaining: "ab"              Remaining: "b"
         /          \                      │
    Cut "a"        Cut "ab"            Cut "b" (Valid)
     (Valid)      (Invalid!)               │
       │                               Result: ["aa", "b"]
    Cut "b" (Valid)
       │
  Result: ["a", "a", "b"]
```

---

## ☕ Standard Java Templates

### String Partitioning Pattern (Palindrome Partitioning / IP Addresses)
```java
private void backtrack(String s, int start, List<String> current, List<List<String>> result) {
    if (start == s.length()) {
        result.add(new ArrayList<>(current)); // Valid partition complete
        return;
    }
    
    for (int end = start; end < s.length(); end++) {
        String segment = s.substring(start, end + 1);
        
        if (isValidSegment(segment)) {     // 1. VALIDITY CHECK
            current.add(segment);          // 2. CHOOSE
            backtrack(s, end + 1, current, result); // 3. RECURSE
            current.remove(current.size() - 1); // 4. UNDO
        }
    }
}
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Word Search (LC 79)** | Traverses 2D grid cells — belongs to **Grid Backtracking (Pattern 05)**. |
| **Subsets II** | Selects non-contiguous element subsets — belongs to **Pattern 02**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Medium** | 4 |
| **Hard** | 1 |
| **Total Canonical Questions** | **5** |

---

## 🎯 Question Set (5 Canonical Questions)

### Q1. Generate Parentheses
<a href="https://leetcode.com/problems/generate-parentheses/" target="_blank">LeetCode 22</a> — **Medium**

**Target Skill:** Structural prefix validity balance tracking.

**Core Reasoning:**
- Generate all combinations of $N$ pairs of valid parentheses.
- Maintain `open` count and `close` count.
- Branch 1: If `open < N`, append `'('` and recurse `backtrack(open + 1, close)`.
- Branch 2: If `close < open`, append `')'` and recurse `backtrack(open, close + 1)`.
- Base case: `current.length() == 2 * N`.

**Why it belongs here:** Canonical structural construction with prefix validity pruning.

**Complexity:** Time: $O(4^N / \sqrt{N})$ (Catalan number $C_N$), Space: $O(N)$.

---

### Q2. Palindrome Partitioning
<a href="https://leetcode.com/problems/palindrome-partitioning/" target="_blank">LeetCode 131</a> — **Medium**

**Target Skill:** Contiguous string partitioning with palindrome validation.

**Core Reasoning:**
- Partition string $S$ such that every substring in the partition is a palindrome.
- Loop `end` from `start` to $S.length() - 1$.
- If `isPalindrome(s, start, end)` is true: add `s.substring(start, end + 1)`, recurse `backtrack(end + 1)`, then backtrack.

**Why it belongs here:** Canonical contiguous string partitioning algorithm.

**Complexity:** Time: $O(N \cdot 2^N)$, Space: $O(N)$.

---

### Q3. Restore IP Addresses
<a href="https://leetcode.com/problems/restore-ip-addresses/" target="_blank">LeetCode 93</a> — **Medium**

**Target Skill:** Bounded length segment validation and depth-limited partitioning.

**Core Reasoning:**
- Split string into 4 valid IP octets ($0-255$, no leading zero unless single `'0'`).
- Pruning: String length must be between 4 and 12. Segment length is at most 3 digits.
- Track `segmentCount`. At `segmentCount == 4`, if `start == s.length()`, format IP string and save to result.

**Why it belongs here:** Teaches segment length bounds and string segment numerical validation.

**Complexity:** Time: $O(3^4) = O(1)$ fixed bound, Space: $O(1)$.

---

### Q4. Letter Tile Possibilities
<a href="https://leetcode.com/problems/letter-tile-possibilities/" target="_blank">LeetCode 1079</a> — **Medium**

**Target Skill:** Frequency map backtracking for distinct sequence construction of all lengths.

**Core Reasoning:**
- Given tile letters string `tiles`. Return number of possible non-empty sequences.
- Build frequency array `int[] count = new int[26]`.
- Backtracking function returns count of valid sequences:
  - Loop `i` from $0$ to $25$: if `count[i] > 0`, decrement `count[i]`, add `1 + dfs(count)` to total, then restore `count[i]++`.

**Why it belongs here:** Demonstrates frequency-based sequence construction without generating duplicate branches.

**Complexity:** Time: $O(\text{unique sequences})$, Space: $O(\Sigma)$.

---

### Q5. Expression Add Operators
<a href="https://leetcode.com/problems/expression-add-operators/" target="_blank">LeetCode 282</a> — **Hard**

**Target Skill:** Dynamic mathematical expression construction with multiplication operator precedence tracking.

**Core Reasoning:**
- Insert `+`, `-`, or `*` between digits of string `num` to evaluate to `target`.
- Recurse with state: `(idx, path, eval, prevOperand)`.
- On `+`: `eval + curr`, `prevOperand = curr`.
- On `-`: `eval - curr`, `prevOperand = -curr`.
- On `*` (Operator Precedence!): `eval - prevOperand + (prevOperand * curr)`, `prevOperand = prevOperand * curr`.
- Handle leading zero check: if segment starts with `'0'` and length $> 1$, break segment loop.

**Why it belongs here:** Advanced construction problem requiring complex arithmetic state tracking.

**Complexity:** Time: $O(4^N)$, Space: $O(N)$.

---

## ⚡ Mastery Checklist

- [ ] How do prefix validity checks (`open < N` and `close < open`) prevent invalid parenthesis strings in `Generate Parentheses`?
- [ ] Why does `Palindrome Partitioning` use `end + 1` as the next `start` index?
- [ ] Can you state the 3 rules for valid IP address octets in `Restore IP Addresses`?
- [ ] How does `Expression Add Operators` handle operator precedence (`*`) recursively without converting the expression to RPN?
