# Pattern 06 — Topological Sort & DAG

Topological Sorting produces a linear ordering of vertices in a Directed Acyclic Graph (DAG) such that for every directed edge $u \to v$, vertex $u$ comes before $v$ in the ordering. It is the primary algorithm for dependency resolution, task scheduling, and prerequisite sequencing.

---

## Kahn's Algorithm (BFS Indegree Approach)

```text
1. Compute the Indegree (number of incoming directed edges) for each vertex.
2. Initialize Queue with all vertices having Indegree == 0 (no prerequisites).
3. While Queue is not empty:
     a. Poll node u, add u to topological ordering.
     b. For each neighbor v of u:
          - Decrement indegree[v] by 1.
          - If indegree[v] reaches 0, push v into Queue.
4. If processed count == Total Vertices V -> Valid Topological Order exists.
   Else -> Graph contains a cycle!
```

---

## Pattern Questions (4 Canonical)

### 1. Course Schedule II — <a href="https://leetcode.com/problems/course-schedule-ii/" target="_blank">LeetCode 210</a>

#### Problem Statement
Return the ordering of courses you should take to finish all `numCourses`. If it is impossible to finish all courses, return an empty array `[]`.

#### Key Insight & Kahn's Ordering
Build an adjacency list and an `indegree` array. Apply Kahn's Algorithm to generate the linear sequence.

#### Java Implementation
```java
import java.util.*;

public class CourseScheduleII {
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        List<List<Integer>> adj = new ArrayList<>();
        for (int i = 0; i < numCourses; i++) adj.add(new ArrayList<>());
        int[] indegree = new int[numCourses];

        for (int[] pre : prerequisites) {
            adj.get(pre[1]).add(pre[0]); // pre[1] -> pre[0]
            indegree[pre[0]]++;
        }

        Queue<Integer> queue = new ArrayDeque<>();
        for (int i = 0; i < numCourses; i++) {
            if (indegree[i] == 0) {
                queue.offer(i);
            }
        }

        int[] order = new int[numCourses];
        int index = 0;

        while (!queue.isEmpty()) {
            int curr = queue.poll();
            order[index++] = curr;

            for (int neighbor : adj.get(curr)) {
                indegree[neighbor]--;
                if (indegree[neighbor] == 0) {
                    queue.offer(neighbor);
                }
            }
        }

        return index == numCourses ? order : new int[0];
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(V + E)$ — Processes each course and dependency edge.
- **Space Complexity:** $O(V + E)$ — Adjacency list and indegree storage.

---

### 2. Alien Dictionary — Classic Interview Problem (LeetCode 269)

#### Problem Statement
Given a sorted dictionary of words from an alien language, derive the lexicographical order of characters in the alien language.

#### Key Insight & Implicit Dependency Graph Construction
1. Create graph nodes for all unique characters present across all words.
2. Compare adjacent words `word1` and `word2` character-by-character to find the **first differing character**:
   - If `word1[k] != word2[k]`, directed edge `word1[k] -> word2[k]` represents a precedent constraint.
   - **Invalid Prefix Edge Case**: If `word2` is a prefix of `word1` (e.g., `"abc"` comes before `"ab"`), the dictionary is invalid!
3. Perform Kahn's algorithm on character nodes.

#### Java Implementation
```java
import java.util.*;

public class AlienDictionary {
    public String alienOrder(String[] words) {
        Map<Character, Set<Character>> adj = new HashMap<>();
        Map<Character, Integer> indegree = new HashMap<>();

        // Initialize graph nodes
        for (String word : words) {
            for (char c : word.toCharArray()) {
                indegree.putIfAbsent(c, 0);
                adj.putIfAbsent(c, new HashSet<>());
            }
        }

        // Build directed edges from adjacent word comparisons
        for (int i = 0; i < words.length - 1; i++) {
            String w1 = words[i];
            String w2 = words[i + 1];

            // Invalid prefix check (e.g., "abcd" before "ab")
            if (w1.length() > w2.length() && w1.startsWith(w2)) {
                return "";
            }

            for (int j = 0; j < Math.min(w1.length(), w2.length()); j++) {
                char c1 = w1.charAt(j);
                char c2 = w2.charAt(j);
                if (c1 != c2) {
                    if (!adj.get(c1).contains(c2)) {
                        adj.get(c1).add(c2);
                        indegree.put(c2, indegree.get(c2) + 1);
                    }
                    break; // Only first differing character defines precedence
                }
            }
        }

        // Kahn's Topological Sort
        Queue<Character> queue = new ArrayDeque<>();
        for (char c : indegree.keySet()) {
            if (indegree.get(c) == 0) {
                queue.offer(c);
            }
        }

        StringBuilder sb = new StringBuilder();
        while (!queue.isEmpty()) {
            char curr = queue.poll();
            sb.append(curr);

            for (char neighbor : adj.get(curr)) {
                indegree.put(neighbor, indegree.get(neighbor) - 1);
                if (indegree.get(neighbor) == 0) {
                    queue.offer(neighbor);
                }
            }
        }

        return sb.length() == indegree.size() ? sb.toString() : "";
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(C)$ where $C$ is total number of characters in all words.
- **Space Complexity:** $O(1)$ — Alphabet size is bounded (max 26 characters).

---

### 3. Parallel Courses — Classic Interview Problem (LeetCode 1136)

#### Problem Statement
Given `n` courses and `relations` where `relations[i] = [prev, next]`. In one semester, you can take any number of courses as long as you have taken all prerequisites. Return the minimum number of semesters needed to take all courses.

#### Level-by-Level Kahn's BFS
While LC 210 asks for the course order, LC 1136 asks for **minimum semesters (tree depth)**:
- Process the Kahn's queue level-by-level (batching all courses available in the current semester).
- Each level processed equals 1 semester.

#### Java Implementation
```java
import java.util.*;

public class ParallelCourses {
    public int minimumSemesters(int n, int[][] relations) {
        List<List<Integer>> adj = new ArrayList<>();
        for (int i = 0; i <= n; i++) adj.add(new ArrayList<>());
        int[] indegree = new int[n + 1];

        for (int[] rel : relations) {
            adj.get(rel[0]).add(rel[1]);
            indegree[rel[1]]++;
        }

        Queue<Integer> queue = new ArrayDeque<>();
        for (int i = 1; i <= n; i++) {
            if (indegree[i] == 0) queue.offer(i);
        }

        int semesters = 0;
        int processedCourses = 0;

        while (!queue.isEmpty()) {
            semesters++;
            int size = queue.size();

            for (int i = 0; i < size; i++) {
                int curr = queue.poll();
                processedCourses++;

                for (int neighbor : adj.get(curr)) {
                    indegree[neighbor]--;
                    if (indegree[neighbor] == 0) {
                        queue.offer(neighbor);
                    }
                }
            }
        }

        return processedCourses == n ? semesters : -1;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(V + E)$ — Kahn's BFS pass.
- **Space Complexity:** $O(V + E)$ — Graph representation.

---

### 4. Minimum Height Trees — <a href="https://leetcode.com/problems/minimum-height-trees/" target="_blank">LeetCode 310</a>

> [!IMPORTANT]
> **Classification Note**: LC 310 is **not** an ordinary topological sort. Instead, it uses **iterative leaf trimming, degree reduction, and layered removal** to locate the topological center of a tree.

#### Problem Statement
Given a tree of `n` nodes labeled `0` to `n - 1`. Choose any node as root. Return a list of all roots that produce a Minimum Height Tree (MHT).

#### Key Insight & Graph Center Trimming
- A tree has at most **2 topological centroids/centers**.
- **Leaf Trimming**: Leaves are nodes with degree 1.
- Iteratively trim all leaves layer-by-layer (similar to Kahn's indegree reduction) until at most 2 nodes remain. The remaining nodes are the roots of Minimum Height Trees.

#### Java Implementation (Leaf Trimming BFS)
```java
import java.util.*;

public class MinimumHeightTrees {
    public List<Integer> findMinHeightTrees(int n, int[][] edges) {
        if (n == 1) return Collections.singletonList(0);

        List<Set<Integer>> adj = new ArrayList<>();
        for (int i = 0; i < n; i++) adj.add(new HashSet<>());
        for (int[] edge : edges) {
            adj.get(edge[0]).add(edge[1]);
            adj.get(edge[1]).add(edge[0]);
        }

        // Initialize queue with initial leaves (degree == 1)
        List<Integer> leaves = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            if (adj.get(i).size() == 1) {
                leaves.add(i);
            }
        }

        int remainingNodes = n;

        // Trim leaves until <= 2 nodes remain
        while (remainingNodes > 2) {
            remainingNodes -= leaves.size();
            List<Integer> newLeaves = new ArrayList<>();

            for (int leaf : leaves) {
                // Get the unique neighbor of leaf
                int neighbor = adj.get(leaf).iterator().next();
                adj.get(neighbor).remove(leaf); // Trim leaf

                if (adj.get(neighbor).size() == 1) {
                    newLeaves.add(neighbor);
                }
            }

            leaves = newLeaves;
        }

        return leaves;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(V)$ — Each node and edge is trimmed once.
- **Space Complexity:** $O(V)$ — Adjacency list and leaf queue.

---

## Pattern Summary

| Problem | Core Mechanism | Output / Distinction |
| :--- | :--- | :--- |
| **LC 210 (Course Order)** | Standard Kahn's BFS | Linear ordered sequence array |
| **LC 269 (Alien Dict)** | Character precedence comparison | Custom graph build + Topological sort |
| **LC 1136 (Parallel Courses)**| Batch level-by-level BFS | Minimum semester count (tree depth) |
| **LC 310 (MHT Trees)** | Iterative degree reduction | Graph-center recognition via leaf trimming |
