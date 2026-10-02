# Pattern 08: Tree DP & Subtree State

> Tree Dynamic Programming computes state tuples for subtrees bottom-up. Each node receives state vectors from its left and right children, computes its own state, and returns it to its parent while updating a global answer.

---

## Why This Pattern Exists

Basic DFS returns a single primitive value (like height or boolean balance).

Tree DP is required when:
- A node must make optimal decisions based on multiple structural choices (e.g., *Should I rob this node or skip it?*).
- A node must pass state tuples (e.g., `[HAS_CAMERA, COVERED, PLEASE_COVER]`) to its parent.
- The path answer passing through a node combines left and right branches, but the value returned to the parent can only extend ONE branch upward.

### The Fundamental Distinction: Local State vs Global Answer

$$\text{Single-Leg Return to Parent} \neq \text{Full Subtree Path Combination}$$

For example, in **Binary Tree Maximum Path Sum (LC 124)**:
- **Full Path at Current Node:** `node.val + leftMax + rightMax` (Updates global max).
- **Single Leg Returned to Parent:** `node.val + Math.max(leftMax, rightMax)` (Because a path cannot branch twice when extended upward).

---

## ☕ Standard Java Templates

### 1. State Tuple Return (House Robber III — LC 337)
```java
// Returns int[] where index 0 = max money if NOT robbing node, index 1 = max money if ROBBING node
public int[] robSub(TreeNode root) {
    if (root == null) return new int[]{0, 0};
    
    int[] left = robSub(root.left);
    int[] right = robSub(root.right);
    
    int[] res = new int[2];
    // If we don't rob current node, we can choose to rob or skip left/right children
    res[0] = Math.max(left[0], left[1]) + Math.max(right[0], right[1]);
    
    // If we rob current node, we CANNOT rob left or right children
    res[1] = root.val + left[0] + right[0];
    
    return res;
}
```

### 2. State Machine / Greedy Cover (Binary Tree Cameras — LC 968)
```java
// States: 0 = UNCOVERED, 1 = HAS_CAMERA, 2 = COVERED
private int cameras = 0;

public int minCameraCover(TreeNode root) {
    if (dfs(root) == 0) cameras++; // Root itself is uncovered
    return cameras;
}

private int dfs(TreeNode node) {
    if (node == null) return 2; // Null nodes are covered
    
    int left = dfs(node.left);
    int right = dfs(node.right);
    
    // If either child needs cover, place a camera at current node
    if (left == 0 || right == 0) {
        cameras++;
        return 1; // Current node HAS_CAMERA
    }
    
    // If either child has a camera, current node is COVERED
    if (left == 1 || right == 1) {
        return 2;
    }
    
    // Both children are covered (no camera), so current node is UNCOVERED
    return 0;
}
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Diameter of Binary Tree (LC 543)** | Belongs **exclusively to Pattern 02** (entry subtree depth aggregation). |
| **Path Sum III** | Uses Prefix Sum HashMap state tracking — belongs to **Pattern 02**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Medium** | 5 |
| **Hard** | 2 |
| **Total** | **7** |

---

## 🎯 Question Set (7 Questions)

### Q1. Binary Tree Maximum Path Sum
<a href="https://leetcode.com/problems/binary-tree-maximum-path-sum/" target="_blank">LeetCode 124</a> — **Hard**

**Target Skill:** Branch clipping, single-leg return vs full-path global update.

**Core Reasoning:**
- Path can start and end at any node. Values can be negative!
- Subtree helper returns max single-leg sum: `node.val + Math.max(0, Math.max(left, right))`. (Clip negative branch sums to `0`).
- Update global max: `maxSum = Math.max(maxSum, node.val + max(0, left) + max(0, right))`.

**Why it belongs here:** Canonical Tree DP problem introducing single-leg return vs combined path global updates under negative constraints.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

### Q2. House Robber III
<a href="https://leetcode.com/problems/house-robber-iii/" target="_blank">LeetCode 337</a> — **Medium**

**Target Skill:** Dual-state array return (`[rob, skip]`) from subtree recursion.

**Core Reasoning:**
- Directly connected parent and child nodes cannot both be robbed.
- Subtree returns `int[]{skipMoney, robMoney}`.
- `skipMoney = max(left.skip, left.rob) + max(right.skip, right.rob)`.
- `robMoney = node.val + left.skip + right.skip`.
- Answer = `Math.max(res[0], res[1])`.

**Why it belongs here:** Quintessential Tree DP state choice problem.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

### Q3. Binary Tree Cameras
<a href="https://leetcode.com/problems/binary-tree-cameras/" target="_blank">LeetCode 968</a> — **Hard**

**Target Skill:** 3-state greedy postorder state machine (`UNCOVERED`, `HAS_CAMERA`, `COVERED`).

**Core Reasoning:**
- Place minimum number of cameras to monitor all nodes. A camera monitors parent, self, and children.
- Postorder bottom-up: Greedy choice is to place cameras as HIGH as possible (at parents, not leaves!).
- Return state: `0` (Needs camera), `1` (Has camera), `2` (Covered).
- If any child is `0` $\to$ current node MUST place camera (state `1`), `cameras++`.
- Else if any child is `1` $\to$ current node is covered (state `2`).
- Else $\to$ current node needs cover from parent (state `0`).

**Why it belongs here:** Advanced SDE-1 Tree DP state machine problem.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

### Q4. Distribute Coins in Binary Tree
<a href="https://leetcode.com/problems/distribute-coins-in-binary-tree/" target="_blank">LeetCode 979</a> — **Medium**

**Target Skill:** Net excess/deficit coin balance flow.

**Core Reasoning:**
- Total $N$ coins across $N$ nodes. Each node must end up with 1 coin.
- Excess coins at node = `node.val - 1`.
- Net balance of subtree = `leftBalance + rightBalance + node.val - 1`.
- Number of coin moves across node's parent edge = `Math.abs(netBalance)`.
- Accumulate `ans += Math.abs(netBalance)` and return `netBalance` to parent.

**Why it belongs here:** Demonstrates signed flow balance state propagation across subtrees.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

### Q5. Longest ZigZag Path in a Binary Tree
<a href="https://leetcode.com/problems/longest-zigzag-path-in-a-binary-tree/" target="_blank">LeetCode 1372</a> — **Medium**

**Target Skill:** Directional state tracking (`[leftZigzag, rightZigzag]`).

**Core Reasoning:**
- A ZigZag path alternates directions (Left $\to$ Right $\to$ Left $\to$ ...).
- Subtree helper returns `int[]{leftLength, rightLength}`:
  - `leftLength`: length of ZigZag path starting from current node moving LEFT.
  - `rightLength`: length of ZigZag path starting from current node moving RIGHT.
- `currLeft = 1 + leftChild[1]` (takes right path from left child).
- `currRight = 1 + rightChild[0]` (takes left path from right child).
- Update global max with both lengths.

**Why it belongs here:** Directional state vector return from subtrees.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

### Q6. Maximum Difference Between Node and Ancestor
<a href="https://leetcode.com/problems/maximum-difference-between-node-and-ancestor/" target="_blank">LeetCode 1026</a> — **Medium**

**Target Skill:** Ancestor range tracking (`minVal`, `maxVal`).

**Core Reasoning:**
- Find max $|A.val - B.val|$ where $A$ is an ancestor of $B$.
- Pass `minVal` and `maxVal` seen so far down the branch.
- At leaf or null node, max difference along this branch is `maxVal - minVal`.
- Update `maxDiff = Math.max(maxDiff, maxVal - minVal)`.

**Why it belongs here:** Top-down subtree state tracking of path extremes.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

### Q7. Count Good Nodes in Binary Tree
<a href="https://leetcode.com/problems/count-good-nodes-in-binary-tree/" target="_blank">LeetCode 1448</a> — **Medium**

**Target Skill:** Path maximum state tracking during DFS.

**Core Reasoning:**
- A node $X$ is "good" if in the path from root to $X$, there are no nodes with value greater than $X$.
- Pass `maxSoFar` down to children.
- If `node.val >= maxSoFar`, increment good node count and set `maxSoFar = node.val`.
- Recurse left and right with updated `maxSoFar`.

**Why it belongs here:** Entry-level path state propagation for tree condition evaluation.

**Complexity:** Time: $O(N)$, Space: $O(H)$.

---

## ⚡ Mastery Checklist

- [ ] Can you articulate why `Binary Tree Maximum Path Sum` clips negative branch returns to 0?
- [ ] Do you understand why `House Robber III` returns an `int[2]` tuple to prevent exponential re-computation?
- [ ] Can you explain the 3 states of `Binary Tree Cameras` (`UNCOVERED`, `HAS_CAMERA`, `COVERED`) and why greedy postorder placement works?
- [ ] How does net excess coin balance solve `Distribute Coins in Binary Tree` in $O(N)$ time?
