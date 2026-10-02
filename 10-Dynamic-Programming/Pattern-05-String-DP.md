# Pattern 05: String DP

> String DP aligns two character sequences $S_1$ and $S_2$ using a 2D matrix state `dp[i][j]`, representing the optimal cost, alignment length, or combination count for prefixes $S_1[0 \dots i-1]$ and $S_2[0 \dots j-1]$.

---

## Why This Pattern Exists

Comparing two strings character-by-character from left to right generates branching decisions at index pairs $(i, j)$:
- **Characters Match ($S_1[i-1] == S_2[j-1]$):** Both pointers advance diagonally $\to (i-1, j-1)$.
- **Characters Mismatch:** We must evaluate inserting, deleting, or skipping characters in either string $\to$ state transitions from $(i-1, j)$, $(i, j-1)$, or $(i-1, j-1)$.

```text
                     s2[j-1] (Char from String 2)
                        │
                        ▼
s1[i-1] ────────►  dp[i][j]
(Char S1)       ▲       ▲       ▲
                │       │       │
             Insert   Delete  Replace / Match
             (i, j-1) (i-1, j) (i-1, j-1)
```

---

## ☕ Standard Java Templates

### 1. Longest Common Subsequence (LC 1143)
```java
public int longestCommonSubsequence(String text1, String text2) {
    int m = text1.length(), n = text2.length();
    int[][] dp = new int[m + 1][n + 1];
    
    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (text1.charAt(i - 1) == text2.charAt(j - 1)) {
                dp[i][j] = 1 + dp[i - 1][j - 1]; // Character match!
            } else {
                dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]); // Mismatch
            }
        }
    }
    return dp[m][n];
}
```

### 2. Edit Distance (LC 72)
```java
public int minDistance(String word1, String word2) {
    int m = word1.length(), n = word2.length();
    int[][] dp = new int[m + 1][n + 1];
    
    for (int i = 0; i <= m; i++) dp[i][0] = i; // Delete all in word1
    for (int j = 0; j <= n; j++) dp[0][j] = j; // Insert all into word1
    
    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (word1.charAt(i - 1) == word2.charAt(j - 1)) {
                dp[i][j] = dp[i - 1][j - 1]; // No operation needed
            } else {
                dp[i][j] = 1 + Math.min(dp[i - 1][j - 1], // Replace
                               Math.min(dp[i - 1][j],     // Delete
                                        dp[i][j - 1]));    // Insert
            }
        }
    }
    return dp[m][n];
}
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Word Search II** | Grid backtracking with Trie prefix pruning — belongs to **Recursion & Backtracking (Module 09)**. |
| **Decode Ways** | 1D string decoding transition — belongs to **DP Fundamentals (Pattern 01)**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Medium** | 2 |
| **Hard** | 2 |
| **Total Canonical Questions** | **4** |

---

## 🎯 Question Set (4 Canonical Questions)

### Q1. Longest Common Subsequence
<a href="https://leetcode.com/problems/longest-common-subsequence/" target="_blank">LeetCode 1143</a> — **Medium**

**Target Skill:** Dual-string 2D state alignment matrix.

**Core Reasoning:**
- Find length of longest subsequence present in both `text1` and `text2`.
- `dp[i][j]` = LCS of `text1[0...i-1]` and `text2[0...j-1]`.
- If match: `1 + dp[i-1][j-1]`. Else: `max(dp[i-1][j], dp[i][j-1])`.

**Why it belongs here:** Canonical foundation for 2-string DP alignment.

**Complexity:** Time: $O(M \cdot N)$, Space: $O(M \cdot N)$ or $O(N)$.

---

### Q2. Edit Distance
<a href="https://leetcode.com/problems/edit-distance/" target="_blank">LeetCode 72</a> — **Hard**

**Target Skill:** Multi-operation transformation cost minimization (Insert, Delete, Replace).

**Core Reasoning:**
- Minimum operations to convert `word1` to `word2`. Operations: Insert, Delete, Replace.
- Base cases: `dp[i][0] = i` (deletions), `dp[0][j] = j` (insertions).
- If match: `dp[i-1][j-1]`. Else: `1 + min(replace, delete, insert)`.

**Why it belongs here:** Quintessential 2-string transformation DP problem.

**Complexity:** Time: $O(M \cdot N)$, Space: $O(M \cdot N)$.

---

### Q3. Distinct Subsequences
<a href="https://leetcode.com/problems/distinct-subsequences/" target="_blank">LeetCode 115</a> — **Hard**

**Target Skill:** 2-string subsequence occurrence counting.

**Core Reasoning:**
- Count number of distinct subsequences of string $S$ that equal string $T$.
- `dp[i][j]` = number of ways `s[0...i-1]` can form `t[0...j-1]`. Base case: `dp[i][0] = 1`.
- If `s[i-1] == t[j-1]`: `dp[i][j] = dp[i-1][j-1] + dp[i-1][j]` (Use `s[i-1]` + Skip `s[i-1]`).
- Else: `dp[i][j] = dp[i-1][j]` (Must skip `s[i-1]`).

**Why it belongs here:** Teaches subproblem combination counting over 2D string matrices.

**Complexity:** Time: $O(M \cdot N)$, Space: $O(M \cdot N)$.

---

### Q4. Interleaving String
<a href="https://leetcode.com/problems/interleaving-string/" target="_blank">LeetCode 97</a> — **Medium**

**Target Skill:** Dual-source string prefix boolean verification.

**Core Reasoning:**
- Determine if $S_3$ is formed by interleaving $S_1$ and $S_2$. Length constraint: $|S_1| + |S_2| == |S_3|$.
- Boolean `dp[i][j]`: true if $S_3[0 \dots i+j-1]$ is formed by interleaving $S_1[0 \dots i-1]$ and $S_2[0 \dots j-1]$.
- `dp[i][j] = (dp[i-1][j] && s1[i-1] == s3[i+j-1]) || (dp[i][j-1] && s2[j-1] == s3[i+j-1])`.

**Why it belongs here:** Demonstrates boolean state validation matching two strings against a target stream.

**Complexity:** Time: $O(M \cdot N)$, Space: $O(M \cdot N)$.

---

## ⚡ Mastery Checklist

- [ ] Can you set up a 2D matrix `dp[m+1][n+1]` with 1-based indexing for 0-based string characters?
- [ ] Why does `Edit Distance (LC 72)` check 3 previous states (`dp[i-1][j-1]`, `dp[i-1][j]`, `dp[i][j-1]`) on character mismatch?
- [ ] How does `Distinct Subsequences (LC 115)` count matching options by adding `dp[i-1][j-1]` (use char) and `dp[i-1][j]` (skip char)?
- [ ] Can you optimize `LCS` space from $O(M \cdot N)$ to $O(N)$ using two row arrays?
