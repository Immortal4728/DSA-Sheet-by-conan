# Pattern 06: Tree Construction & Serialization

> Binary trees can be represented as linear sequences (traversal arrays or string streams). Tree construction algorithms reconstruct unique 2D tree structures from linear representations.

---

## Why This Pattern Exists

When storing trees in databases, transmitting them over networks, or rebuilding trees from traversal output, we must convert between 2D tree references and 1D serialized representations.

Key Principles:
1. **Single Traversal Ambiguity:** A single traversal array (e.g., `[1, 2, 3]`) is ambiguous because multiple tree topologies can produce the same sequence.
2. **Two Traversal Reconstruction:** Combining **Inorder** with either **Preorder** or **Postorder** uniquely defines any binary tree (with unique values).
   - Preorder gives the `Root` (first element).
   - Inorder uses `Root` location to split elements into `Left Subtree` and `Right Subtree`.
3. **Null-Delimited Serialization:** A single traversal stream (e.g., Preorder) can uniquely encode a tree IF explicit null markers (like `"null"` or `"#"`) are preserved.

---

## ☕ Standard Java Templates

### 1. Preorder + Inorder Construction (LC 105)
```java
private Map<Integer, Integer> inorderMap = new HashMap<>();
private int preIdx = 0;

public TreeNode buildTree(int[] preorder, int[] inorder) {
    for (int i = 0; i < inorder.length; i++) {
        inorderMap.put(inorder[i], i);
    }
    return build(preorder, 0, inorder.length - 1);
}

private TreeNode build(int[] preorder, int inLeft, int inRight) {
    if (inLeft > inRight) return null;
    
    int rootVal = preorder[preIdx++];
    TreeNode root = new TreeNode(rootVal);
    int inIndex = inorderMap.get(rootVal);
    
    // Construct Left then Right (matching Preorder order)
    root.left = build(preorder, inLeft, inIndex - 1);
    root.right = build(preorder, inIndex + 1, inRight);
    
    return root;
}
```

### 2. Null-Delimited Serialization / Deserialization (LC 297)
```java
// Encodes tree to string using Preorder with "," delimiter and "X" for null
public String serialize(TreeNode root) {
    if (root == null) return "X,";
    return root.val + "," + serialize(root.left) + serialize(root.right);
}

// Decodes string back to tree structure
public TreeNode deserialize(String data) {
    Queue<String> nodes = new LinkedList<>(Arrays.asList(data.split(",")));
    return buildTreeFromQueue(nodes);
}

private TreeNode buildTreeFromQueue(Queue<String> nodes) {
    String val = nodes.poll();
    if (val.equals("X")) return null;
    
    TreeNode node = new TreeNode(Integer.parseInt(val));
    node.left = buildTreeFromQueue(nodes);
    node.right = buildTreeFromQueue(nodes);
    return node;
}
```

---

## 🛑 Pattern Boundary

| Problem | Why it belongs elsewhere |
|---------|--------------------------|
| **Flatten Binary Tree to Linked List** | Restructures node pointers in-place without building new nodes — belongs to **Transformations (Pattern 07)**. |
| **Convert Sorted Array to BST** | Uses BST middle-element invariant — belongs to **BST (Section 07)**. |

---

## 📊 Difficulty Distribution

| Difficulty | Count |
|------------|-------|
| **Easy** | 1 |
| **Medium** | 3 |
| **Hard** | 1 |
| **Total** | **5** |

---

## 🎯 Question Set (5 Questions)

### Q1. Construct Binary Tree from Preorder and Inorder Traversal
<a href="https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/" target="_blank">LeetCode 105</a> — **Medium**

**Target Skill:** Root partitioning via HashMap index lookup.

**Core Reasoning:**
- `preorder[0]` is root.
- Find `root` in `inorder` using HashMap. Elements left of root index belong to `left subtree`, right elements belong to `right subtree`.
- Recurse left first, then right.

**Why it belongs here:** Canonical tree construction problem.

**Complexity:** Time: $O(N)$ with HashMap lookup, Space: $O(N)$.

---

### Q2. Construct Binary Tree from Inorder and Postorder Traversal
<a href="https://leetcode.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/" target="_blank">LeetCode 106</a> — **Medium**

**Target Skill:** Right-first postorder index consumption.

**Core Reasoning:**
- `postorder[last]` is root. Decrement postorder index.
- Locate root in `inorder` map.
- **Crucial:** Build `right subtree` FIRST, then `left subtree` (because Postorder reads Left $\to$ Right $\to$ Root; backward reading yields Root $\to$ Right $\to$ Left!).

**Why it belongs here:** Teaches subtle traversal order dependencies when reading arrays backward.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

### Q3. Construct Binary Tree from Preorder and Postorder Traversal
<a href="https://leetcode.com/problems/construct-binary-tree-from-preorder-and-postorder-traversal/" target="_blank">LeetCode 889</a> — **Medium**

**Target Skill:** Boundary matching without Inorder array.

**Core Reasoning:**
- `preorder[0]` is root. Next element `preorder[1]` is the root of the left subtree.
- Find `preorder[1]` inside `postorder` array. All elements up to that index belong to left subtree.
- Split remaining elements for right subtree. Recurse.

**Why it belongs here:** Advanced construction problem showing how left child identification replaces inorder splitting.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

### Q4. Serialize and Deserialize Binary Tree
<a href="https://leetcode.com/problems/serialize-and-deserialize-binary-tree/" target="_blank">LeetCode 297</a> — **Hard**

**Target Skill:** Null-delimited stream serialization and queue-based reconstruction.

**Core Reasoning:**
- **Serialize:** Preorder traversal string with `,` separator and `X` for null.
- **Deserialize:** Split string by `,` into a FIFO Queue.
- Poll queue: if `"X"`, return `null`. Else create node, recurse `node.left = deserialize(q)`, `node.right = deserialize(q)`.

**Why it belongs here:** Quintessential tree serialization problem for system design & SDE interviews.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

### Q5. Construct String from Binary Tree
<a href="https://leetcode.com/problems/construct-string-from-binary-tree/" target="_blank">LeetCode 606</a> — **Easy**

**Target Skill:** Omission rules for empty child parentheses.

**Core Reasoning:**
- Preorder string representation with parentheses `root(left)(right)`.
- Omit empty parentheses `()` EXCEPT when left child is null but right child exists! (e.g., `1()(2)` must preserve empty `()` for left to maintain structure).

**Why it belongs here:** Teaches string formatting and parenthetical structural preservation rules.

**Complexity:** Time: $O(N)$, Space: $O(N)$.

---

## ⚡ Mastery Checklist

- [ ] Why does Preorder + Inorder uniquely define a tree, but Preorder alone does not?
- [ ] Why must `Construct Tree from Inorder & Postorder` build the RIGHT subtree before the LEFT subtree?
- [ ] Can you implement `Serialize and Deserialize` using Preorder + Queue in under 15 lines?
- [ ] What is the purpose of explicit null markers in serialized streams?
