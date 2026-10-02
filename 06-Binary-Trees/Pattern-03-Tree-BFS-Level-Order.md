# Pattern 03: Tree BFS / Level-Order Patterns

> Breadth-First Search (BFS) processes a binary tree level-by-level, depth-by-depth, using a FIFO Queue. It is the optimal structure for level-wise aggregations, shortest depth queries, and horizontal view evaluations.

---

## Why This Pattern Exists

While DFS explores down a single branch to maximum depth before backtracking, BFS explores all nodes at depth $D$ before visiting any node at depth $D+1$.

BFS is required when:
- Problem output is grouped level by level (e.g., `[[root], [left, right], ...]`).
- Level metrics (average value, max value, level size, rightmost/leftmost element) are requested.
- Finding the shortest path or shallowest target node in an unweighted tree structure.

---

## ☕ Standard Java Templates

### 1. Level-Order Snapshot Pattern
```java
public List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> result = new ArrayList<>();
    if (root == null) return result;
    
    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);
    
    while (!queue.isEmpty()) {
        int levelSize = queue.size(); // Snapshot size of current level
        List<Integer> currentLevel = new ArrayList<>();
        
        for (int i = 0; i < levelSize; i++) {
            TreeNode node = queue.poll();
            currentLevel.add(node.val);
            
            if (node.left != null) queue.offer(node.left);
            if (node.right != null) queue.offer(node.right);
        }
        
        result.add(currentLevel);
    }
    
    return result;
}
```

### 2. Level Indexing for Width / Positional State (LC 662)
```java
class Pair {
    TreeNode node;
    int index;
    Pair(TreeNode node, int index) { this.node = node; this.index = index; }
}

// In loop: left child index = 2 * idx, right child index = 2 * idx + 1
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Right Side View (LC 199)** | Belongs **exclusively to Pattern 03** because taking the last element of each level queue is a level-order operation. |
| **Vertical Order Traversal** | Requires tracking horizontal column coordinate ($X$) across subtrees — belongs to **Tree Views & Coordinates (Pattern 04)**. |
| **All Nodes Distance K** | Requires moving upward to parents (graph traversal) — belongs to **Hidden Recognition (Pattern 10)**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 1 |
| **Medium** | 5 |
| **Total** | **6** |

---

## 🎯 Question Set (6 Questions)

### Q1. Binary Tree Level Order Traversal
<a href="https://leetcode.com/problems/binary-tree-level-order-traversal/" target="_blank">LeetCode 102</a> — **Medium**

**Target Skill:** Queue-based level snapshot processing.

**Core Reasoning:**
- Initialize `Queue<TreeNode>`, offer `root`.
- While queue is non-empty, capture `levelSize = queue.size()`.
- Iterate `levelSize` times: poll node, collect value, offer non-null children.
- Add level list to master result list.

**Why it belongs here:** Canonical foundation for level-by-level BFS traversal.

**Complexity:** Time: $O(N)$, Space: $O(W)$ (where $W$ is maximum width of tree).

---

### Q2. Binary Tree Level Order Traversal II
<a href="https://leetcode.com/problems/binary-tree-level-order-traversal-ii/" target="_blank">LeetCode 107</a> — **Medium**

**Target Skill:** Bottom-up level order collection.

**Core Reasoning:**
- Standard level-order traversal, but insert each level list at index `0` of result list (`result.add(0, currentLevel)` or reverse at end).

**Why it belongs here:** Direct variation demonstrating output ordering modification on level-wise BFS.

**Complexity:** Time: $O(N)$, Space: $O(W)$.

---

### Q3. Average of Levels in Binary Tree
<a href="https://leetcode.com/problems/average-of-levels-in-binary-tree/" target="_blank">LeetCode 637</a> — **Easy**

**Target Skill:** Level-wise numeric accumulation.

**Core Reasoning:**
- Standard BFS loop. For each level of size `K`, sum node values using `double sum` to prevent integer overflow.
- Append `sum / K` to averages result list.

**Why it belongs here:** Shows scalar reduction per level during queue iteration.

**Complexity:** Time: $O(N)$, Space: $O(W)$.

---

### Q4. Binary Tree Right Side View
<a href="https://leetcode.com/problems/binary-tree-right-side-view/" target="_blank">LeetCode 199</a> — **Medium**

**Target Skill:** Tail-node extraction from level snapshot.

**Core Reasoning:**
- Imagine standing on the right side of the tree. You only see the rightmost node at each depth level.
- Run standard level-order BFS.
- For each level: when `i == levelSize - 1` (last element polled in current level), append `node.val` to result.

**Why it belongs here:** **Canonical home for LC 199.** Right side view is fundamentally the last node of each BFS level snapshot.

**Complexity:** Time: $O(N)$, Space: $O(W)$.

---

### Q5. Binary Tree Zigzag Level Order Traversal
<a href="https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/" target="_blank">LeetCode 103</a> — **Medium**

**Target Skill:** Alternating direction level insertion using Deque / List reversing.

**Core Reasoning:**
- Traverse level by level. Maintain `boolean leftToRight = true`.
- For current level list: if `leftToRight` is true, add to end of level list (`list.add(val)`). If false, add to front (`list.add(0, val)`).
- Flip `leftToRight = !leftToRight` after completing each level.

**Why it belongs here:** Demonstrates alternating insertion order across levels while maintaining uniform queue processing.

**Complexity:** Time: $O(N)$, Space: $O(W)$.

---

### Q6. Maximum Width of Binary Tree
<a href="https://leetcode.com/problems/maximum-width-of-binary-tree/" target="_blank">LeetCode 662</a> — **Medium**

**Target Skill:** Position index tracking in complete binary tree representation.

**Core Reasoning:**
- Width of a level is distance between leftmost and rightmost non-null nodes, including null gaps between them.
- Assign index to nodes: root at index `1`. Left child at `2 * idx`, Right child at `2 * idx + 1`.
- Store `Pair<TreeNode, Integer>` in queue.
- At start of level loop, `firstIdx = queue.peek().index`.
- At end of level loop, `lastIdx = currentPair.index`. Width = `lastIdx - firstIdx + 1`.
- Update `maxWidth = Math.max(maxWidth, width)`. (Normalize indices per level `idx - firstIdx` to prevent integer overflow).

**Why it belongs here:** Advanced level-wise positional index tracking without allocating actual null nodes.

**Complexity:** Time: $O(N)$, Space: $O(W)$.

---

## ⚡ Mastery Checklist

- [ ] Do you know why `levelSize = queue.size()` MUST be captured before the level `for` loop starts?
- [ ] Can you explain why `Right Side View` is fundamentally a level-order BFS problem?
- [ ] Can you implement `Maximum Width of Binary Tree` using $2i$ index normalization to prevent 32-bit integer overflow?
- [ ] Do you understand why BFS space complexity is $O(W)$ (max width, up to $N/2$ for leaf level) compared to DFS $O(H)$?
