# Binary Trees, BSTs & Minimum Spanning Trees: The Definitive Master Guide

---

## Table of Contents
1. [General Trees & Tree Terminology](#1-general-trees--tree-terminology)
   - [Fundamental Definitions: Root, Child, Parent, Sibling, Ancestor, Descendant](#fundamental-definitions-root-child-parent-sibling-ancestor-descendant)
   - [Depth vs. Height vs. Degree](#depth-vs-height-vs-degree)
   - [First-Child / Next-Sibling Representation (Arbitrary Degree Trees)](#first-child--next-sibling-representation-arbitrary-degree-trees)
2. [Binary Tree Fundamentals & Classifications](#2-binary-tree-fundamentals--classifications)
   - [Recursive Definition & Linked Representation](#recursive-definition--linked-representation)
   - [Taxonomy of Binary Trees: Full, Complete, Perfect, and Degenerate](#taxonomy-of-binary-trees-full-complete-perfect-and-degenerate)
   - [Mathematical Bounds: Heights, Leaves, and Node Counts](#mathematical-bounds-heights-leaves-and-node-counts)
3. [Binary Tree Traversals & Reconstruction Theorems](#3-binary-tree-traversals--reconstruction-theorems)
   - [Preorder, Inorder, and Postorder Traversals (DFS)](#preorder-inorder-and-postorder-traversals-dfs)
   - [Level-Order Traversal (BFS)](#level-order-traversal-bfs)
   - [Reconstructing Binary Trees from Traversal Sequences (GATE Analysis)](#reconstructing-binary-trees-from-traversal-sequences-gate-analysis)
   - [Why Preorder + Postorder Fails Without Strict Fullness (2-Node Counterexample)](#why-preorder--postorder-fails-without-strict-fullness-2-node-counterexample)
4. [Binary Search Trees (BST): Properties & Querying](#4-binary-search-trees-bst-properties--querying)
   - [The Binary Search Tree Property](#the-binary-search-tree-property)
   - [Inorder Tree Walk Theorem (Proof that Inorder Yields Sorted Keys)](#inorder-tree-walk-theorem-proof-that-inorder-yields-sorted-keys)
   - [`Tree-Search`: Recursive vs. Iterative Implementations](#tree-search-recursive-vs-iterative-implementations)
   - [`Tree-Minimum` and `Tree-Maximum`](#tree-minimum-and-tree-maximum)
   - [`Tree-Successor` and `Tree-Predecessor` (Exhaustive Case Analysis)](#tree-successor-and-tree-predecessor-exhaustive-case-analysis)
5. [BST Dynamic Modifications: Insertion & Deletion](#5-bst-dynamic-modifications-insertion--deletion)
   - [`Tree-Insert`: The Trailing Pointer Technique](#tree-insert-the-trailing-pointer-technique)
   - [`Tree-Delete`: The Three Fundamental Cases](#tree-delete-the-three-fundamental-cases)
   - [Deep Dive into Case 3: Successor Splicing & Structural Correctness](#deep-dive-into-case-3-successor-splicing--structural-correctness)
   - [Why Node Copying vs. Pointer Transplants Matter](#why-node-copying-vs-pointer-transplants-matter)
6. [Segment Trees: Range Queries & Complexity (ClassPractice Q8)](#6-segment-trees-range-queries--complexity-classpractice-q8)
   - [Segment Tree Architecture & Representation](#segment-tree-architecture--representation)
   - [Linear Build Time: Proof of $O(N)$ Construction](#linear-build-time-proof-of-on-construction)
   - [Space Complexity: Proof of $O(N)$ Space ($4N$ Bound)](#space-complexity-proof-of-on-space-4n-bound)
7. [Minimum Spanning Trees (MST) & Graph Trees (ClassPractice Exam Special)](#7-minimum-spanning-trees-mst--graph-trees-classpractice-exam-special)
   - [Core Spanning Tree Theorems: The Cut Property & Cycle Property](#core-spanning-tree-theorems-the-cut-property--cycle-property)
   - [Kruskal's Algorithm & The Disjoint-Set / Union-Find Role (ClassPractice Q2)](#kruskals-algorithm--the-disjoint-set--union-find-role-classpractice-q2)
   - [ClassPractice Q3: Step-by-Step Kruskal Trace on 7-Vertex Wheel Graph](#classpractice-q3-step-by-step-kruskal-trace-on-7-vertex-wheel-graph)
   - [ClassPractice Q1: Maximum Spanning Tree in $O(E \log V)$](#classpractice-q1-maximum-spanning-tree-in-oe-\log-v)
   - [ClassPractice Q4: Negative Weights, Reverse-Delete, and Doubling Edge Costs](#classpractice-q4-negative-weights-reverse-delete-and-doubling-edge-costs)
   - [ClassPractice Q11: Weight Squaring Invariance & Acyclic Spanning Tree Proof](#classpractice-q11-weight-squaring-invariance--acyclic-spanning-tree-proof)
   - [ClassPractice Q12: Complete Graph $K_4$ MST Edge Sum Optimization](#classpractice-q12-complete-graph-k_4-mst-edge-sum-optimization)
   - [ClassPractice Q16: Deep Analysis of Modified MST Algorithms (MAYBE-MST-A, B, C)](#classpractice-q16-deep-analysis-of-modified-mst-algorithms-maybe-mst-a-b-c)
8. [Complexity Analysis & Balanced Tree Motivations](#8-complexity-analysis--balanced-tree-motivations)
   - [Worst-Case vs. Average-Case Complexity ($O(h)$ Analysis)](#worst-case-vs-average-case-complexity-oh-analysis)
   - [Randomly Built BSTs ($O(\log n)$ Expected Height)](#randomly-built-bsts-o\log-n-expected-height)
   - [Pathological Degeneracy (Sorted Input Pitfall)](#pathological-degeneracy-sorted-input-pitfall)
   - [Overview of Self-Balancing Trees (AVL, Red-Black Trees)](#overview-of-self-balancing-trees-avl-red-black-trees)
9. [Exhaustive Conceptual True / False Question Bank (With Deep Justifications)](#9-exhaustive-conceptual-true--false-question-bank-with-deep-justifications)

---

# 1. General Trees & Tree Terminology

## Fundamental Definitions: Root, Child, Parent, Sibling, Ancestor, Descendant
A **tree** $T$ is a hierarchical collection of nodes such that:
- If $T$ is non-empty, it contains a unique distinguished node called the **root**.
- Every other node $u$ has exactly one **parent** node.
- A directed edge connects a parent to its **child**.
- Nodes sharing the same parent are **siblings**.
- An **external node (leaf)** is a node with zero children.
- An **internal node** is a non-leaf node (has at least one child).
- A node $u$ is an **ancestor** of $v$ (and $v$ is a **descendant** of $u$) if $u$ lies on the unique path from the root to $v$.

```
                 [ A ] (Root)
                /     \
             [ B ]   [ C ]
            /     \     \
         [ D ]   [ E ] [ F ] (Leaves: D, E, F)
```

---

## Depth vs. Height vs. Degree

1. **Depth of a Node $u$:**
   The number of edges on the unique path from the **root to $u$**:
   $$\text{depth}(\text{root}) = 0$$
   $$\text{depth}(u) = \text{depth}(\text{parent}(u)) + 1$$
2. **Height of a Node $u$:**
   The number of edges on the longest downward path from **$u$ to a leaf**:
   $$\text{height}(\text{leaf}) = 0$$
   $$\text{height}(u) = 1 + \max_{v \in \text{children}(u)} \text{height}(v)$$
3. **Height of a Tree:**
   The height of the root node:
   $$\text{height}(T) = \max_{u \in T} \text{depth}(u)$$
4. **Degree of a Node:** The number of children of that node.
5. **Degree of a Tree:** The maximum degree among all nodes in the tree.

---

## First-Child / Next-Sibling Representation (Arbitrary Degree Trees)

When the maximum number of children per node is unbounded or unknown in advance:
- Allocating a fixed array of child pointers wastes massive memory.
- **Solution (Slide 5):** Each node stores exactly **two pointers**:
  1. `left_child`: Points to the node's first (leftmost) child.
  2. `next_sibling`: Points to the sibling immediately to its right.

```
Node Structure:
┌───────────────────────────────┐
│              Key              │
├───────────────┬───────────────┤
│  left_child   │ next_sibling  │
└───────────────┴───────────────┘
```
This elegant representation encodes any arbitrary multi-way tree using at most $2n$ pointers!

---

# 2. Binary Tree Fundamentals & Classifications

## Recursive Definition & Linked Representation
A **binary tree** is a tree in which every node has **at most two children**, labeled as the **left child** and **right child**.
Formally, a binary tree is either:
1. An empty set (NIL), or
2. A tuple $(r, T_L, T_R)$ where $r$ is a root node, and $T_L$ and $T_R$ are binary trees (left and right subtrees).

### Linked Node Representation (Slide 7 & 15):
Each node contains five fields:
`key`, `left`, `right`, `p` (parent pointer), and optional satellite data.

```text
struct Node {
    KeyType key;
    Node* left;
    Node* right;
    Node* p;
};
```

---

## Taxonomy of Binary Trees: Full, Complete, Perfect, and Degenerate

```
      Full / Proper                 Complete                      Perfect
          (1)                         (1)                           (1)
         /   \                       /   \                         /   \
       (2)   (3)                   (2)   (3)                     (2)   (3)
             / \                   / \   /                       / \   / \
           (4) (5)               (4) (5)(6)                     (4)(5)(6)(7)
```

1. **Full (Proper / Strict) Binary Tree (Slide 13):**
   - Every node has **either 0 or 2 children**. No node has exactly 1 child.
2. **Complete Binary Tree:**
   - Every level, except possibly the last, is completely filled.
   - All nodes in the last level are packed as far **left** as possible.
   - *Key Usage:* Array-based representation of Binary Heaps.
3. **Perfect (Strictly Complete) Binary Tree:**
   - All internal nodes have exactly 2 children, and all leaves reside at the **exact same depth**.
4. **Degenerate (Skewed) Tree:**
   - Every internal node has exactly 1 child. Structurally identical to a singly linked list.

---

## Mathematical Bounds: Heights, Leaves, and Node Counts

Let $n = |V|$ be the number of nodes, $h$ be the height, and $L$ be the number of leaves:

1. **Height Bounds for Binary Trees of $n$ Nodes (Slide 8):**
   - **Maximum Height (Worst-case, Skewed):**
     $$h_{\max} = n - 1$$
   - **Minimum Height (Best-case, Completely Balanced):**
     $$h_{\min} = \lfloor \log_2 n \rfloor$$
2. **Number of Nodes in a Perfect Binary Tree of Height $h$:**
   $$n = \sum_{i=0}^h 2^i = 2^{h+1} - 1$$
   $$L = 2^h = \frac{n + 1}{2}$$
3. **Full Binary Tree Leaf Theorem (Slide 13):**
   > **Theorem:** In any full binary tree with $L$ leaves and $I$ internal nodes:
   > $$L = I + 1$$
   > $$n = 2I + 1 = 2L - 1$$

*Proof:* Every internal node has 2 children, generating $2I$ child edges. Each node except the root has 1 incoming parent edge:
$$n - 1 = 2I \implies (L + I) - 1 = 2I \implies L = I + 1 \quad \blacksquare$$

---

# 3. Binary Tree Traversals

## Preorder, Inorder, and Postorder Traversals (DFS)

Traversals visit every node in a binary tree systematically in $\Theta(n)$ time:

```text
PREORDER(x):                  INORDER(x):                   POSTORDER(x):
    if x != NIL then              if x != NIL then              if x != NIL then
        PRINT(x.key)                  INORDER(x.left)               POSTORDER(x.left)
        PREORDER(x.left)              PRINT(x.key)                  POSTORDER(x.right)
        PREORDER(x.right)             INORDER(x.right)              PRINT(x.key)
```

### Orderings:
- **Preorder:** $\text{Root} \to \text{Left Subtree} \to \text{Right Subtree}$ (Used to clone/serialize trees).
- **Inorder:** $\text{Left Subtree} \to \text{Root} \to \text{Right Subtree}$ (Used in BSTs to extract sorted keys).
- **Postorder:** $\text{Left Subtree} \to \text{Right Subtree} \to \text{Root}$ (Used in bottom-up deletions/evaluations).

---

## Level-Order Traversal (BFS)
Visits nodes level-by-level from top to bottom, left to right, using a FIFO queue:
```text
LEVEL-ORDER(root):
    if root == NIL then return
    Q = EMPTY_QUEUE()
    ENQUEUE(Q, root)
    while not IS-EMPTY(Q) do
        u = DEQUEUE(Q)
        PRINT(u.key)
        if u.left != NIL then ENQUEUE(Q, u.left)
        if u.right != NIL then ENQUEUE(Q, u.right)
```

---

## Reconstructing Binary Trees from Traversal Sequences (GATE Analysis)

**Classic GATE 2008 Problem (Slide 12):**
*Under what conditions can a binary tree be uniquely reconstructed from its traversal sequences?*

1. **Inorder + Preorder $\implies$ UNIQUE Reconstruction.**
   - Preorder's first element identifies the **root**.
   - Locating that root in the Inorder sequence partitions elements into the **left subtree** and **right subtree**.
   - Recursively repeat for both subtrees.
2. **Inorder + Postorder $\implies$ UNIQUE Reconstruction.**
   - Postorder's last element identifies the **root**.
   - Locating that root in the Inorder sequence partitions the left and right subtrees.
3. **Inorder + Level-Order $\implies$ UNIQUE Reconstruction.**

---

## Why Preorder + Postorder Fails Without Strict Fullness

> **Question:** Can a general binary tree be uniquely reconstructed from its Preorder and Postorder traversals?
> **Answer: NO, NOT IN GENERAL.**

### Counterexample:
Consider two distinct binary trees with nodes $\{A, B\}$:
- **Tree 1:** $A$ is root, $B$ is the **left child** of $A$.
  - Preorder: $[A, B]$
  - Postorder: $[B, A]$
- **Tree 2:** $A$ is root, $B$ is the **right child** of $A$.
  - Preorder: $[A, B]$
  - Postorder: $[B, A]$

Both trees produce the **exact same** Preorder and Postorder traversals! It is impossible to determine whether $B$ belongs to the left or right subtree.

> [!IMPORTANT]
> **Exception (Full Binary Trees):** If a binary tree is strictly a **Full Binary Tree** (every node has either 0 or 2 children), then it **CAN** be uniquely reconstructed from Preorder and Postorder! The element immediately following the root in Preorder must be the root of the left subtree, allowing unambiguous partitioning.

---

# 4. Binary Search Trees (BST): Properties & Querying

## The Binary Search Tree Property

> **BST Invariant (Slide 16):**
> Let $x$ be a node in a binary search tree.
> - If $y$ is a node in the **left subtree** of $x$, then:
>   $$y.key \le x.key$$
> - If $y$ is a node in the **right subtree** of $x$, then:
>   $$y.key \ge x.key$$

*(For strict BSTs without duplicates, inequalities are strict: $<$ and $>$.)*

---

## Inorder Tree Walk Theorem (Proof that Inorder Yields Sorted Keys)

> **Theorem (Slide 17):** If $x$ is the root of an $n$-node binary search tree, `INORDER-TREE-WALK(x)` prints the keys in **monotonically increasing sorted order** in $\Theta(n)$ time.

### Proof by Mathematical Induction:
- **Base Case ($n = 0$):** An empty tree prints nothing, which is vacuously sorted.
- **Inductive Hypothesis:** Assume `INORDER-TREE-WALK` correctly outputs sorted keys for all BSTs with $< n$ nodes.
- **Inductive Step:** Consider a BST rooted at $x$ with $n \ge 1$ nodes.
  `INORDER-TREE-WALK(x)` executes:
  1. `INORDER-TREE-WALK(x.left)`
  2. `PRINT(x.key)`
  3. `INORDER-TREE-WALK(x.right)`
  By the inductive hypothesis:
  - Step 1 prints all keys in the left subtree in sorted order. By the BST property, all keys in the left subtree are $\le x.key$.
  - Step 2 prints $x.key$, which is $\ge$ all keys printed in Step 1.
  - Step 3 prints all keys in the right subtree in sorted order. By the BST property, all keys in the right subtree are $\ge x.key$.
  Thus, the concatenated output is strictly sorted. $\blacksquare$

---

## `Tree-Search`: Recursive vs. Iterative Implementations

### Recursive Search (Slide 21):
```text
TREE-SEARCH(x, k):
    if x == NIL or k == x.key then
        return x
    if k < x.key then
        return TREE-SEARCH(x.left, k)
    else
        return TREE-SEARCH(x.right, k)
```

### Iterative Search (Slide 22):
```text
ITERATIVE-TREE-SEARCH(x, k):
    while x != NIL and k != x.key do
        if k < x.key then
            x = x.left
        else
            x = x.right
    return x
```
- **Complexity:** $O(h)$ time, where $h$ is tree height.
- **Engineering Advantage:** The iterative version avoids call-stack frame allocation and is immune to stack-overflow errors on degenerate trees.

---

## `Tree-Minimum` and `Tree-Maximum` (Slides 23–25)

By the BST property, the minimum key must reside in the leftmost node, and the maximum key in the rightmost node:

```text
TREE-MINIMUM(x):
    while x.left != NIL do
        x = x.left
    return x

TREE-MAXIMUM(x):
    while x.right != NIL do
        x = x.right
    return x
```
- **Time Complexity:** $O(h)$.

---

## `Tree-Successor` and `Tree-Predecessor` (Exhaustive Case Analysis)

The **successor** of a node $x$ is the node with the **smallest key strictly greater than $x.key$** (the node visited immediately after $x$ in an inorder walk).

```
Case 1: Right Subtree Exists             Case 2: Right Subtree is NIL
         (x)                                    (Ancestor) ◄── Successor!
           \                                   /
           (Subtree)                         (Node)
           /                                   \
  Successor = Min(x.right)                     (x)
```

```text
TREE-SUCCESSOR(x):
    // Case 1: Right subtree is non-empty
    if x.right != NIL then
        return TREE-MINIMUM(x.right)
        
    // Case 2: Right subtree is empty (climb ancestors)
    y = x.p
    while y != NIL and x == y.right do
        x = y
        y = y.p
    return y
```

### Two Exhaustive Cases for Successor:
1. **Case 1: $x.right \neq \text{NIL}$.**
   The successor is simply the leftmost node in $x$'s right subtree: `TREE-MINIMUM(x.right)`.
2. **Case 2: $x.right == \text{NIL}$.**
   Traverse upwards using parent pointers until reaching an ancestor $y$ such that the current node is the **left child** of $y$. That ancestor $y$ is the successor. If we reach the root from the right, $x$ is the maximum key in the tree, and its successor is `NIL`.

### Predecessor Duality:
- **Case 1 ($x.left \neq \text{NIL}$):** `TREE-MAXIMUM(x.left)`.
- **Case 2 ($x.left == \text{NIL}$):** Lowest ancestor whose right child is also an ancestor of $x$.

---

# 5. BST Dynamic Modifications: Insertion & Deletion

## `Tree-Insert`: The Trailing Pointer Technique

To insert a new node $z$ with key $k$, we traverse downwards from the root until reaching a `NIL` pointer. A **trailing pointer $y$** maintains the parent of current pointer $x$:

```text
TREE-INSERT(T, z):
    y = NIL
    x = T.root
    
    // Trace down to leaf
    while x != NIL do
        y = x
        if z.key < x.key then
            x = x.left
        else
            x = x.right
            
    z.p = y
    if y == NIL then
        T.root = z // Tree was empty
    else if z.key < y.key then
        y.left = z
    else
        y.right = z
```
- **Complexity:** $O(h)$ time, $\Theta(1)$ auxiliary space.

---

## `Tree-Delete`: The Three Fundamental Cases

Deleting a node $z$ from a BST is classified into three cases based on the number of children of $z$:

```
Case 1: 0 Children (Leaf)           Case 2: 1 Child
        [ Parent ]                             [ Parent ]
            │                                      │
           (z) ──► Simply remove                  (z) ──► Splice out
                                                   │
                                                [ Child ]

Case 3: 2 Children
        [ Parent ]
            │
           (z) ◄── Replace key with successor y's key
          /   \
       [ L ] [ R ] ──► contains Successor y (has at most 1 child!)
```

### The Three Cases (Slides 31–33):
1. **Case 1: $z$ has no children ($z$ is a leaf).**
   Simply disconnect $z$ from its parent by setting the parent's child pointer to `NIL`.
2. **Case 2: $z$ has exactly one child.**
   Splice out $z$ by connecting $z$'s single child directly to $z$'s parent.
3. **Case 3: $z$ has two children.**
   Find $z$'s successor $y$ (which lies in $z$'s right subtree: $y = \text{TREE-MINIMUM}(z.right)$).
   - Because $y$ is the minimum in $z$'s right subtree, **$y$ cannot have a left child**! ($y.left == \text{NIL}$).
   - Splice $y$ out of its original position (which is an easy Case 1 or Case 2 deletion).
   - Replace $z$'s contents or position with $y$.

---

## Deep Dive into Case 3: Successor Splicing & Structural Correctness

### The Modern CLRS / In-Place Transplant Procedure:
Rather than merely copying `z.key = y.key` (which breaks external pointers referencing node addresses), modern production code transplants the actual node pointers:

```text
TRANSPLANT(T, u, v):
    if u.p == NIL then
        T.root = v
    else if u == u.p.left then
        u.p.left = v
    else
        u.p.right = v
    if v != NIL then
        v.p = u.p

TREE-DELETE(T, z):
    if z.left == NIL then
        TRANSPLANT(T, z, z.right)
    else if z.right == NIL then
        TRANSPLANT(T, z, z.left)
    else
        y = TREE-MINIMUM(z.right)
        if y.p != z then
            TRANSPLANT(T, y, y.right)
            y.right = z.right
            y.right.p = y
        TRANSPLANT(T, z, y)
        y.left = z.left
        y.left.p = y
```

### Proof of BST Property Invariance After Deletion (Slide 34):
- In Case 3, $y$ is the successor of $z$.
- Every key in $z$'s left subtree is $< z.key \le y.key$. Thus, $y$ is strictly greater than all nodes in $z.left$.
- Since $y$ was the minimum in $z$'s right subtree, every other node in $z.right$ is $\ge y.key$.
- Thus, placing $y$ at $z$'s position preserves the BST property across the entire tree! $\blacksquare$

---

# 6. Segment Trees: Range Queries & Complexity (ClassPractice Q8)

## Segment Tree Architecture & Representation

A **Segment Tree** is a binary tree data structure used for storing intervals or segments. It allows querying which of the stored segments contain a given point, or performing associative range queries (such as Range Minimum Query, Range Sum Query, or Range GCD) and point updates in logarithmic time.

```
                      [0 .. 7]  (Node 1)
                    /          \
            [0 .. 3]            [4 .. 7]
           /        \          /        \
       [0 .. 1]   [2 .. 3]  [4 .. 5]   [6 .. 7]
       /      \   /      \  /      \   /      \
      [0]    [1] [2]    [3][4]    [5] [6]    [7] (Leaves)
```

---

## Linear Build Time: Proof of $O(N)$ Construction (ClassPractice Q8a)

**Exam Question (ClassPractice Quiz-2, Q8):**
> *Which of the following are true for a segment tree built over $N$ elements:*
> *(a) Time taken to build the tree is $O(N)$*
> *(b) Time taken to build the tree is $O(N \log N)$*
> *(c) Space complexity of the tree is $O(N)$*
> *(d) Space complexity of the tree is $O(N \log N)$*
> **Answer: (a) and (c) are TRUE.**

### Mathematical Proof of $O(N)$ Construction Time:
A segment tree built over an array of size $N$ is constructed recursively bottom-up:
1. Divide the current segment $[L, R]$ into two halves: $[L, M]$ and $[M+1, R]$, where $M = \lfloor (L + R) / 2 \rfloor$.
2. Recursively build the left child and right child.
3. Merge the results of the two children in $O(1)$ time:
   $$\text{tree}[\text{node}] = \text{tree}[2 \cdot \text{node}] \oplus \text{tree}[2 \cdot \text{node} + 1]$$

#### Recurrence Relation:
$$T(N) = 2 \cdot T\left(\frac{N}{2}\right) + O(1)$$

By the **Master Theorem**:
- $a = 2, \; b = 2, \; f(N) = O(1) = O(N^0)$.
- Critical exponent: $\log_b a = \log_2 2 = 1$.
- Since $f(N) = O(N^{\log_b a - \epsilon}) = O(N^{1 - 1}) = O(1)$ for $\epsilon = 1$, we are in **Case 1 of the Master Theorem**:
  $$T(N) = \Theta(N^1) = \mathbf{\Theta(N)}$$

#### Intuitive Leaf/Node Count Proof:
- A segment tree over $N$ elements contains exactly $N$ leaves (each corresponding to an individual array element).
- By the Full Binary Tree Theorem, any binary tree with $N$ leaves where every internal node has exactly 2 children contains:
  $$I = N - 1 \text{ internal nodes}$$
- Total number of nodes in the tree:
  $$N_{\text{total}} = N + (N - 1) = 2N - 1$$
- Because the algorithm performs $O(1)$ constant work per node during construction:
  $$\text{Total Time} = (2N - 1) \times O(1) = \mathbf{O(N)} \quad \blacksquare$$

---

## Space Complexity: Proof of $O(N)$ Space ($4N$ Bound) (ClassPractice Q8c)

In an array-based representation of a Segment Tree (where 1-based root node has children at $2i$ and $2i + 1$):
- If $N$ is an exact power of 2 ($N = 2^k$), the tree is a **perfect binary tree**.
  Total nodes $= 2N - 1 < 2N$.
- If $N$ is **not a power of 2**, let $2^{\lceil \log_2 N \rceil}$ be the smallest power of 2 greater than $N$.
- In the worst case, $N = 2^k + 1$:
  - The next power of 2 is $2^{k+1} = 2(N - 1) < 2N$.
  - The tree height will be $h = \lceil \log_2 N \rceil + 1$.
  - The size of the array required to index all nodes up to depth $h$ is:
    $$\text{Array Size} < 2^{h+1} = 2^{\lceil \log_2 N \rceil + 2} < 4N$$
- **Conclusion:** An array of size $4N$ is always strictly sufficient to store a segment tree for any arbitrary $N$.
  $$\text{Space Complexity} = \Theta(4N) = \mathbf{O(N)} \quad \blacksquare$$

---

# 7. Minimum Spanning Trees (MST) & Graph Trees (ClassPractice Exam Special)

## Core Spanning Tree Theorems: The Cut Property & Cycle Property

Let $G = (V, E)$ be a connected, undirected weighted graph with edge weight function $\omega: E \to \mathbb{R}$.
A **Spanning Tree** $T = (V, E_T)$ is an acyclic connected subgraph spanning all $|V|$ vertices, having exactly $|V| - 1$ edges.
A **Minimum Spanning Tree (MST)** is a spanning tree that minimizes the total edge weight:
$$\omega(T) = \sum_{e \in E_T} \omega(e)$$

```
        The Cut Property (Green Rule)                The Cycle Property (Red Rule)
       [ Set S ]          [ Set V - S ]                   (u) ────── 2 ──────► (v)
       ┌───────┐          ┌───────────┐                    ▲                   │
       │  (u)  │──────────│    (v)    │ Light Edge        1│                   │9 (Max weight)
       └───────┘  e=(u,v) └───────────┘ MUST be in MST     │                   ▼
           \               /                              (x) ◄───── 3 ─────── (w)
            \─────────────/                              Edge (v,w)=9 CANNOT be in MST!
```

### 1. The Cut Property (Light Edge Theorem):
Let $S \subset V$ be any proper subset of vertices, defining a **cut** $(S, V \setminus S)$. An edge crosses the cut if one endpoint is in $S$ and the other is in $V \setminus S$.
> **Theorem:** If edge $e = (u, v)$ is a **strictly light edge** crossing cut $(S, V \setminus S)$ (i.e., its weight is strictly smaller than every other edge crossing the cut), then $e$ **must belong to every Minimum Spanning Tree of $G$**.

### 2. The Cycle Property:
Let $C$ be any simple cycle in graph $G$.
> **Theorem:** If edge $e = (u, v)$ is the **strictly heaviest edge on cycle $C$**, then $e$ **cannot belong to any Minimum Spanning Tree of $G$**.

*Proof:* Suppose $e \in T$ for some MST $T$. Removing $e$ splits $T$ into two disconnected trees $T_1$ and $T_2$, defining a cut $(V(T_1), V(T_2))$. Cycle $C$ must cross this cut at least twice (since it contains $e$ and is a closed loop). Therefore, there exists another edge $e' \in C$ crossing this cut with $e' \neq e$. Reconnecting $T_1$ and $T_2$ with $e'$ creates a new spanning tree $T' = (T \setminus \{e\}) \cup \{e'\}$. Since $e$ was the heaviest edge on $C$, $\omega(e') < \omega(e) \implies \omega(T') < \omega(T)$, contradicting the optimality of $T$! $\blacksquare$

---

## Kruskal's Algorithm & The Disjoint-Set / Union-Find Role (ClassPractice Q2)

**Exam Question (ClassPractice Quiz-2, Q2):**
> *A union-find data-structure is commonly applied while implementing:*
> *(a) A depth-first search traversal of a graph.*
> *(b) Computing the minimum spanning tree of a graph using the Prim's algorithm.*
> *(c) Computing the minimum spanning tree of a graph using the Kruskal algorithm.*
> *(d) Computing the all-pairs shortest path in a graph.*
> **Answer: (c).**

### Why Kruskal's Algorithm Mandates Union-Find:
Kruskal's algorithm greedily examines edges in increasing order of weight.
For each candidate edge $(u, v) \in E$:
1. If $u$ and $v$ already belong to the same connected component, adding $(u, v)$ would introduce a cycle.
2. If $u$ and $v$ reside in different components, edge $(u, v)$ safely merges them without forming a cycle.

The **Disjoint-Set (Union-Find)** data structure provides near-instantaneous operations:
- `MAKE-SET(v)`: Initializes $|V|$ disjoint components in $O(1)$.
- `FIND-SET(u)`: Returns the representative root of the component containing $u$.
- `UNION(u, v)`: Merges the components of $u$ and $v$.

```text
KRUSKAL-MST(G, w):
    A = ∅
    for each vertex v ∈ G.V do
        MAKE-SET(v)
        
    sort the edges of G.E into non-decreasing order by weight w
    
    for each edge (u, v) ∈ G.E taken in sorted order do
        if FIND-SET(u) != FIND-SET(v) then
            A = A ∪ {(u, v)}
            UNION(u, v)
            if |A| == |G.V| - 1 then
                break // Early exit: spanning tree complete!
                
    return A
```

### Complexity Breakdown:
- Edge sorting: $O(E \log E) = O(E \log V)$.
- Disjoint-set operations ($V$ `MAKE-SET`, $2E$ `FIND-SET`, and $V - 1$ `UNION`):
  With **Path Compression** and **Union by Rank**, each operation takes amortized $O(\alpha(V))$, where $\alpha$ is the inverse Ackermann function ($\alpha(V) \le 4$ for all practical universe sizes).
  Total Union-Find time: $O(E \cdot \alpha(V))$.
- **Total Time Complexity:**
  $$T(V, E) = O(E \log V + E \alpha(V)) = \mathbf{O(E \log V)}$$

---

## ClassPractice Q3: Step-by-Step Kruskal Trace on 7-Vertex Wheel Graph

**Exam Problem (ClassPractice Quiz-2, Q3):**
> *Which is the correct order for Kruskal's minimum spanning tree algorithm to add edges to the minimum spanning tree for the figure shown below:*
> *(a) $(A, D)$ then $(E, G)$ then $(B, D)$ then $(D, E)$ then $(F, G)$ then $(A, C)$.*
> *(b) $(A, B)$ then $(A, C)$ then $(A, D)$ then $(D, E)$ then $(C, F)$ then $(D, G)$.*
> *(c) Both A and B*
> *(d) None of above*

```
                 (A) ─────── 20 ─────── (B)
                /   \                 /   \
              23/     \1             /4    \15
              /         \           /       \
            (C) ── 36 ── (D) ── 9 ── (E)
              \         / \         /
              28\     /25  \16    /3
                \   /       \   /
                 (F) ─────── 17 ─────── (G)
```

### Edge List Sorted in Ascending Order:
1. $(A, D) = 1$
2. $(E, G) = 3$
3. $(B, D) = 4$
4. $(D, E) = 9$
5. $(B, E) = 15$
6. $(D, G) = 16$
7. $(F, G) = 17$
8. $(A, B) = 20$
9. $(A, C) = 23$
10. $(D, F) = 25$
11. $(C, F) = 28$
12. $(C, D) = 36$

### Kruskal Step-by-Step Execution:
- **Initial Components:** $\{A\}, \{B\}, \{C\}, \{D\}, \{E\}, \{F\}, \{G\}$. ($|V| = 7$, need $7 - 1 = 6$ edges).
- **Edge 1: $(A, D) = 1$**
  - $\text{FIND}(A) \neq \text{FIND}(D) \implies$ **ACCEPTED (Edge 1)**.
  - Components: $\{A, D\}, \{B\}, \{C\}, \{E\}, \{F\}, \{G\}$.
- **Edge 2: $(E, G) = 3$**
  - $\text{FIND}(E) \neq \text{FIND}(G) \implies$ **ACCEPTED (Edge 2)**.
  - Components: $\{A, D\}, \{B\}, \{C\}, \{E, G\}, \{F\}$.
- **Edge 3: $(B, D) = 4$**
  - $\text{FIND}(B) \neq \text{FIND}(D) \implies$ **ACCEPTED (Edge 3)**.
  - Components: $\{A, B, D\}, \{C\}, \{E, G\}, \{F\}$.
- **Edge 4: $(D, E) = 9$**
  - $\text{FIND}(D) \neq \text{FIND}(E) \implies$ **ACCEPTED (Edge 4)**.
  - Merges $\{A, B, D\}$ and $\{E, G\} \implies \{A, B, D, E, G\}, \{C\}, \{F\}$.
- **Edge $(B, E) = 15$:**
  - $B$ and $E$ are already in the same component! Adding $(B, E)$ creates cycle $B - D - E - B$.
  - $\implies$ **REJECTED (Cycle detected)**.
- **Edge $(D, G) = 16$:**
  - $D$ and $G$ are already in the same component! Adding $(D, G)$ creates cycle $D - E - G - D$.
  - $\implies$ **REJECTED (Cycle detected)**.
- **Edge 5: $(F, G) = 17$**
  - $\text{FIND}(F) \neq \text{FIND}(G) \implies$ **ACCEPTED (Edge 5)**.
  - Merges $\{F\} \implies \{A, B, D, E, G, F\}, \{C\}$.
- **Edge $(A, B) = 20$:**
  - $A$ and $B$ are already in the same component! Adding $(A, B)$ creates cycle $A - D - B - A$.
  - $\implies$ **REJECTED (Cycle detected)**.
- **Edge 6: $(A, C) = 23$**
  - $\text{FIND}(A) \neq \text{FIND}(C) \implies$ **ACCEPTED (Edge 6)**.
  - Merges $\{C\} \implies \{A, B, C, D, E, F, G\}$.
  - Tree has $6 = |V| - 1$ edges $\implies$ **ALGORITHM TERMINATES!**

> [!IMPORTANT]
> **Result:** Kruskal adds edges in the exact sequence:
> $$\mathbf{(A, D) \to (E, G) \to (B, D) \to (D, E) \to (F, G) \to (A, C)}$$
> Total MST Weight $= 1 + 3 + 4 + 9 + 17 + 23 = \mathbf{57}$.
> **Correct Option:** **(a)**.

---

## ClassPractice Q1: Maximum Spanning Tree in $O(E \log V)$

**Exam Question (ClassPractice Quiz-2, Q1):**
> *A maximum spanning tree (i.e., a spanning tree that maximizes the sum of the weights) can be constructed in:*
> *(a) $O(E \log V)$*
> *(b) $O(E)$*
> *(c) $O(E \log E)$*
> *(d) None of these*
> **Answer: (a) and (c) are both correct ($O(E \log E) = O(E \log V)$).**

### Proof:
To find a maximum spanning tree, we can:
1. Negate all edge weights: $\omega'(e) = -\omega(e)$.
2. Run standard Kruskal's or Prim's algorithm to find the minimum spanning tree of $\omega'$.
   $$\min \sum_{e \in T} (-\omega(e)) = -\max \sum_{e \in T} \omega(e)$$
3. Alternatively, sort edges in **non-increasing** order in Kruskal's algorithm and greedily pick edges that do not form a cycle.
- In either case, the algorithm performs standard sorting in $O(E \log E) = O(E \log V)$ followed by $O(E \alpha(V))$ Union-Find operations.
- Total Time: $\mathbf{O(E \log V)}$.

---

## ClassPractice Q4: Negative Weights, Reverse-Delete, and Doubling Edge Costs

**Exam Question (ClassPractice Quiz-2, Q4):**
> *Select correct statements:*
> *(a) Prim's and Kruskal's algorithm can also produce a MST for a graph whose weights can be positive or negative.*
> *(b) Consider a reversed Kruskal's algorithm for computing a MST. Initialize $T$ to be the set of all edges in the graph. Now, consider edges from largest to smallest cost. For each edge, delete it from $T$ if that edge belongs to a cycle in $T$. Assuming all the edge costs are distinct, this new algorithm correctly computes MST.*
> *(c) Suppose we have computed a minimum spanning tree of a graph and its weight. If we make a new graph by doubling the weight of every edge in original graph, we still need $\Omega(E)$ time to compute the cost of the MST of new graph.*
> *(d) All of above*

### Rigorous Analysis:

#### Statement (a): TRUE
- **Why it works with negative weights:** Unlike Dijkstra's algorithm (which fundamentally breaks with negative edges due to cumulative path lengths), **MST algorithms rely purely on the Cut Property and Cycle Property**.
- The Cut Property states: *For any cut, the lightest crossing edge is safe.* Whether the edge weights are positive, zero, or negative, an edge of weight $-10$ is strictly lighter than an edge of weight $-3$ or $+5$.
- Adding an arbitrary constant $C$ to every edge preserves all relative orderings ($\omega(e_1) < \omega(e_2) \iff \omega(e_1) + C < \omega(e_2) + C$). Thus, Prim and Kruskal compute correct MSTs regardless of weight signs!

#### Statement (b): TRUE (The Reverse-Delete Algorithm)
- This is the classic **Reverse-Delete Algorithm** (Kruskal in reverse):
  1. Start with $T = E$.
  2. Iterate through edges in descending order of cost (largest to smallest).
  3. If deleting $e$ leaves $T$ connected, $e$ was part of a cycle and was the heaviest edge on that cycle.
- By the **Cycle Property**, the heaviest edge on any cycle cannot belong to an MST. Deleting it is provably safe and optimal.

#### Statement (c): FALSE
- Let $T$ be the known MST of $G$ with total weight $W = \sum_{e \in T} \omega(e)$.
- In the new graph $G'$, every edge has weight $\omega'(e) = 2 \cdot \omega(e)$.
- Because multiplying by a positive constant $2 > 0$ is a **strictly monotonic transformation**, the relative ordering of all edge weights is completely unchanged:
  $$\omega(e_1) < \omega(e_2) \iff 2\omega(e_1) < 2\omega(e_2)$$
- Therefore, the **exact same edge set $T$ forms the MST in $G'$**!
- The cost of the new MST is simply:
  $$W' = \sum_{e \in T} 2\omega(e) = 2 \sum_{e \in T} \omega(e) = \mathbf{2 \cdot W}$$
- This computation requires **$O(1)$ constant time**, NOT $\Omega(E)$ time!

> [!NOTE]
> **Correct Option for Q4:** **(a) and (b)** are correct statements. (Option (d) is false because (c) is false).

---

## ClassPractice Q11: Weight Squaring Invariance & Acyclic Spanning Tree Proof

**Exam Question (ClassPractice Quiz-2, Q11):**
> *Which of the following statements are true:*
> *(a) Given a graph $G$ with unique positive edges, with a spanning tree $T$, MST will be the same on squaring all edges.*
> *(b) Given a graph $G$ with positive edges, with a spanning tree $T$, MST will be the same on squaring all edges.*
> *(c) Given that graph $G$ has positive as well as negative weight edges, the minimum spanning tree may have a cycle.*
> *(d) Given that graph $G$ has positive as well as negative weight edges, the minimum spanning tree never consists of a cycle.*

### Mathematical Proofs:

#### Statements (a) and (b): BOTH TRUE
- Let $f(x) = x^2$. For all positive edge weights $x > 0$, the function $f(x)$ is **strictly monotonically increasing**:
  $$x_1 < x_2 \iff x_1^2 < x_2^2$$
  $$x_1 = x_2 \iff x_1^2 = x_2^2$$
- In Kruskal's algorithm, edge selection depends *only* on the sorting permutation of edges.
  Since $w(e_1) \le w(e_2) \iff w(e_1)^2 \le w(e_2)^2$, the exact same sorting order and tie-breaking choices apply.
- In Prim's algorithm, the minimum edge crossing each cut remains the minimum edge because $w(e)$ being minimal implies $w(e)^2$ is minimal.
- Thus, squaring positive edge weights **guarantees that the MST remains identical** (both for unique weights and non-unique weights with identical tie-breaking).

#### Statement (c): FALSE & Statement (d): TRUE
- A **Spanning Tree** by definition is a connected, **acyclic** graph spanning all vertices.
- Even if edge weights are negative, adding an edge that forms a cycle violates the definition of a tree.
- An MST can never consist of a cycle! Thus, (d) is TRUE and (c) is FALSE.

---

## ClassPractice Q12: Complete Graph $K_4$ MST Edge Sum Optimization

**Exam Problem (ClassPractice Quiz-2, Q12):**
> *Consider a complete graph (All pair of vertices connected) $G$ with 4 nodes as shown below. Given that all edges are positive weight with one edge having weight 4, if the Minimum Spanning Tree has weight 10, fill in the blank with the minimum possible value of the sum of all edges of graph $G$.*

```
                 (1) ──────── (2)
                  │ \        / │
                  │   \    /   │
                  │     ╳      │
                  │   /    \   │
                  │ /        \ │
                 (3) ──────── (4)
```

### Problem Parameters:
- Complete graph $K_4$: $|V| = 4, \; |E| = \binom{4}{2} = 6$.
- All edge weights are strictly positive: $\omega(e) > 0$.
- One distinguished edge in $G$ has weight $\mathbf{4}$.
- The MST has $|V| - 1 = 3$ edges with total weight:
  $$\omega(T) = e_1 + e_2 + e_3 = 10$$
- We seek the **minimum possible total sum of all 6 edges** in $G$:
  $$\min \sum_{e \in E} \omega(e) = 10 + \min(e_4 + e_5 + e_6)$$

---

### Structural Analysis via Kruskal's Algorithm & The Cycle Property:
In $K_4$, any 3 edges form an MST unless they form a triangle ($K_3$).
For $T = \{e_1, e_2, e_3\}$ to be an MST, every non-tree edge $e \notin T$ must satisfy the **Cycle Property**:
$$\omega(e) \ge \max_{e' \in \text{cycle}(T \cup \{e\})} \omega(e')$$

#### Case A: No edge is rejected before Kruskal completes the MST
- Kruskal selects the 3 smallest edges in $G$: $w_1 \le w_2 \le w_3$.
- MST weight: $w_1 + w_2 + w_3 = 10$.
- The 3 remaining non-tree edges must satisfy:
  $$w_4 \ge w_3, \quad w_5 \ge w_3, \quad w_6 \ge w_3$$
- The sum of all 6 edges is:
  $$\sum \omega = 10 + w_4 + w_5 + w_6 \ge 10 + 3w_3$$
- Since one edge in $G$ has weight 4:
  - If $w_3 = 4$, then $w_4 = w_5 = w_6 = 4$, giving $\sum \omega = 10 + 3(4) = \mathbf{22}$.
  - If $w_4 = 4$, then $w_3 \le 4$, so $w_5 \ge 4, w_6 \ge 4$, giving $\sum \omega \ge 10 + 4 + 4 + 4 = \mathbf{22}$.

#### Case B: One edge IS rejected by Kruskal because it forms a triangle
Can a non-tree edge have weight smaller than $w_3$?
**YES!** If three vertices $\{A, B, C\}$ form a triangle:
- Kruskal picks edge $(A, B) = w_1$ and edge $(B, C) = w_2$.
- The chord edge $(A, C)$ completes the cycle $A - B - C - A$.
- By the Cycle Property, Kruskal rejects $(A, C)$ if:
  $$\omega(A, C) \ge \max(w_1, w_2) = w_2$$
- Now Kruskal moves to connect the 4th vertex $D$, selecting edge $(C, D) = w_3$.
- If $w_2 \le \omega(A, C) < w_3$, edge $(A, C)$ was rejected **even though its weight is smaller than $w_3$**!
- The other two non-tree edges $(A, D)$ and $(B, D)$ connect to vertex $D$. Their fundamental cycles must use edge $w_3$, so:
  $$\omega(A, D) \ge w_3 \quad \text{and} \quad \omega(B, D) \ge w_3$$
- Total sum of all 6 edges:
  $$\sum \omega = 10 + \omega(A, C) + \omega(A, D) + \omega(B, D) \ge 10 + w_2 + 2w_3$$

#### Numerical Optimization:
We want to minimize $10 + w_2 + 2w_3$ subject to:
1. $w_1 + w_2 + w_3 = 10$ with $0 < w_1 \le w_2 \le w_3$.
2. One edge in $G$ has weight 4.
3. For standard discrete integer weights:
   - To make $w_3$ as small as possible while satisfying $w_1 + w_2 + w_3 = 10$, set $w_3 = 4$ (the given edge!).
   - Then $w_1 + w_2 = 10 - 4 = 6$.
   - Since $w_1 \le w_2$, the smallest valid integer for $w_2$ is $w_2 = 3$ (with $w_1 = 3$).
   - Then rejected edge $\omega(A, C) \ge w_2 = 3 \implies \omega(A, C) = 3$.
   - Non-tree edges $(A, D)$ and $(B, D)$ must be $\ge w_3 = 4 \implies \omega(A, D) = 4, \; \omega(B, D) = 4$.
- **Edge Weight Assignment:**
  - Tree edges: $(A, B) = 3, \; (B, C) = 3, \; (C, D) = 4$. (MST cost $= 3 + 3 + 4 = 10$).
  - Non-tree edges: $(A, C) = 3, \; (A, D) = 4, \; (B, D) = 4$.
  - Notice that edge $(C, D)$ has weight 4 (satisfying "one edge has weight 4").
- **Sum of All 6 Edges:**
  $$\sum_{e \in E} \omega(e) = 3 + 3 + 4 + 3 + 4 + 4 = \mathbf{21}$$

> [!IMPORTANT]
> **Final Answer to ClassPractice Q12:**
> The minimum possible value of the sum of all edges of graph $G$ is **21** (achieved via triangle rejection with integer weights $3, 3, 4, 3, 4, 4$). If no cycle rejection occurs before $w_3$, the minimum sum is **22**.

---

## ClassPractice Q16: Deep Analysis of Modified MST Algorithms (MAYBE-MST-A, B, C)

**Exam Problem (ClassPractice Quiz-2, Q16):**
> *State True/False if each of the modified MST algorithms gives a valid MST of a given graph $G$.*

```text
a. MAYBE-MST-A(G, w):
1   sort the edges into nonincreasing order of edge weights w
2   T = E
3   for each edge e, taken in nonincreasing order by weight
4       if T - {e} is a connected graph
5           T = T - {e}
6   return T
```
> **Answer: TRUE.**
> **Justification:** This is the famous **Reverse-Delete Algorithm**. It starts with all edges and removes cycle edges from heaviest to lightest. By the Cycle Property, the heaviest edge in any cycle cannot belong to an MST. Because the edges are considered in non-increasing order, the first edge removed from any cycle is guaranteed to be the maximum-weight edge of that cycle. Thus, every removed edge is provably absent from the MST. When no more edges can be removed without disconnecting the graph, the remaining graph is connected and acyclic (a spanning tree) and is guaranteed to be an MST.

---

```text
b. MAYBE-MST-B(G, w):
1   T = ∅
2   for each edge e, taken in arbitrary order
3       if T ∪ {e} has no cycles
4           T = T ∪ {e}
5   return T
```
> **Answer: FALSE.**
> **Justification:** This algorithm processes edges in **arbitrary order** rather than sorted order. It simply finds an **arbitrary spanning tree**, which is almost never minimal!
> **Minimal 3-Vertex Counterexample:**
> Consider a triangle graph $K_3$ with vertices $\{A, B, C\}$ and edge weights:
> $\omega(A, B) = 1, \; \omega(B, C) = 2, \; \omega(A, C) = 100$.
> - True MST: edges $\{(A, B), (B, C)\}$ with total cost $1 + 2 = \mathbf{3}$.
> - Suppose `MAYBE-MST-B` encounters edge $(A, C) = 100$ first.
>   - $T \cup \{(A, C)\}$ has no cycles $\implies T = \{(A, C)\}$.
> - Next, it encounters $(B, C) = 2$:
>   - $T \cup \{(B, C)\}$ has no cycles $\implies T = \{(A, C), (B, C)\}$.
> - Finally, it encounters $(A, B) = 1$:
>   - $T \cup \{(A, B)\}$ forms cycle $A - B - C - A \implies$ rejected!
> - Algorithm returns $T = \{(A, C), (B, C)\}$ with total cost $100 + 2 = \mathbf{102}$!
> - **Catastrophic failure:** Computed tree has cost 102 vs. optimal cost 3!

---

```text
c. MAYBE-MST-C(G, w):
1   T = ∅
2   for each edge e, taken in arbitrary order
3       T = T ∪ {e}
4       if T has a cycle c
5           let e' be a maximum-weight edge on c
6           T = T - {e'}
7   return T
```
> **Answer: TRUE.**
> **Justification:** This algorithm dynamically maintains a cycle-free forest. Whenever adding an edge $e$ creates a cycle $c$, it immediately identifies the **maximum-weight edge $e'$ on cycle $c$ and deletes it**. By the **Cycle Property (Red Rule)**, the heaviest edge on any cycle cannot be part of the MST. Therefore, dropping $e'$ never removes any necessary MST edge. When all edges have been processed, $T$ spans all vertices, contains no cycles, and contains no suboptimal edges. Thus, $T$ is provably an optimal Minimum Spanning Tree!

---

# 8. Complexity Analysis & Balanced Tree Motivations

## Worst-Case vs. Average-Case Complexity ($O(h)$ Analysis)

Every fundamental dynamic set operation on a BST—`Search`, `Minimum`, `Maximum`, `Successor`, `Predecessor`, `Insert`, and `Delete`—runs in time proportional to the height $h$:
$$T(n) = O(h)$$

| Data Structure / Operation | Search | Insert | Delete | Minimum / Successor |
| :--- | :--- | :--- | :--- | :--- |
| **Sorted Array** | $O(\log n)$ (Binary Search) | $O(n)$ (Shifting) | $O(n)$ (Shifting) | $O(1)$ |
| **Singly Linked List** | $O(n)$ | $O(1)$ (at head) | $O(n)$ | $O(n)$ |
| **BST (Balanced)** | **$O(\log n)$** | **$O(\log n)$** | **$O(\log n)$** | **$O(\log n)$** |
| **BST (Worst-Case Skewed)** | $O(n)$ | $O(n)$ | $O(n)$ | $O(n)$ |

---

## Randomly Built BSTs ($O(\log n)$ Expected Height)
If keys are inserted in random uniform order:
$$\mathbb{E}[h] = \Theta(\log n)$$
On average, a basic BST performs searches, insertions, and deletions in logarithmic time.

---

## Pathological Degeneracy (Sorted Input Pitfall)

If elements arrive in **strictly sorted order** ($1, 2, 3, 4, 5$):
- Each new node is inserted as the right child of the previous node.
- The tree collapses into a linear chain of height $h = n - 1$.
- Search time degrades from $O(\log n)$ to **linear $O(n)$ time**!

---

## Overview of Self-Balancing Trees (AVL, Red-Black Trees)

To enforce $h = O(\log n)$ unconditionally against adversarial inputs:
1. **AVL Trees:** Enforce strict balance factor $\in \{-1, 0, 1\}$. Height is strictly $< 1.44 \log_2 n$. Uses single and double tree rotations.
2. **Red-Black Trees:** Enforce balance using node colors (Red/Black) and black-height properties. Height is guaranteed $\le 2 \log_2(n + 1)$. Used internally in C++ `std::map`, Java `TreeMap`.

---

# 9. Exhaustive Conceptual True / False Question Bank (With Deep Justifications)

---

### Q1: The height of a binary search tree containing $n$ elements is always $O(\log n)$.
> **Answer: FALSE.**
> **Justification:** If elements are inserted in sorted order, the BST degenerates into a linear chain of height $h = n - 1 = \Theta(n)$.

---

### Q2: Inorder traversal of a binary search tree visits nodes in non-decreasing sorted key order.
> **Answer: TRUE.**
> **Justification:** By the Inorder Tree Walk Theorem, recursively visiting the left subtree ($\le x.key$), printing $x.key$, and then visiting the right subtree ($\ge x.key$) guarantees monotonic output.

---

### Q3: A binary tree can always be uniquely reconstructed from its Preorder and Postorder traversals alone.
> **Answer: FALSE.**
> **Justification:** If an internal node has only one child, it is impossible to determine whether that child is a left child or a right child from Preorder and Postorder sequences alone.

---

### Q4: In a full binary tree with $L$ leaves, the total number of internal nodes is $L - 1$.
> **Answer: TRUE.**
> **Justification:** By the Full Binary Tree Leaf Theorem, $L = I + 1$, which algebraically implies $I = L - 1$.

---

### Q5: In a binary search tree, the successor of a node with a non-empty right subtree is always a leaf node.
> **Answer: FALSE.**
> **Justification:** The successor is the minimum node in the right subtree (`TREE-MINIMUM(x.right)`). While it cannot have a left child, it **can have a right child**, so it is not necessarily a leaf.

---

### Q6: Deleting a node with two children from a BST can be accomplished by replacing it with either its inorder successor or its inorder predecessor.
> **Answer: TRUE.**
> **Justification:** Both the inorder successor and predecessor preserve the BST invariant when moved to the deleted node's position.

---

### Q7: The predecessor of a node in a BST can have a right child.
> **Answer: FALSE.**
> **Justification:** When the left subtree exists, the predecessor is the maximum of the left subtree (`TREE-MAXIMUM(x.left)`). The maximum node in any subtree cannot have a right child, by definition.

---

### Q8: In a complete binary tree of height $h$, all leaves reside at depth $h$.
> **Answer: FALSE.**
> **Justification:** This defines a **perfect** binary tree. In a complete binary tree, leaves can reside at both depth $h$ and depth $h - 1$.

---

### Q9: The maximum number of nodes in a binary tree of depth $d$ is $2^{d+1} - 1$.
> **Answer: TRUE.**
> **Justification:** Summing the geometric series $\sum_{i=0}^d 2^i = 2^{d+1} - 1$.

---

### Q10: Searching in an AVL tree has worst-case time complexity $O(\log n)$.
> **Answer: TRUE.**
> **Justification:** AVL trees maintain balance factors ensuring the height never exceeds $1.44 \log_2 n$, guaranteeing $O(\log n)$ worst-case search time.

---

### Q11: An iterative search in a BST uses less stack memory than a recursive search.
> **Answer: TRUE.**
> **Justification:** Iterative search uses $O(1)$ auxiliary space, whereas recursive search consumes $O(h)$ frames on the execution call stack.

---

### Q12: In a full binary tree, every node has an even degree.
> **Answer: FALSE.**
> **Justification:** In a full binary tree, internal nodes have degree 2 (two children), but leaves have degree 0. Both 0 and 2 are even numbers, but the root has no parent, so in terms of graph degree, leaves have degree 1 (odd) and internal nodes have degree 3 (odd).

---

### Q13: A binary tree with $n$ nodes has exactly $n + 1$ `NIL` child pointers.
> **Answer: TRUE.**
> **Justification:** A binary tree with $n$ nodes has $2n$ total child pointers. Exactly $n - 1$ pointers point to actual child nodes (all nodes except the root). Thus, $2n - (n - 1) = n + 1$ pointers are `NIL`.

---

### Q14: If we know the Inorder and Preorder traversals of a general binary tree, the tree is uniquely determined.
> **Answer: TRUE.**
> **Justification:** Preorder identifies the root, and Inorder splits the left and right subtrees, allowing unique recursive construction.

---

### Q15: In a BST, the minimum element never has a left child.
> **Answer: TRUE.**
> **Justification:** If it had a left child, that left child would have a strictly smaller key, contradicting the definition of the minimum element.

---

### Q16: Deleting a node from a BST can increase the height of the tree.
> **Answer: FALSE.**
> **Justification:** Standard BST deletion removes or splices out a node; it never moves any node to a deeper level than its original depth.

---

### Q17: A complete binary tree with $n$ nodes can be stored efficiently in an array without using explicit left and right child pointers.
> **Answer: TRUE.**
> **Justification:** In an array representation (1-indexed), the left child of node $i$ is at $2i$ and the right child is at $2i + 1$, eliminating pointer overhead.

---

### Q18: Inserting $n$ keys in random order into an initially empty BST takes $O(n \log n)$ expected time.
> **Answer: TRUE.**
> **Justification:** The expected height of a randomly built BST is $O(\log n)$, so each of the $n$ insertions takes $O(\log n)$ expected time, totaling $O(n \log n)$.

---

### Q19: If node $x$ is an ancestor of node $y$ in a BST, then $x.key$ must be greater than $y.key$.
> **Answer: FALSE.**
> **Justification:** $x.key$ is greater than $y.key$ only if $y$ resides in the left subtree of $x$. If $y$ resides in the right subtree of $x$, $x.key < y.key$.

---

### Q20: Postorder traversal can be used to compute the space used by a directory hierarchy on a disk.
> **Answer: TRUE.**
> **Justification:** Computing the total size of a directory requires first summing the sizes of all its subdirectories (children) before computing the parent's size, which matches postorder traversal.

---

### Q21: In a BST with unique keys, the successor of a node $x$ can have a key smaller than $x.key$.
> **Answer: FALSE.**
> **Justification:** By definition, the successor is the smallest key strictly greater than $x.key$.

---

### Q22: A binary search tree is always a complete binary tree.
> **Answer: FALSE.**
> **Justification:** BST ordering constraints govern keys, not tree shape. A BST can be completely skewed and incomplete.

---

### Q23: The number of distinct binary trees that can be formed using $n$ unlabeled nodes is given by the $n$-th Catalan number $C_n = \frac{1}{n+1}\binom{2n}{n}$.
> **Answer: TRUE.**
> **Justification:** This is a fundamental combinatorial theorem of binary tree topologies.

---

### Q24: In a BST, if node $z$ has two children, its successor $y$ has at most one child.
> **Answer: TRUE.**
> **Justification:** Successor $y = \text{TREE-MINIMUM}(z.right)$. As the minimum in a subtree, $y$ cannot have a left child ($y.left == \text{NIL}$).

---

### Q25: Breadth-First Search on a tree visits nodes in the exact same sequence as Level-Order Traversal.
> **Answer: TRUE.**
> **Justification:** Both algorithms use a FIFO queue to explore nodes level-by-level starting from the root.

---

### Q26: A Maximum Spanning Tree can be computed in $O(E \log V)$ time using Kruskal's or Prim's algorithm. (ClassPractice Q1)
> **Answer: TRUE.**
> **Justification:** By sorting edges in non-increasing order or negating all edge weights ($\omega' = -\omega$) and finding the MST, standard Kruskal's or Prim's algorithms find the Maximum Spanning Tree in $O(E \log V)$ time.

---

### Q27: The Disjoint-Set (Union-Find) data structure is used in Prim's algorithm to grow the spanning tree. (ClassPractice Q2)
> **Answer: FALSE.**
> **Justification:** Prim's algorithm uses a **Min-Priority Queue** to pick the minimum-weight edge connecting the growing tree to unvisited vertices. It is **Kruskal's algorithm** that uses the Union-Find data structure to track connected components and prevent cycles.

---

### Q28: If a connected graph contains negative edge weights but no negative cycles, Kruskal's algorithm may fail to compute a valid Minimum Spanning Tree. (ClassPractice Q4a)
> **Answer: FALSE.**
> **Justification:** Unlike shortest-path algorithms (which accumulate path lengths), MST correctness relies exclusively on the **Cut Property** and **Cycle Property**. Relative ordering between edges is preserved regardless of sign, so Kruskal and Prim correctly compute the MST on any real-weighted graph (positive, zero, or negative).

---

### Q29: If we have already computed the MST of a graph $G$, and a new graph $G'$ is formed by doubling every edge weight, finding the cost of the new MST requires $\Omega(E)$ time. (ClassPractice Q4c)
> **Answer: FALSE.**
> **Justification:** Multiplying all edge weights by 2 is a strictly monotonic transformation that preserves all relative edge weight inequalities. The exact same edge set forms the MST in $G'$, and its cost is simply $2 \times \text{weight}(T)$, computable in $O(1)$ constant time!

---

### Q30: A segment tree built over an array of $N$ elements requires $O(N \log N)$ time to construct and $O(N \log N)$ memory space. (ClassPractice Q8)
> **Answer: FALSE.**
> **Justification:** A segment tree has $N$ leaves and $N - 1$ internal nodes ($2N - 1$ total nodes). Merging takes $O(1)$ per internal node, yielding a linear build time of $\Theta(N)$. Using an implicit array representation, at most $4N$ elements are allocated, so space is strictly $\Theta(N)$.

---

### Q31: Given a graph $G$ with positive edge weights and a minimum spanning tree $T$, squaring all edge weights guarantees that $T$ remains a minimum spanning tree of the modified graph. (ClassPractice Q11)
> **Answer: TRUE.**
> **Justification:** For all $x > 0$, the function $f(x) = x^2$ is strictly monotonically increasing ($u < v \iff u^2 < v^2$). Therefore, the relative ordering of all edge weights and cut-crossings is perfectly preserved, leaving the MST unchanged.

---

### Q32: Initializing $T = \emptyset$ and adding edges in arbitrary order as long as they do not form a cycle (`MAYBE-MST-B`) correctly computes a Minimum Spanning Tree. (ClassPractice Q16b)
> **Answer: FALSE.**
> **Justification:** Processing edges in arbitrary order finds an **arbitrary spanning tree**, not a minimum spanning tree. For example, in a triangle with edge weights 1, 2, and 100, if edge 100 is processed first, it will be added to $T$, resulting in a suboptimal spanning tree of cost 102 instead of 3.

