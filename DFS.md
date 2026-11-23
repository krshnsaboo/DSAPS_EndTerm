# Depth-First Search (DFS): The Definitive Master Guide
## Advanced Algorithmic Reasoning, Failure Analyses & Exam Slip-Test Deep-Dives

---

## Table of Contents
1. [Core Mechanics: How and Why DFS Works](#1-core-mechanics-how-and-why-dfs-works)
   - [Exploration Philosophy: Deep Plunges & Backtracking](#exploration-philosophy-deep-plunges--backtracking)
   - [The Three-Color State Machine (White, Grey, Black)](#the-three-color-state-machine-white-grey-black)
   - [Discrete Global Timestamps ($d[u]$ and $f[u]$)](#discrete-global-timestamps-du-and-fu)
   - [The Structural Invariant of Grey Vertices (Active Call Stack)](#the-structural-invariant-of-grey-vertices-active-call-stack)
   - [Recursive Implementation vs. True Iterative Simulation](#recursive-implementation-vs-true-iterative-simulation)
2. [Why DFS Fails for Shortest Distances (Slip Test Question 1 Deep-Dive)](#2-why-dfs-fails-for-shortest-distances-slip-test-question-1-deep-dive)
   - [The Proposed Algorithm & Student's Fallacy](#the-proposed-algorithm--students-fallacy)
   - [Part (a): Smallest Possible Counterexample (3 Vertices)](#part-a-smallest-possible-counterexample-3-vertices)
   - [Part (b): Why BFS Fixes the Flaw (Queue Monotonicity & Level Sets)](#part-b-why-bfs-fixes-the-flaw-queue-monotonicity--level-sets)
   - [Part (c): What if DFS Dynamically Updates Visited Nodes Until Convergence?](#part-c-what-if-dfs-dynamically-updates-visited-nodes-until-convergence)
3. [Core Theorems & Rigorous Structural Proofs](#3-core-theorems--rigorous-structural-proofs)
   - [The Parenthesis Theorem & Interval Nesting](#the-parenthesis-theorem--interval-nesting)
   - [Descendant Nesting Corollary](#descendant-nesting-corollary)
   - [The White-Path Theorem](#the-white-path-theorem)
   - [The Undirected DFS Leaf Theorem (ClassPractice Question 7)](#the-undirected-dfs-leaf-theorem-classpractice-question-7)
4. [Edge Classification & Color-Based Identification](#4-edge-classification--color-based-identification)
   - [The Four Directed Edge Types: Tree, Back, Forward, Cross](#the-four-directed-edge-types-tree-back-forward-cross)
   - [Color-State Decision Table at Exploration Time](#color-state-decision-table-at-exploration-time)
   - [Why Forward and Cross Edges CANNOT Exist in Undirected DFS](#why-forward-and-cross-edges-cannot-exist-in-undirected-dfs)
5. [Advanced Algorithmic Applications of DFS](#5-advanced-algorithmic-applications-of-dfs)
   - [Cycle Detection in Directed Graphs (Back-Edge Equivalence)](#cycle-detection-in-directed-graphs-back-edge-equivalence)
   - [Bridges and Articulation Points (Cut Vertices & Low-Link Values)](#bridges-and-articulation-points-cut-vertices--low-link-values)
   - [Eulerian Circuits and Paths (Hierholzer's Algorithm)](#eulerian-circuits-and-paths-hierholzers-algorithm)
6. [Exam Scenario & Reasoning Breakdown (ClassPractice Analysis)](#6-exam-scenario--reasoning-breakdown-classpractice-analysis)
   - [Start & Finish Time Relative Inequalities (ClassPractice Question 9)](#start--finish-time-relative-inequalities-classpractice-question-9)
   - [DFS Subgraph Structure: Tree vs. Forest](#dfs-subgraph-structure-tree-vs-forest)
7. [Exhaustive Conceptual True / False Question Bank (With Deep Justifications)](#7-exhaustive-conceptual-true--false-question-bank-with-deep-justifications)

---

# 1. Core Mechanics: How and Why DFS Works

## Exploration Philosophy: Deep Plunges & Backtracking
Depth-First Search (DFS) systematically traverses a graph by exploring as deeply as possible along each branch before backtracking:
- When visiting vertex $u$, DFS selects an arbitrary unvisited neighbor $v$, establishes a directed tree edge $(u, v)$, and suspends the exploration of $u$ to begin recursively exploring $v$.
- Only when all edges incident to the current vertex have been fully explored does the algorithm **backtrack** to the predecessor from which this vertex was discovered.

---

## The Three-Color State Machine (White, Grey, Black)

DFS manages the state of every vertex $u \in V$ using a 3-coloring convention:

```
[ WHITE ] ────────────► [ GREY ] ────────────► [ BLACK ]
Undiscovered             Active on Stack          Finished & Popped
(Unvisited)              (Ancestor frontier)      (All edges scanned)
```

1. **WHITE (Undiscovered):** Initial state of all vertices. The vertex has not yet been encountered.
2. **GREY (Active / In-Progress):** The vertex has been discovered. It currently resides on the execution call stack. Its adjacency list is being actively explored.
3. **BLACK (Finished / Completed):** Every edge in the vertex's adjacency list has been completely examined. The vertex has been popped from the call stack.

---

## Discrete Global Timestamps ($d[u]$ and $f[u]$)

DFS maintains a global discrete clock variable `time`, initialized to 0:
- When vertex $u$ is first discovered (colored GREY), `time` is incremented and recorded as the **discovery timestamp**:
  $$\text{time} \leftarrow \text{time} + 1; \quad d[u] \leftarrow \text{time}$$
- When all outgoing edges of $u$ have been explored (colored BLACK), `time` is incremented and recorded as the **finish timestamp**:
  $$\text{time} \leftarrow \text{time} + 1; \quad f[u] \leftarrow \text{time}$$

### Critical Timestamp Invariants:
1. For every vertex $u$:
   $$1 \le d[u] < f[u] \le 2|V|$$
2. All $2|V|$ timestamps across the entire graph are **strictly distinct integers**.
3. Vertex $u$ is GREY strictly during the time interval $[d[u], f[u]]$.

---

## The Structural Invariant of Grey Vertices (Active Call Stack)

> **Fundamental Invariant:** At any point during the execution of DFS, the set of currently GREY vertices forms a **single directed path** in the DFS forest from the root of the current tree down to the currently active vertex:
> $$\text{Grey Vertices} = \langle v_{\text{root}} \to \dots \to u.p.p \to u.p \to u_{\text{active}} \rangle$$

### Why This Invariant Holds:
A vertex is colored GREY upon function invocation and turns BLACK only upon function return. Therefore, the set of GREY vertices corresponds **1-to-1 with the active stack frames** on the execution call stack. Since each frame was invoked by its immediate predecessor, they form a simple directed chain of ancestors.

---

## Recursive Implementation vs. True Iterative Simulation

```text
DFS(G):
    for each u ∈ G.V do
        u.color = WHITE
        u.p = NIL
    time = 0
    for each u ∈ G.V do
        if u.color == WHITE then
            DFS-VISIT(G, u)

DFS-VISIT(G, u):
    time = time + 1
    u.d = time
    u.color = GREY
    for each v ∈ G.Adj[u] do
        if v.color == WHITE then
            v.p = u
            DFS-VISIT(G, v)
    u.color = BLACK
    time = time + 1
    u.f = time
```

> [!WARNING]
> **The Naive Stack Trap:** Simply pushing unvisited neighbors onto an explicit stack does NOT simulate DFS correctly! In naive stack implementations, a node is popped and processed immediately, losing the ability to record finish timestamps $f[u]$ when all descendants finish. A correct iterative DFS must maintain an iterator over each node's adjacency list and only pop the node when all its neighbors have finished.

---

# 2. Why DFS Fails for Shortest Distances (Slip Test Question 1 Deep-Dive)

## The Proposed Algorithm & Student's Fallacy

**Exam Slip-Test Problem Statement:**
*Given an unweighted directed graph with source $s$.*
*Proposed Algorithm: Perform DFS starting from $s$. When a vertex $v$ is visited for the first time, set $dist[v] = dist[u] + 1$, where $u$ is the vertex from which $v$ was first discovered. Never update the distance of a vertex once it has been visited.*
*Student's Claim: "This computes shortest distances correctly, because DFS explores every reachable vertex and records the length of a path to it."*

---

## Part (a): Smallest Possible Counterexample (3 Vertices)

> **Claim Critique:** The student's claim is **FALSE**. DFS explores paths based on arbitrary edge orderings, greedily diving deep along long winding paths before backtracking, which can record arbitrarily suboptimal path lengths.

### Construction of the Smallest Counterexample:
Consider a directed graph with **$|V| = 3$ vertices**: $\{s, a, b\}$.
Directed edges:
$$(s, a), \quad (a, b), \quad (s, b)$$

```
          (s)
         /   \
        ▼     ▼
      (a) ──► (b)
```

- True shortest-path distance from $s$ to $b$: $\mathbf{\delta(s, b) = 1}$ via direct edge $(s, b)$.

### DFS Execution Trace:
Suppose the adjacency list of $s$ lists $a$ before $b$ ($Adj[s] = [a, b]$):
1. Start at source $s$: $dist[s] = 0$.
2. Explore edge $(s, a)$ first: Vertex $a$ is visited for the first time $\implies dist[a] = dist[s] + 1 = 1$.
3. From vertex $a$, explore edge $(a, b)$: Vertex $b$ is visited for the first time $\implies dist[b] = dist[a] + 1 = \mathbf{2}$.
4. Backtrack to $a$, then backtrack to $s$.
5. From $s$, explore edge $(s, b)$: Vertex $b$ has **already been visited**!
6. By the algorithm's rule ("Never update the distance of a vertex once visited"), edge $(s, b)$ is ignored.
7. Output: $dist[b] = 2 \neq \delta(s, b) = 1$.

**Minimum vertices required:** Exactly **3 vertices**. (With 1 or 2 vertices, any reachable path is either 0 or 1 edge, which is trivially optimal).

---

## Part (b): Why BFS Fixes the Flaw (Queue Monotonicity & Level Sets)

Replacing DFS with Breadth-First Search (BFS) guarantees 100% correctness on unweighted graphs:

### Mechanistic Reason:
1. **FIFO Queue Ordering:** BFS uses a First-In, First-Out (FIFO) queue rather than a LIFO stack.
2. **Level-by-Level Expansion:** Vertices are discovered in non-decreasing order of their true distance from $s$:
   $$Q = \langle \text{all nodes at distance } k, \; \text{all nodes at distance } k + 1 \rangle$$
3. **Queue Monotonicity:** At any point, the distances in the queue differ by at most 1:
   $$dist[v_{\text{tail}}] \le dist[v_{\text{head}}] + 1$$
4. **First Visit Optimality:** The first time BFS encounters an unvisited vertex $v$ from a dequeued vertex $u$, it traverses an edge from level $k$ to level $k + 1$. Because all possible paths of length $\le k$ have already been exhausted, no shorter path to $v$ can exist. Setting $dist[v] = dist[u] + 1$ is guaranteed to be optimal.

---

## Part (c): What if DFS Dynamically Updates Visited Nodes Until Convergence?

**Question:** *Suppose DFS may update the distance of an already visited vertex whenever a shorter path is found. If DFS continues until no distance changes occur, is the modified algorithm guaranteed to work?*

### In-Depth Mathematical Analysis:

#### 1. On Graphs WITHOUT Negative Cycles:
- **Correctness:** **YES, it is guaranteed to work.**
- **Reasoning:**
  - The distance estimate $dist[v]$ is initialized to $\infty$ (or the first discovered path) and is updated via relaxation:
    $$dist[v] \leftarrow \min(dist[v], \; dist[u] + 1)$$
  - By the Upper-Bound Property of relaxation, $dist[v] \ge \delta(s, v)$ remains an invariant.
  - Whenever $dist[v]$ is updated, it decreases strictly.
  - Since edge weights are positive (1), path lengths are lower-bounded by 0.
  - Because the set of simple paths in a finite graph is finite, the sequence of updates must terminate.
  - Upon termination, every edge satisfies the triangle inequality $dist[v] \le dist[u] + 1$, meaning $dist[v] = \delta(s, v)$.
- **Time Complexity Trap:** While correct, the running time can become **exponential $O(2^V)$** in the worst case! A DFS can explore an exponential number of paths before converging, repeatedly re-relaxing long chains of vertices.

#### 2. On Graphs WITH Negative-Weight Cycles:
- **Correctness:** **FAILS (Enters an Infinite Loop).**
- **Reasoning:** Each traversal of a negative-weight cycle decreases the path weight without bound ($-\infty$). DFS will cycle indefinitely, repeatedly updating distances, and will **never terminate**.

---

# 3. Core Theorems & Rigorous Structural Proofs

## The Parenthesis Theorem & Interval Nesting

> **Theorem (Parenthesis Theorem):**
> In any DFS of a graph $G = (V, E)$, for any two vertices $u$ and $v$, exactly one of three mutually exclusive conditions holds:
> 1. Intervals $[d[u], f[u]]$ and $[d[v], f[v]]$ are **entirely disjoint**:
>    $$d[u] < f[u] < d[v] < f[v] \quad \text{or} \quad d[v] < f[v] < d[u] < f[u]$$
>    Neither vertex is a descendant of the other in the DFS forest.
> 2. $[d[v], f[v]]$ is **strictly nested** within $[d[u], f[u]]$:
>    $$d[u] < d[v] < f[v] < f[u]$$
>    $v$ is a proper descendant of $u$ in a DFS tree.
> 3. $[d[u], f[u]]$ is **strictly nested** within $[d[v], f[v]]$:
>    $$d[v] < d[u] < f[u] < f[v]$$
>    $u$ is a proper descendant of $v$ in a DFS tree.

```
Disjoint (Separate Branches/Trees):       Nested (Descendant Relationship):
     [   u   ]       [   v   ]                 [           u           ]
   d[u]     f[u]   d[v]     f[v]                     [       v       ]
                                                   d[v]             f[v]
```

---

## Descendant Nesting Corollary
Vertex $v$ is a proper descendant of vertex $u$ in the DFS forest if and only if:
$$d[u] < d[v] < f[v] < f[u]$$

---

## The White-Path Theorem

> **Theorem:** In a DFS forest of graph $G$, vertex $v$ is a descendant of vertex $u$ if and only if at time $d[u]$ (the moment $u$ is discovered), there exists a path from $u$ to $v$ consisting **entirely of white vertices**.

### Proof:
- **($\implies$):** If $v$ is a descendant of $u$, consider the path of tree edges from $u$ to $v$. Every intermediate node $w$ on this path is a descendant of $u$, meaning $d[u] < d[w]$. Thus, at time $d[u]$, no node on this path has been discovered yet; they are all WHITE.
- **($\impliedby$):** If there is a path of white vertices from $u$ to $v$ at time $d[u]$, vertex $u$ cannot turn BLACK until all reachable white vertices have been explored. By induction along the path, $v$ must be discovered before $u$ finishes, forcing $d[u] < d[v] < f[v] < f[u]$. $\blacksquare$

---

## The Undirected DFS Leaf Theorem (ClassPractice Question 7)

**Exam Question Analysis:**
*Consider an undirected graph $G$. Let $T$ be a depth-first search tree. Let $u$ be a vertex, and let $v$ be the first unvisited vertex visited after visiting $u$. Which statement is always true?*
*(a) $(u, v)$ must be an edge in $G$.*
*(b) $(u, v)$ must be an edge and $v$ is a descendant of $u$ in $T$.*
*(c) If $(u, v)$ is not an edge, $u$ and $v$ have the same parent.*
*(d) If $(u, v)$ is not an edge, then $u$ is a leaf in $T$.*

> **Correct Answer: (d) If $(u, v)$ is not an edge, then $u$ is a leaf in $T$.**

### Rigorous Mathematical Justification:
When DFS visits vertex $u$, it immediately scans the adjacency list $Adj[u]$ for an unvisited (WHITE) neighbor:
1. **Case 1: $u$ has at least one unvisited neighbor $w$.**
   DFS immediately traverses edge $(u, w)$ and visits $w$. In this case, the very next visited vertex $v = w$ **must be a neighbor of $u$** ($(u, v) \in E$), and $v$ becomes a child of $u$ in $T$.
2. **Case 2: $u$ has NO unvisited neighbors.**
   If $u$ has no unvisited neighbors, DFS **cannot branch deeper from $u$**.
   - Because $u$ cannot discover any children, $u$ has **zero children in $T$**.
   - By definition, a node with zero children in a tree is a **leaf**!
   - DFS immediately colors $u$ BLACK and backtracks to $u$'s parent (or higher ancestors) until finding an ancestor with an unvisited neighbor $v$.
   - When that ancestor discovers $v$, vertex $v$ is visited, but $(u, v)$ is not necessarily an edge in $G$.

**Deduction:** If $(u, v)$ is NOT an edge in $G$, Case 1 did not happen. Therefore, Case 2 must have occurred, which proves that **$u$ must be a leaf in $T$**. $\blacksquare$

---

# 4. Edge Classification & Color-Based Identification

## The Four Directed Edge Types: Tree, Back, Forward, Cross

```
                       ┌──────────────────────┐
                       │      [Ancestor]      │
                       └──────────┬───────────┘
                                  │ ▲       ▲
                        Tree Edge │ │ Back  │
                                  ▼ │ Edge  │ Cross
                       ┌────────────┴┐      │ Edge
                       │   [ u ]     │      │
                       └──────┬──────┘      │
                  Forward     │             │
                   Edge       ▼             │
                       ┌─────────────┐      │
                       │ [Descendant]├──────┘
                       └─────────────┘
```

1. **Tree Edges:** Edges in the DFS forest. $(u, v)$ is a tree edge if $v$ was first discovered by exploring $(u, v)$.
2. **Back Edges:** Edges $(u, v)$ connecting $u$ to an **ancestor** $v$ in the DFS tree. Self-loops are back edges.
3. **Forward Edges:** Non-tree edges $(u, v)$ connecting $u$ to a **descendant** $v$ in the DFS tree.
4. **Cross Edges:** Edges $(u, v)$ connecting vertices that are neither ancestors nor descendants. Can connect different subtrees or different trees in the forest.

---

## Color-State Decision Table at Exploration Time

When edge $(u, v)$ is scanned from currently active (GREY) node $u$:

| Target Color of $v$ | Edge Classification | Timestamp Condition | Structural Meaning |
| :---: | :---: | :---: | :--- |
| **WHITE** | **Tree Edge** | $d[u] < d[v]$ | $v$ discovered for the first time via $u$ |
| **GREY** | **Back Edge** | $d[v] < d[u] < f[u] < f[v]$ | $v$ is an active ancestor on the call stack $\implies$ **CYCLE!** |
| **BLACK** | **Forward Edge** | $d[u] < d[v] < f[v] < f[u]$ | $v$ is a descendant already explored via another path |
| **BLACK** | **Cross Edge** | $d[v] < f[v] < d[u] < f[u]$ | $v$ was completed in an earlier branch/tree |

---

## Why Forward and Cross Edges CANNOT Exist in Undirected DFS

> **Theorem:** In a depth-first search of an **undirected graph**, every edge is strictly a **Tree Edge** or a **Back Edge**.

### Proof:
Let $(u, v)$ be an arbitrary edge in an undirected graph $G$.
Without loss of generality, suppose $u$ is discovered before $v$ ($d[u] < d[v]$).
- When $u$ is discovered at time $d[u]$, edge $(u, v)$ exists, so $v$ is adjacent to $u$.
- At time $d[u]$, vertex $v$ is WHITE.
- By the White-Path Theorem, the single-edge path $\langle u, v \rangle$ is an all-white path, meaning **$v$ must become a descendant of $u$** ($d[u] < d[v] < f[v] < f[u]$).
- When edge $(u, v)$ is explored:
  - If explored from $u \to v$: $v$ is WHITE, so it is a **Tree Edge**.
  - If explored from $v \to u$: $u$ is an ancestor of $v$ (and is GREY), so it is a **Back Edge**.
- It can never be a cross edge because $v$ cannot finish before $u$ is discovered.
- It can never be a forward edge because the edge is traversed in the forward direction as a tree edge first.
Thus, **only Tree and Back edges exist**. $\blacksquare$

---

# 5. Advanced Algorithmic Applications of DFS

## Cycle Detection in Directed Graphs (Back-Edge Equivalence)

> **Theorem:** A directed graph $G$ contains a directed cycle if and only if DFS yields at least one **Back Edge**.

### Why Tree/Forward/Cross Edges Cannot Form Cycles:
- Tree and forward edges go from smaller discovery time to larger discovery time within an ancestor-descendant tree.
- Cross edges go from higher finish time to lower finish time ($f[v] < d[u]$), crossing between completed subtrees.
- Only a back edge points to an active ancestor currently on the stack ($d[v] < d[u] < f[u] < f[v]$), closing the loop $v \leadsto u \to v$.

---

## Bridges and Articulation Points (Cut Vertices & Low-Link Values)

### Low-Link Definition:
$$low[u] = \min \begin{cases} 
d[u] \\ 
\min \{ d[v] : (u, v) \text{ is a Back Edge} \} \\ 
\min \{ low[v] : (u, v) \text{ is a Tree Edge} \} 
\end{cases}$$

### The Three Critical Conditions:
1. **Root Articulation Condition:** Root of DFS tree is an articulation point $\iff$ it has **$\ge 2$ tree children**.
2. **Non-Root Articulation Condition:** Non-root $u$ is an articulation point $\iff \exists \text{ child } v \text{ such that } low[v] \ge d[u]$.
3. **Bridge Condition:** Tree edge $(u, v)$ is a bridge $\iff low[v] > d[u]$.

---

# 6. Exam Scenario & Reasoning Breakdown (ClassPractice Analysis)

## Start & Finish Time Relative Inequalities (ClassPractice Question 9)

Consider a graph with vertices $\{A, B, C, D, E\}$ and edges $A \to B \to D$, $A \to C \to D$, and $B \to E$:

```
        (A) ──────► (B) ──────► (E)
         │           │
         ▼           ▼
        (C) ──────► (D)
```

1. **Can $d[D] < d[E]$?**
   **YES.** If $B$ explores $(B, D)$ before $(B, E)$, $D$ is discovered before $E$.
2. **Can $d[E] < d[D]$?**
   **YES.** If $B$ explores $(B, E)$ before $(B, D)$, $E$ is discovered before $D$.
3. **Can $d[D] < d[C]$?**
   **YES.** If $A$ explores $(A, B)$ first, $B$ can discover $D$ before $A$ ever explores $(A, C)$.
4. **Can $f[A] < f[B]$?**
   **NO, NEVER.** Because $A \to B$ is a directed edge in a DAG, $B$ is a descendant of $A$. By the Parenthesis Theorem, $f[B] < f[A]$ unconditionally.
5. **Can $f[D] < f[B]$?**
   **YES, ALWAYS.** $D$ is a descendant of $B$, so $D$ must finish before $B$ finishes.

---

# 7. Exhaustive Conceptual True / False Question Bank (With Deep Justifications)

---

### Q1: In an unweighted directed graph, DFS always finds the shortest path from source $s$ to all reachable vertices.
> **Answer: FALSE.**
> **Justification:** As proven in Slip Test Question 1, DFS follows arbitrary edge branches and may discover a node via a long winding path before seeing a direct single-edge shortcut.

---

### Q2: In an undirected graph, if $v$ is the first unvisited vertex visited after $u$ and $(u, v) \notin E$, then $u$ must be a leaf in the DFS tree.
> **Answer: TRUE.**
> **Justification:** If $u$ had any unvisited neighbor, DFS would visit it next (forming an edge). Since $v$ is not adjacent to $u$, $u$ had zero unvisited neighbors and backtracked immediately, meaning $u$ has 0 children (a leaf).

---

### Q3: In a directed graph, if $(u, v)$ is an edge and $d[u] < d[v]$, then $(u, v)$ must be a tree edge.
> **Answer: FALSE.**
> **Justification:** $(u, v)$ could be a **forward edge** to a descendant that was already discovered through another path.

---

### Q4: If DFS is modified to update visited vertex distances whenever a shorter path is found until convergence, it will terminate in finite time on graphs with negative cycles.
> **Answer: FALSE.**
> **Justification:** Traversal of a negative cycle decreases distances infinitely ($-\infty$), trapping the relaxation loop in an infinite cycle.

---

### Q5: In an undirected graph, a DFS tree can never contain cross edges.
> **Answer: TRUE.**
> **Justification:** By the White-Path Theorem, an edge between two vertices forces one to be discovered while the other is active, classifying all non-tree edges as back edges.

---

### Q6: If vertex $v$ is a descendant of $u$ in a DFS forest, then $d[u] < d[v] < f[v] < f[u]$.
> **Answer: TRUE.**
> **Justification:** This is the Descendant Nesting Corollary of the Parenthesis Theorem.

---

### Q7: The root of a DFS tree in an undirected graph is an articulation point if and only if it has at least two children in the DFS tree.
> **Answer: TRUE.**
> **Justification:** Undirected DFS trees have no cross edges. Disjoint child subtrees cannot reach each other without passing through the root.

---

### Q8: In a DAG, a back edge can be discovered during DFS.
> **Answer: FALSE.**
> **Justification:** A back edge forms a directed cycle. A DAG by definition contains no directed cycles.

---

### Q9: The running time of DFS is $\Theta(V^2)$ when using an adjacency list.
> **Answer: FALSE.**
> **Justification:** With an adjacency list, DFS touches each vertex once and scans each edge once, running in $\Theta(V + E)$ time.

---

### Q10: A vertex $u$ is GREY if and only if it is currently in the active execution call stack.
> **Answer: TRUE.**
> **Justification:** A node is colored GREY upon function entry and BLACK upon function return.
