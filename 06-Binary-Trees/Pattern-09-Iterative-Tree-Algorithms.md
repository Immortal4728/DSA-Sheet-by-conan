# Pattern 09: Iterative Tree Algorithms (Technique Only)

> Every recursive tree algorithm uses the system call stack implicitly. Converting recursive traversals to iterative algorithms requires managing an explicit `ArrayDeque` stack to simulate call stack frames manually.

---

## 🛑 Important Note on Curriculum Architecture

> **This is a TECHNIQUE section.** It does NOT introduce new LeetCode questions to the syllabus. It provides explicit stack implementations for the canonical traversal problems introduced in Pattern 01:
> - **Preorder Traversal** (<a href="https://leetcode.com/problems/binary-tree-preorder-traversal/" target="_blank">LeetCode 144</a>)
> - **Inorder Traversal** (<a href="https://leetcode.com/problems/binary-tree-inorder-traversal/" target="_blank">LeetCode 94</a>)
> - **Postorder Traversal** (<a href="https://leetcode.com/problems/binary-tree-postorder-traversal/" target="_blank">LeetCode 145</a>)

---

## Why Master Iterative Traversal?

1. **StackOverflow Protection:** In environments with strict stack limits, deep tree recursion ($H > 10,000$) causes `StackOverflowError`. Iterative algorithms store state on the heap, allowing traversal of arbitrarily deep trees.
2. **Custom State Suspensions:** Iterative traversals allow pausing and resuming traversal on demand (e.g., implementing a `BSTIterator`).
3. **Interview Requirement:** Interviewers frequently ask candidates to convert their recursive code into explicit stack iteration.

---

## ☕ Standard Java Iterative Templates

### 1. Iterative Preorder Traversal (Root $\to$ Left $\to$ Right)
```java
public List<Integer> preorderTraversal(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    if (root == null) return result;
    
    Deque<TreeNode> stack = new ArrayDeque<>();
    stack.push(root);
    
    while (!stack.isEmpty()) {
        TreeNode curr = stack.pop();
        result.add(curr.val);
        
        // Push RIGHT first so LEFT is popped and processed first!
        if (curr.right != null) stack.push(curr.right);
        if (curr.left != null) stack.push(curr.left);
    }
    
    return result;
}
```

### 2. Iterative Inorder Traversal (Left $\to$ Root $\to$ Right)
```java
public List<Integer> inorderTraversal(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    Deque<TreeNode> stack = new ArrayDeque<>();
    TreeNode curr = root;
    
    while (curr != null || !stack.isEmpty()) {
        // Step 1: Push all left nodes down to leftmost leaf
        while (curr != null) {
            stack.push(curr);
            curr = curr.left;
        }
        
        // Step 2: Pop leftmost node, visit value
        curr = stack.pop();
        result.add(curr.val);
        
        // Step 3: Move to right subtree
        curr = curr.right;
    }
    
    return result;
}
```

### 3. Iterative Postorder Traversal — 2-Stack / Reverse Preorder Approach
```java
public List<Integer> postorderTraversal(TreeNode root) {
    LinkedList<Integer> result = new LinkedList<>();
    if (root == null) return result;
    
    Deque<TreeNode> stack = new ArrayDeque<>();
    stack.push(root);
    
    // Modified Preorder: Root -> Right -> Left, inserting at head of list
    while (!stack.isEmpty()) {
        TreeNode curr = stack.pop();
        result.addFirst(curr.val); // Reverse order: produces Left -> Right -> Root!
        
        if (curr.left != null) stack.push(curr.left);
        if (curr.right != null) stack.push(curr.right);
    }
    
    return result;
}
```

### 4. Iterative Postorder Traversal — 1-Stack Peak Track Approach
```java
public List<Integer> postorderOneStack(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    Deque<TreeNode> stack = new ArrayDeque<>();
    TreeNode curr = root;
    TreeNode lastVisited = null;
    
    while (curr != null || !stack.isEmpty()) {
        if (curr != null) {
            stack.push(curr);
            curr = curr.left;
        } else {
            TreeNode peekNode = stack.peek();
            // If right child exists and coming from left, move to right child
            if (peekNode.right != null && lastVisited != peekNode.right) {
                curr = peekNode.right;
            } else {
                // Both subtrees done: process root
                result.add(peekNode.val);
                lastVisited = stack.pop();
            }
        }
    }
    return result;
}
```

---

## 📊 Summary of Iterative Techniques

| Traversal | Stack Strategy | Key Implementation Detail |
|-----------|----------------|---------------------------|
| **Preorder** | Single Stack | Push `right` then `left` to process left first. |
| **Inorder** | Single Stack + Pointer | Drill down `curr = curr.left` until `null`, pop, visit, then `curr = curr.right`. |
| **Postorder (Reverse Preorder)** | Single Stack + `addFirst` | Traverse Root $\to$ Right $\to$ Left, prepend to list to yield Left $\to$ Right $\to$ Root. |
| **Postorder (Pure 1-Stack)** | Single Stack + `lastVisited` | Track `lastVisited` node to prevent re-entering right subtree after returning from it. |

---

## ⚡ Mastery Checklist

- [ ] Why must `right` child be pushed before `left` child in iterative Preorder?
- [ ] Can you trace the `while (curr != null || !stack.isEmpty())` loop for Inorder traversal on paper?
- [ ] How does `addFirst()` (or using 2 stacks) convert Root $\to$ Right $\to$ Left into valid Postorder?
- [ ] What is the purpose of `lastVisited` in the single-stack Postorder implementation?
