# Pattern 01: Recursion Fundamentals & Recursive State

> Recursion solves a problem by expressing it in terms of smaller instances of the same problem. A well-defined recursive algorithm requires a precise **Recursive State** definition, valid **Transitions**, and explicit **Base Cases** to prevent infinite execution stacks.

---

## Why This Pattern Exists

Before building complex backtracking trees or dynamic programming tables, you must master the fundamental mechanics of recursive function execution.

Every recursive function consists of three components:
1. **Recursive State:** The minimum set of parameters passed into the function that uniquely identifies a subproblem (e.g., `(index, remainingSum)` or `(n, k)`).
2. **Base Cases:** The boundary conditions where the subproblem can be answered directly without further recursive calls.
3. **Transitions:** The mathematical or logical step that breaks the current subproblem into one or more smaller subproblems.

```text
                  [ Original Problem: State S ]
                               │
                ┌──────────────┴──────────────┐
                ▼                             ▼
       [ Subproblem: State S1 ]      [ Subproblem: State S2 ]
                │                             │
                ▼                             ▼
            Base Case                     Base Case
```

---

## ☕ Standard Java Templates

### 1. Divide & Conquer Return-Value Recursion (Pow(x, n))
```java
public double myPow(double x, long n) {
    if (n == 0) return 1.0;
    if (n < 0) return 1.0 / myPow(x, -n);
    
    // Subproblem: compute x^(n/2) once
    double half = myPow(x, n / 2);
    
    if (n % 2 == 0) {
        return half * half;
    } else {
        return half * half * x;
    }
}
```

### 2. Expression Parsing Recursion (Different Ways to Add Parentheses)
```java
public List<Integer> diffWaysToCompute(String expression) {
    List<Integer> res = new ArrayList<>();
    
    for (int i = 0; i < expression.length(); i++) {
        char c = expression.charAt(i);
        if (c == '+' || c == '-' || c == '*') {
            // Divide at operator
            List<Integer> left = diffWaysToCompute(expression.substring(0, i));
            List<Integer> right = diffWaysToCompute(expression.substring(i + 1));
            
            // Combine all left and right subproblem results
            for (int l : left) {
                for (int r : right) {
                    if (c == '+') res.add(l + r);
                    else if (c == '-') res.add(l - r);
                    else if (c == '*') res.add(l * r);
                }
            }
        }
    }
    
    // Base Case: Expression is a single integer number
    if (res.isEmpty()) {
        res.add(Integer.parseInt(expression));
    }
    return res;
}
```

---

## 🔗 Cross-Section Bridge / Reference Only

> **Decode Ways (<a href="https://leetcode.com/problems/decode-ways/" target="_blank">LeetCode 91</a>):**
> Demonstrates top-down recursive decision making (`decode(i) = decode(i+1) + decode(i+2)`).
> ⚠️ **Canonical Ownership Note:** LC 91 belongs canonically to **Dynamic Programming**. It is referenced here strictly as a bridge showing when plain recursion with overlapping subproblems requires memoization / DP. It does NOT count toward Section 09's canonical question total.

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Decode Ways (LC 91)** | Housed canonically in **Dynamic Programming** (memoized / DP state transition). |
| **Subsets** | Requires generating combinations and backtracking state modification — belongs to **Pattern 02**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 1 |
| **Medium** | 3 |
| **Total Canonical Questions** | **4** *(+1 Bridge Reference)* |

---

## 🎯 Question Set (4 Canonical Questions)

### Q1. Fibonacci Number
<a href="https://leetcode.com/problems/fibonacci-number/" target="_blank">LeetCode 509</a> — **Easy**

**Target Skill:** Basic recursive state definition and base-case identification.

**Core Reasoning:**
- State: $F(N)$. Base cases: $F(0) = 0$, $F(1) = 1$.
- Transition: $F(N) = F(N-1) + F(N-2)$.

**Why it belongs here:** The fundamental entry problem for recursive state transitions and call stack visualization.

**Complexity:** Time: $O(2^N)$ naive recursion / $O(N)$ with memoization, Space: $O(N)$ call stack.

---

### Q2. Pow(x, n)
<a href="https://leetcode.com/problems/powx-n/" target="_blank">LeetCode 50</a> — **Medium**

**Target Skill:** Logarithmic divide-and-conquer recursion state reduction.

**Core Reasoning:**
- Naive recursion multiplies $x$ by itself $N$ times ($O(N)$).
- **Divide & Conquer:** $x^N = (x^{N/2})^2$ for even $N$, $x \times (x^{N/2})^2$ for odd $N$.
- Compute subproblem $x^{N/2}$ once recursively, reducing state from $N \to N/2$.

**Why it belongs here:** Demonstrates reducing recursive tree depth from $O(N)$ to $O(\log N)$.

**Complexity:** Time: $O(\log N)$, Space: $O(\log N)$ call stack.

---

### Q3. K-th Symbol in Grammar
<a href="https://leetcode.com/problems/k-th-symbol-in-grammar/" target="_blank">LeetCode 779</a> — **Medium**

**Target Skill:** Parent-child recursive index state derivation.

**Core Reasoning:**
- Row $N$ has $2^{N-1}$ elements. Each symbol $0 \to 01$ and $1 \to 10$.
- The first half of Row $N$ is identical to Row $N-1$. The second half is the bitwise complement of Row $N-1$.
- Base case: `n == 1` returns `0`.
- If $K \le 2^{N-2}$ (first half): return `kthGrammar(n - 1, k)`.
- If $K > 2^{N-2}$ (second half): return `1 ^ kthGrammar(n - 1, k - 2^(n-2))`.

**Why it belongs here:** Pure recursive state transformation where the answer to subproblem $(N, K)$ depends directly on $(N-1, K')$.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

### Q4. Different Ways to Add Parentheses
<a href="https://leetcode.com/problems/different-ways-to-add-parentheses/" target="_blank">LeetCode 241</a> — **Medium**

**Target Skill:** Divide-and-conquer sub-expression combinatorial evaluation.

**Core Reasoning:**
- Iterate through string `expression`. Whenever an operator (`+`, `-`, `*`) is found at index $i$, split string into `leftSub = expression[0...i-1]` and `rightSub = expression[i+1...end]`.
- Recursively evaluate `diffWaysToCompute(leftSub)` and `diffWaysToCompute(rightSub)`.
- Combine all pair results from left and right lists.

**Why it belongs here:** Demonstrates return-value recursion where subproblem results are combined combinatorially.

**Complexity:** Time: $O(4^N / \sqrt{N})$ (Catalan number growth), Space: $O(4^N / \sqrt{N})$.

---

## ⚡ Mastery Checklist

- [ ] Can you define the **Recursive State** and **Base Case** for any given recursive function before writing code?
- [ ] Why does `Pow(x, n)` take $O(\log N)$ time when storing `half = myPow(x, n/2)` vs $O(N)$ without storing `half`?
- [ ] How do you deduce parent-child symbol relationships in `K-th Symbol in Grammar`?
- [ ] Why does `Decode Ways (LC 91)` serve as the bridge between recursion and Dynamic Programming?
