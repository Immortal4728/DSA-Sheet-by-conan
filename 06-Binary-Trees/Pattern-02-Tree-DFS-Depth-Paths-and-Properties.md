# Pattern 02: Tree DFS: Depth, Paths & Properties

> Tree DFS is fundamentally about information flow: what a parent sends down to its children (top-down), and what children return back up to their parent (bottom-up).

---

## Why This Pattern Exists

Basic traversals print or collect values in fixed order. Real interview problems require deriving properties (depth, balance, path sums, diameters) by combining state across subtrees.

The core DFS paradigm follows recursive decomposition:
```java
LeftResult left = solve(node.left);
RightResult right = solve(node.right);
return combine(left, right, node);
```

This pattern progresses through 5 levels of information flow complexity:
1. **Simple Value Return:** Computing depth or height (`Min Depth`, `Balanced Tree`).
2. **Top-Down Path Passing:** Passing running sum or path history down to leaf nodes (`Path Sum`, `Binary Tree Paths`).
3. **Backtracking Path State:** Building and popping path lists during root-to-leaf exploration (`Path Sum II`).
4. **Bottom-Up Global Answer Updating:** Returning subtree depth while updating a global diameter or univalue path variable (`Diameter of Binary Tree`).
5. **Prefix Sum Path Tracking:** Using HashMaps on tree paths to count arbitrary sub-paths (`Path Sum III`).

---

## ☕ Standard Java Templates

### 1. Global Variable Update (Diameter / Univalue Pattern)
```java
int maxPath = 0;

public int calculate(TreeNode root) {
    dfs(root);
    return maxPath;
}

private int dfs(TreeNode node) {
    if (node == null) return 0;
    
    int left = dfs(node.left);
    int right = dfs(node.right);
    
    // Update global maximum (combining left + right + current)
    maxPath = Math.max(maxPath, left + right);
    
    // Return single-leg depth to parent
    return 1 + Math.max(left, right);
}
```

### 2. Path Sum III Prefix Map Pattern
```java
public int pathSum(TreeNode root, int targetSum) {
    Map<Long, Integer> prefixMap = new HashMap<>();
    prefixMap.put(0L, 1); // Base prefix
    return dfs(root, 0L, targetSum, prefixMap);
}

private int dfs(TreeNode node, long currSum, int target, Map<Long, Integer> map) {
    if (node == null) return 0;
    
    currSum += node.val;
    int count = map.getOrDefault(currSum - target, 0);
    
    map.put(currSum, map.getOrDefault(currSum, 0) + 1);
    
    count += dfs(node.left, currSum, target, map);
    count += dfs(node.right, currSum, target, map);
    
    // Backtrack prefix map for other branches
    map.put(currSum, map.get(currSum) - 1);
    
    return count;
}
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Diameter of Binary Tree (LC 543)** | Belongs **exclusively to Pattern 02** as the entry problem for subtree depth aggregation. (Not duplicated in Tree DP). |
| **Binary Tree Maximum Path Sum (LC 124)** | Handles negative values and state optimization choices — belongs to **Tree DP (Pattern 08)**. |
| **Lowest Common Ancestor** | Focuses on node relationship identification — belongs to **LCA (Pattern 05)**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 5 |
| **Medium** | 4 |
| **Total** | **9** |

---

## 🎯 Question Set (9 Questions)

### Q1. Minimum Depth of Binary Tree
<a href="https://leetcode.com/problems/minimum-depth-of-binary-tree/" target="_blank">LeetCode 111</a> — **Easy**

**Target Skill:** Handling asymmetric null branch edge cases in leaf depth calculations.

**Core Reasoning:**
- Minimum depth is number of nodes along shortest path from root down to nearest **leaf node** (node with no children).
- Edge Case: If a node has only one child (e.g., `left == null`, `right != null`), you must recurse into the existing child! Do not return `1` for the null child.
- If `left == null`, return `1 + minDepth(right)`. If `right == null`, return `1 + minDepth(left)`.

**Why it belongs here:** Teaches precise leaf boundary checking vs standard maximum depth.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

### Q2. Balanced Binary Tree
<a href="https://leetcode.com/problems/balanced-binary-tree/" target="_blank">LeetCode 110</a> — **Easy**

**Target Skill:** Early-exit height computation (bottom-up boolean depth check).

**Core Reasoning:**
- A tree is height-balanced if depth of two subtrees of every node never differs by more than 1.
- Top-down $O(N^2)$ checks height at every node repeatedly.
- **Bottom-up $O(N)$ approach:** Helper function returns height, or `-1` if unbalanced. If `left == -1 || right == -1 || Math.abs(left - right) > 1`, return `-1`.

**Why it belongs here:** Demonstrates how bottom-up return values pass boolean balance status and integer height simultaneously.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

### Q3. Path Sum
<a href="https://leetcode.com/problems/path-sum/" target="_blank">LeetCode 112</a> — **Easy**

**Target Skill:** Top-down target accumulation to leaf nodes.

**Core Reasoning:**
- Determine if tree has a root-to-leaf path such that sum of values equals `targetSum`.
- At leaf node (`node.left == null && node.right == null`), check if `node.val == targetSum`.
- Otherwise, recurse `hasPathSum(node.left, targetSum - node.val) || hasPathSum(node.right, targetSum - node.val)`.

**Why it belongs here:** Entry problem for top-down root-to-leaf sum propagation.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

### Q4. Path Sum II
<a href="https://leetcode.com/problems/path-sum-ii/" target="_blank">LeetCode 113</a> — **Medium**

**Target Skill:** Backtracking path list maintenance during DFS.

**Core Reasoning:**
- Find all root-to-leaf paths where sum equals `targetSum`.
- Maintain `currentPath` list. Add `node.val` when entering.
- If at leaf and sum matches, append a copy `new ArrayList<>(currentPath)` to result list.
- Recurse left and right, then **backtrack:** `currentPath.remove(currentPath.size() - 1)`.

**Why it belongs here:** Integrates DFS path traversal with state backtracking to prevent excessive memory allocation.

**Complexity:** Time: $O(N^2)$ worst case (path copying), Space: $O(H)$.

---

### Q5. Sum Root to Leaf Numbers
<a href="https://leetcode.com/problems/sum-root-to-leaf-numbers/" target="_blank">LeetCode 129</a> — **Medium**

**Target Skill:** Numerical accumulator passing down root-to-leaf paths.

**Core Reasoning:**
- Each node value is a digit ($0-9$). A root-to-leaf path forms a number.
- Pass accumulated `currentNum = currentNum * 10 + node.val` down to children.
- At leaf, return `currentNum`. Non-leaf returns `dfs(left, currentNum) + dfs(right, currentNum)`.

**Why it belongs here:** Demonstrates passing arithmetic accumulated state top-down.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

### Q6. Binary Tree Paths
<a href="https://leetcode.com/problems/binary-tree-paths/" target="_blank">LeetCode 257</a> — **Easy**

**Target Skill:** String path construction and leaf emission.

**Core Reasoning:**
- Return all root-to-leaf paths in string format `"1->2->5"`.
- Pass current path string `path + node.val` to children.
- At leaf node, add completed string to result list.

**Why it belongs here:** Fundamental root-to-leaf string path generation.

**Complexity:** Time: $O(N \cdot H)$, Space: $O(H)$.

---

### Q7. Diameter of Binary Tree
<a href="https://leetcode.com/problems/diameter-of-binary-tree/" target="_blank">LeetCode 543</a> — **Easy**

**Target Skill:** Subtree depth return with global variable maximum updating.

**Core Reasoning:**
- Diameter is the length of the longest path between any two nodes in a tree (path may or may not pass through root).
- For any node, longest path passing through it as highest point is `leftDepth + rightDepth`.
- Helper function `maxDepth(node)` returns height (`1 + Math.max(left, right)`), while updating global `maxDiameter = Math.max(maxDiameter, left + right)`.

**Why it belongs here:** **Canonical home for LC 543.** Quintessential pattern for returning single-branch height up to parent while combining both branches for a global property.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

### Q8. Longest Univalue Path
<a href="https://leetcode.com/problems/longest-univalue-path/" target="_blank">LeetCode 687</a> — **Medium**

**Target Skill:** Conditional path extension based on node values.

**Core Reasoning:**
- Longest path where every node in the path has the same value.
- Helper `arrowLength(node)` returns longest univalue path starting from `node` extending downwards.
- If `node.left` has same value, `leftPath = leftArrow + 1`, else `0`.
- If `node.right` has same value, `rightPath = rightArrow + 1`, else `0`.
- Update global `maxLen = Math.max(maxLen, leftPath + rightPath)`. Return `Math.max(leftPath, rightPath)`.

**Why it belongs here:** Extends diameter reasoning by conditionally resetting path lengths when child values differ from parent.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

### Q9. Path Sum III
<a href="https://leetcode.com/problems/path-sum-iii/" target="_blank">LeetCode 437</a> — **Medium**

**Target Skill:** Prefix sum hash map tracking on tree paths (arbitrary starting and ending nodes).

**Core Reasoning:**
- Path does not need to start at root or end at leaf, but must go downwards (parent to child).
- A simple DFS at every node takes $O(N^2)$.
- **$O(N)$ Prefix Sum HashMap Approach:** Maintain running prefix sum `currSum` from root down current branch.
- Number of valid paths ending at current node = `map.getOrDefault(currSum - targetSum, 0)`.
- Put `currSum` in map, recurse left and right, then **backtrack** (`map.put(currSum, map.get(currSum) - 1)`).

**Why it belongs here:** Teaches that not every tree-path problem is a simple root-to-leaf DFS. Bridges Prefix Sum (Module 03) onto tree hierarchies.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

## ⚡ Mastery Checklist

- [ ] Can you explain why `Min Depth` requires special handling for single-child nodes?
- [ ] Do you know how to write `Balanced Binary Tree` in $O(N)$ bottom-up time instead of $O(N^2)$ top-down?
- [ ] Can you articulate why `Diameter of Binary Tree` returns a single leg depth but updates global diameter using both legs?
- [ ] Can you implement `Path Sum III` using a prefix sum HashMap with proper backtracking?
