# Breadth-First Search (BFS): The Definitive Master Guide

---

## Table of Contents
1. [Graph Fundamentals & Representations](#1-graph-fundamentals--representations)
   - [Graph Definitions & Terminology](#graph-definitions--terminology)
   - [Adjacency Matrix vs. Adjacency List](#adjacency-matrix-vs-adjacency-list)
   - [The Universal Sink Problem ($O(V)$ Solution on Adjacency Matrix)](#the-universal-sink-problem-ov-solution-on-adjacency-matrix)
2. [Breadth-First Search (BFS) Mechanics](#2-breadth-first-search-bfs-mechanics)
   - [Frontier Expansion & Intuition](#frontier-expansion--intuition)
   - [The Three-Coloring Abstraction (White, Grey, Black)](#the-three-coloring-abstraction-white-grey-black)
   - [Vertex Attributes: Distance ($d$), Predecessor ($p$), and Color](#vertex-attributes-distance-d-predecessor-p-and-color)
   - [BFS Algorithm for Connected Graphs](#bfs-algorithm-for-connected-graphs)
   - [General BFS Algorithm for Disconnected Graphs (Multi-Component Wrapper)](#general-bfs-algorithm-for-disconnected-graphs-multi-component-wrapper)
   - [Complete Step-by-Step Trace (8-Vertex CLRS Benchmark Graph)](#complete-step-by-step-trace-8-vertex-clrs-benchmark-graph)
3. [Theoretical Rigor: Complexity & Correctness Proofs](#3-theoretical-rigor-complexity--correctness-proofs)
   - [Asymptotic Time Complexity Analysis](#asymptotic-time-complexity-analysis)
   - [Space Complexity Analysis](#space-complexity-analysis)
   - [Queue Monotonicity Lemma](#queue-monotonicity-lemma)
   - [Shortest Path Theorem & Rigorous Proof by Contradiction](#shortest-path-theorem--rigorous-proof-by-contradiction)
4. [BFS Trees & Edge Classification](#4-bfs-trees--edge-classification)
   - [Structure of the Breadth-First Tree](#structure-of-the-breadth-first-tree)
   - [Edge Classification in Undirected Graphs (Tree & Cross Edges)](#edge-classification-in-undirected-graphs-tree--cross-edges)
   - [Edge Classification in Directed Graphs](#edge-classification-in-directed-graphs)
   - [The Level-Difference Lemma](#the-level-difference-lemma)
5. [Core Algorithmic Applications (From Lecture Slides)](#5-core-algorithmic-applications-from-lecture-slides)
   - [Cycle Detection in Undirected Graphs via BFS](#cycle-detection-in-undirected-graphs-via-bfs)
   - [Connected Components: Counting and Labeling](#connected-components-counting-and-labeling)
   - [Finding a Mother Vertex (Universal Source) in a Directed Graph](#finding-a-mother-vertex-universal-source-in-a-directed-graph)
   - [Bipartite Graph Testing (Two-Colorability & Odd Cycle Detection)](#bipartite-graph-testing-two-colorability--odd-cycle-detection)
   - [Tree Diameter via Two-BFS (Double BFS Algorithm & Formal Proof)](#tree-diameter-via-two-bfs-double-bfs-algorithm--formal-proof)
6. [Advanced Algorithmic Extensions & Design Patterns](#6-advanced-algorithmic-extensions--design-patterns)
   - [0-1 BFS Using a Double-Ended Queue (Deque)](#0-1-bfs-using-a-double-ended-queue-deque)
   - [Dial's Algorithm ($0-K$ BFS via Bucket Queue)](#dials-algorithm-0-k-bfs-via-bucket-queue)
   - [Multi-Source BFS (Simultaneous Wavefront Propagation)](#multi-source-bfs-simultaneous-wavefront-propagation)
   - [Bidirectional BFS (Frontier Meet-in-the-Middle)](#bidirectional-bfs-frontier-meet-in-the-middle)
   - [BFS on Implicit State Spaces (Grid Mazes, Word Ladders, Sliding Puzzles)](#bfs-on-implicit-state-spaces-grid-mazes-word-ladders-sliding-puzzles)
   - [Finding the Shortest Cycle (Girth) in an Unweighted Graph](#finding-the-shortest-cycle-girth-in-an-unweighted-graph)
7. [Deep Intuitive Insights, Common Traps & Reasoning Questions](#7-deep-intuitive-insights-common-traps--reasoning-questions)
8. [Exhaustive Conceptual True / False Question Bank (With Deep Justifications)](#8-exhaustive-conceptual-true--false-question-bank-with-deep-justifications)

---

# 1. Graph Fundamentals & Representations

## Graph Definitions & Terminology
A graph $G = (V, E)$ consists of:
- A set of vertices (or nodes) $V$, with cardinality $|V| = n$.
- A set of edges $E \subseteq V \times V$, with cardinality $|E| = m$.

### Graph Classifications:
1. **Undirected Graph:** Edges are unordered pairs: $(u, v) = (v, u)$. By standard convention, undirected graphs have no self-loops ($(u, u) \notin E$). The maximum number of edges is:
   $$|E|_{\max} = \binom{n}{2} = \frac{n(n - 1)}{2}$$
2. **Directed Graph (Digraph):** Edges are ordered pairs: $(u, v)$ signifies an edge directed from source $u$ to target $v$ ($u \to v$). Self-loops $(u, u)$ are permissible. The maximum number of edges is:
   $$|E|_{\max} = n^2 \quad (\text{or } n(n - 1) \text{ if self-loops are disallowed})$$
3. **Density Characteristics:**
   - **Dense Graph:** $|E| \approx |V|^2$.
   - **Sparse Graph:** $|E| \approx |V|$.
4. **Weighted Graph:** Associates a real number weight $\omega(e) \in \mathbb{R}$ with each edge $e \in E$ (or vertex).

---

## Adjacency Matrix vs. Adjacency List

Choosing the proper data structure fundamentally dictates algorithmic efficiency:

```
Adjacency Matrix (Dense: O(V²))           Adjacency List (Sparse: O(V + E))
     1   2   3   4                             1 ──► [ 2 ] ──► [ 4 ]
 1 [ 0   1   0   1 ]                           2 ──► [ 1 ] ──► [ 3 ]
 2 [ 1   0   1   0 ]                           3 ──► [ 2 ] ──► [ 4 ]
 3 [ 0   1   0   1 ]                           4 ──► [ 1 ] ──► [ 3 ]
 4 [ 1   0   1   0 ]
```

### Detailed Comparison:

| Feature / Operation | Adjacency Matrix | Adjacency List |
| :--- | :--- | :--- |
| **Storage Space** | $\Theta(V^2)$ bits or words | $\Theta(V + E)$ words |
| **Edge Lookup: $(u, v) \in E$?** | $\Theta(1)$ direct array access | $O(\text{deg}(u))$ scan |
| **Iterate over all neighbors of $u$** | $\Theta(V)$ (must inspect entire row) | $\Theta(\text{deg}(u))$ (optimal) |
| **Insert an edge** | $\Theta(1)$ | $\Theta(1)$ (push to head/tail) |
| **Delete an edge $(u, v)$** | $\Theta(1)$ | $O(\text{deg}(u))$ list search |
| **Memory Footprint on Sparse Graphs**| Wasteful ($V^2$ entries even if $E \ll V^2$) | Highly compact |
| **Cache Performance** | Excellent sequential cache locality | Pointer chasing (linked lists) or contiguous (`std::vector`) |
| **BFS Total Running Time** | $\Theta(V^2)$ | $\Theta(V + E)$ |

> [!NOTE]
> For undirected graphs, an adjacency matrix is symmetric ($A[i, j] = A[j, i]$). Storage can be halved by allocating only the upper or lower triangular matrix, or packed as 1 bit per edge in a bitset.

---

## The Universal Sink Problem ($O(V)$ Solution on Adjacency Matrix)

**Problem Statement (Lecture Slide 4):**
Given a directed graph $G = (V, E)$ represented as an $n \times n$ adjacency matrix $A$, determine whether $G$ contains a **universal sink**.
- A universal sink is a vertex $u^*$ such that:
  - $\text{in-degree}(u^*) = |V| - 1$ (every other vertex has a directed edge into $u^*$).
  - $\text{out-degree}(u^*) = 0$ (no directed edges leave $u^*$).
- In the adjacency matrix, row $u^*$ consists entirely of $0$s, and column $u^*$ consists of $1$s (except $A[u^*, u^*] = 0$).
- **Challenge:** Solve this in $O(V)$ time, despite the adjacency matrix containing $V^2$ elements.

### Algorithmic Insight (Elimination Property):
Inspect any arbitrary matrix entry $A[i, j]$ where $i \neq j$:
1. If $A[i, j] == 1$: Vertex $i$ has an outgoing edge to $j$. Hence, **vertex $i$ cannot be a universal sink** (out-degree must be 0).
2. If $A[i, j] == 0$: Vertex $j$ does not receive an edge from $i$. Hence, **vertex $j$ cannot be a universal sink** (in-degree must be $V - 1$).

**Crucial Deduction:** A single test of $A[i, j]$ eliminates either vertex $i$ or vertex $j$ from candidacy!

### Two-Pointer Elimination Algorithm:
1. Initialize two pointers $i = 1, j = 1$.
2. While $i \le n$ and $j \le n$:
   - If $A[i, j] == 1$, increment $i \leftarrow i + 1$ (eliminates $i$).
   - If $A[i, j] == 0$, increment $j \leftarrow j + 1$ (eliminates $j$).
3. The remaining candidate $k = i$ (if $i \le n$) is the **sole possible candidate**.
4. **Verification Step:** We must explicitly verify if candidate $k$ is truly a universal sink:
   - Check that row $k$ contains all $0$s: $A[k, c] == 0$ for all $c \in \{1, \dots, n\}$.
   - Check that column $k$ contains all $1$s except at the diagonal: $A[r, k] == 1$ for all $r \neq k$.

```text
UNIVERSAL-SINK(A, n):
    i = 1
    j = 1
    while i <= n and j <= n do
        if A[i, j] == 1 then
            i = i + 1
        else
            j = j + 1
            
    candidate = i
    if candidate > n then
        return NIL // No universal sink
        
    // Verification phase: O(V)
    for c = 1 to n do
        if A[candidate, c] == 1 then
            return NIL
            
    for r = 1 to n do
        if r != candidate and A[r, candidate] == 0 then
            return NIL
            
    return candidate
```

### Complexity Analysis:
- In each step of the while loop, either $i$ increases by 1 or $j$ increases by 1.
- Total while loop steps: at most $2n - 1 = O(V)$.
- Verification phase: inspects $2n$ elements $= O(V)$.
- **Total Time Complexity:** $\Theta(V)$.
- **Space Complexity:** $\Theta(1)$.

---

# 2. Breadth-First Search (BFS) Mechanics

## Frontier Expansion & Intuition
Breadth-First Search systematically traverses a graph by expanding an exploratory frontier across the "breadth" of the graph:
1. Start at a designated source vertex $s$ (Level 0).
2. Visit all immediate neighbors of $s$ (Level 1).
3. Visit all neighbors of Level 1 vertices that have not been visited yet (Level 2).
4. Continue level by level until all reachable vertices are visited.

Think of BFS as a wave or a ripple propagating outwards in concentric circles from the source vertex.

---

## The Three-Coloring Abstraction (White, Grey, Black)
To manage the state of exploration, BFS assigns one of three colors to each vertex:

```
[ WHITE ] ────────► [ GREY ] ────────► [ BLACK ]
Not Discovered        Frontier / In Queue    Fully Explored
(Unvisited)           (Discovered, pending)  (Dequeued, finished)
```

1. **WHITE (Undiscovered):**
   - The vertex has not yet been encountered.
   - Initial state of all vertices in $G$ before BFS begins.
2. **GREY (Frontier / Discovered):**
   - The vertex has been discovered and placed into the First-In, First-Out (FIFO) queue.
   - Its adjacency list has **not yet been fully examined**.
   - A grey vertex can be adjacent to white, grey, or black vertices.
3. **BLACK (Fully Explored):**
   - The vertex has been removed from the FIFO queue and all its incident edges have been scanned.
   - **Key Invariant:** A black vertex is adjacent **only** to grey or black vertices. It can never have an edge directly to a white vertex!

---

## Vertex Attributes: Distance ($d$), Predecessor ($p$), and Color
For each vertex $u \in V$, BFS maintains:
- $u.color \in \{\text{WHITE}, \text{GREY}, \text{BLACK}\}$
- $u.d \in \mathbb{N} \cup \{\infty\}$: The distance from the source vertex $s$ to $u$. For unweighted graphs, $u.d$ equals the minimum number of edges on any path from $s$ to $u$.
- $u.p \in V \cup \{\text{NIL}\}$: The parent (predecessor) of $u$ in the BFS tree. Used to trace the shortest path back to $s$.

---

## BFS Algorithm for Connected Graphs

```text
BFS(G, s):
    // Phase 1: Initialization
    for each vertex u ∈ G.V - {s} do
        u.color = WHITE
        u.d = ∞
        u.p = NIL
        
    s.color = GREY
    s.d = 0
    s.p = NIL
    
    Q = EMPTY_QUEUE()
    ENQUEUE(Q, s)
    
    // Phase 2: Exploration
    while not IS_EMPTY(Q) do
        u = DEQUEUE(Q)
        for each v ∈ G.Adj[u] do
            if v.color == WHITE then
                v.color = GREY
                v.d = u.d + 1
                v.p = u
                ENQUEUE(Q, v)
        u.color = BLACK
```

---

## General BFS Algorithm for Disconnected Graphs (Multi-Component Wrapper)

**Homework Problem (Lecture Slide 8):**
*Write down the BFS code for when the graph can be disconnected.*

If the graph contains multiple disconnected components, calling `BFS(G, s)` visits only vertices reachable from $s$. To guarantee that **every vertex in $G$ is visited and explored**, we wrap BFS in a driver loop:

```text
BFS-GENERAL(G):
    // Initialize all vertices across the entire graph
    for each vertex u ∈ G.V do
        u.color = WHITE
        u.d = ∞
        u.p = NIL
        
    component_count = 0
    
    // Outer driver loop guarantees full graph coverage
    for each vertex s ∈ G.V do
        if s.color == WHITE then
            component_count = component_count + 1
            BFS-VISIT(G, s, component_count)

BFS-VISIT(G, s, comp_id):
    s.color = GREY
    s.d = 0
    s.p = NIL
    s.component = comp_id
    
    Q = EMPTY_QUEUE()
    ENQUEUE(Q, s)
    
    while not IS_EMPTY(Q) do
        u = DEQUEUE(Q)
        for each v ∈ G.Adj[u] do
            if v.color == WHITE then
                v.color = GREY
                v.d = u.d + 1
                v.p = u
                v.component = comp_id
                ENQUEUE(Q, v)
        u.color = BLACK
```

### Properties of the General Wrapper:
1. Discovers all connected components in an undirected graph.
2. Forms a **Breadth-First Forest** comprising multiple BFS trees.
3. Maintains $O(V + E)$ overall time complexity, because each vertex and edge is processed exactly as before.

---

## Complete Step-by-Step Trace (8-Vertex CLRS Benchmark Graph)

Let us meticulously trace the 8-vertex graph from lecture slides 9–18:
Vertices: $\{r, s, t, u, v, w, x, y\}$.
Undirected edges:
- $(r, s), (r, v)$
- $(s, w)$
- $(w, t), (w, x)$
- $(t, u), (t, x)$
- $(x, u), (x, y)$
- $(u, y)$

```
   (r) ──────── (s) ──────── (t) ──────── (u)
    │            │            │ \        / │
    │            │            │  \      /  │
    │            │            │   \    /   │
   (v)          (w) ──────── (x) ───┼────── (y)
                                    └───────┘
```

Source vertex: $s$.

### Initial State:
- All vertices white, $d = \infty, p = \text{NIL}$.
- $s.color = \text{GREY}, s.d = 0$.
- **Queue $Q$:** `[ s ]`

---

### Iteration 1: Dequeue $u = s$
- Scan $Adj[s] = \{r, w\}$:
  - $r$ is WHITE $\implies r.color = \text{GREY}, r.d = 0 + 1 = 1, r.p = s$. Enqueue $r$.
  - $w$ is WHITE $\implies w.color = \text{GREY}, w.d = 0 + 1 = 1, w.p = s$. Enqueue $w$.
- $s.color = \text{BLACK}$.
- **Queue $Q$:** `[ r, w ]`

---

### Iteration 2: Dequeue $u = r$
- Scan $Adj[r] = \{s, v\}$:
  - $s$ is BLACK (ignored).
  - $v$ is WHITE $\implies v.color = \text{GREY}, v.d = 1 + 1 = 2, v.p = r$. Enqueue $v$.
- $r.color = \text{BLACK}$.
- **Queue $Q$:** `[ w, v ]`

---

### Iteration 3: Dequeue $u = w$
- Scan $Adj[w] = \{s, t, x\}$:
  - $s$ is BLACK (ignored).
  - $t$ is WHITE $\implies t.color = \text{GREY}, t.d = 1 + 1 = 2, t.p = w$. Enqueue $t$.
  - $x$ is WHITE $\implies x.color = \text{GREY}, x.d = 1 + 1 = 2, x.p = w$. Enqueue $x$.
- $w.color = \text{BLACK}$.
- **Queue $Q$:** `[ v, t, x ]`

---

### Iteration 4: Dequeue $u = v$
- Scan $Adj[v] = \{r\}$:
  - $r$ is BLACK (ignored).
- $v.color = \text{BLACK}$.
- **Queue $Q$:** `[ t, x ]`

---

### Iteration 5: Dequeue $u = t$
- Scan $Adj[t] = \{w, x, u\}$:
  - $w$ is BLACK (ignored).
  - $x$ is GREY (already in queue, ignored).
  - $u$ is WHITE $\implies u.color = \text{GREY}, u.d = 2 + 1 = 3, u.p = t$. Enqueue $u$.
- $t.color = \text{BLACK}$.
- **Queue $Q$:** `[ x, u ]`

---

### Iteration 6: Dequeue $u = x$
- Scan $Adj[x] = \{w, t, u, y\}$:
  - $w$ is BLACK, $t$ is BLACK, $u$ is GREY.
  - $y$ is WHITE $\implies y.color = \text{GREY}, y.d = 2 + 1 = 3, y.p = x$. Enqueue $y$.
- $x.color = \text{BLACK}$.
- **Queue $Q$:** `[ u, y ]`

---

### Iteration 7: Dequeue $u = u$
- Scan $Adj[u] = \{t, x, y\}$:
  - $t, x$ are BLACK; $y$ is GREY.
- $u.color = \text{BLACK}$.
- **Queue $Q$:** `[ y ]`

---

### Iteration 8: Dequeue $u = y$
- Scan $Adj[y] = \{x, u\}$:
  - $x, u$ are BLACK.
- $y.color = \text{BLACK}$.
- **Queue $Q$:** `[ ]` (Empty $\implies$ Algorithm terminates!)

---

### Final BFS Result Table:
| Vertex | Color | Distance ($d$) | Parent ($p$) | Shortest Path from $s$ |
| :---: | :---: | :---: | :---: | :--- |
| **$s$** | BLACK | 0 | NIL | $s$ |
| **$r$** | BLACK | 1 | $s$ | $s \to r$ |
| **$w$** | BLACK | 1 | $s$ | $s \to w$ |
| **$v$** | BLACK | 2 | $r$ | $s \to r \to v$ |
| **$t$** | BLACK | 2 | $w$ | $s \to w \to t$ |
| **$x$** | BLACK | 2 | $w$ | $s \to w \to x$ |
| **$u$** | BLACK | 3 | $t$ | $s \to w \to t \to u$ |
| **$y$** | BLACK | 3 | $x$ | $s \to w \to x \to y$ |

---

# 3. Theoretical Rigor: Complexity & Correctness Proofs

## Asymptotic Time Complexity Analysis

Let us analyze the execution cost step-by-step:
1. **Initialization:**
   - Setting `color`, `d`, and `p` for all $V$ vertices requires $\Theta(V)$ operations.
2. **Queue Operations:**
   - Each vertex is colored GREY at most once (only when it is WHITE).
   - Hence, each vertex is enqueued **at most once**.
   - Each vertex is dequeued **at most once**.
   - Enqueue and Dequeue operations execute in $O(1)$ time.
   - Total queue management cost: $O(V)$.
3. **Adjacency List Scans:**
   - The adjacency list of a vertex $u$ is scanned **only once**, precisely when $u$ is dequeued.
   - The number of iterations in the inner `for` loop is $|Adj[u]| = \text{deg}(u)$.
   - Summing across all vertices:
     $$\sum_{u \in V} |Adj[u]| = \begin{cases} 
     2|E| & \text{for undirected graphs} \\ 
     |E| & \text{for directed graphs} 
     \end{cases} = \Theta(E)$$

**Total Time Complexity (Adjacency List):**
$$T(V, E) = \Theta(V) + O(V) + \Theta(E) = \Theta(V + E)$$

**Total Time Complexity (Adjacency Matrix):**
If represented as an adjacency matrix, scanning the neighbors of $u$ requires inspecting all $V$ entries in row $u$.
$$T(V, E) = \Theta(V) + \sum_{u \in V} \Theta(V) = \Theta(V^2)$$

---

## Space Complexity Analysis
- The FIFO Queue holds at most $V$ vertices: $O(V)$ space.
- Arrays for `color`, `d`, and `p` each require $V$ entries: $3 \times O(V) = O(V)$.
- **Total Auxiliary Space Complexity:** $\Theta(V)$ (excluding graph storage).

---

## Queue Monotonicity Lemma

A cornerstone property of BFS is that the queue maintains ordered distances:

> **Lemma (Queue Monotonicity):**
> Suppose that at any point during the execution of BFS, the queue contains vertices $\langle v_1, v_2, \dots, v_r \rangle$, where $v_1$ is the head and $v_r$ is the tail. Then:
> 1. $v_r.d \le v_1.d + 1$
> 2. $v_i.d \le v_{i+1}.d$ for all $1 \le i < r$

### Intuition:
Vertices enter the queue in non-decreasing order of distance. At any single moment, the queue contains vertices from at most two consecutive levels: some vertices with distance $k$, followed by vertices with distance $k + 1$.

```
Head                                        Tail
┌──────────┬──────────┬──────────┬──────────┬──────────┐
│  d = k   │  d = k   │  d = k   │ d = k+1  │ d = k+1  │
└──────────┴──────────┴──────────┴──────────┴──────────┘
```

---

## Shortest Path Theorem & Rigorous Proof by Contradiction

> **Theorem:** Let $G = (V, E)$ be a directed or undirected graph, and suppose BFS is run on $G$ from source vertex $s$. Then for each vertex $v \in V$, the value $v.d$ computed by BFS equals the shortest-path distance $\delta(s, v)$ from $s$ to $v$:
> $$v.d = \delta(s, v)$$

### Proof (Lecture Slides 20–21):

#### Step 1: Prove $v.d \ge \delta(s, v)$
BFS discovers a valid path from $s$ to $v$ of length $v.d$ by chaining predecessor pointers:
$$s \to \dots \to v.p \to v$$
Because $\delta(s, v)$ is by definition the *minimum* weight of any path from $s$ to $v$, no path can be strictly shorter than $\delta(s, v)$. Hence:
$$v.d \ge \delta(s, v) \quad \forall v \in V$$

#### Step 2: Prove $v.d \le \delta(s, v)$ by Contradiction
Assume for the sake of contradiction that there exists at least one vertex for which $v.d \neq \delta(s, v)$. By Step 1, this implies $v.d > \delta(s, v)$.

Choose $v$ to be a vertex with **minimum $\delta(s, v)$** among all vertices that receive an incorrect distance:
$$v = \arg\min \{ \delta(s, u) : u.d > \delta(s, u) \}$$

Notice that $v \neq s$, because $s.d = 0 = \delta(s, s)$.
Furthermore, $v$ must be reachable from $s$ (otherwise $\delta(s, v) = \infty$, so $v.d \le \infty$ would hold).

Consider a true shortest path from $s$ to $v$. Let $u$ be the vertex immediately preceding $v$ on this path:
$$s \xrightarrow{\text{shortest}} u \to v$$
Because subpaths of shortest paths are shortest paths:
$$\delta(s, v) = \delta(s, u) + 1$$

Since $\delta(s, u) < \delta(s, v)$, by our choice of $v$ as the *closest* vertex with an incorrect distance, vertex $u$ **must have received its correct distance**:
$$u.d = \delta(s, u)$$

Therefore:
$$v.d > \delta(s, v) = \delta(s, u) + 1 = u.d + 1$$

Now examine what happens at the moment when vertex $u$ is dequeued from $Q$. When $u$ is dequeued, edge $(u, v)$ is inspected. What is the color of vertex $v$ at that moment?

There are only three possible colors for $v$:
1. **Case A: $v$ is WHITE.**
   - By the BFS code, $v$ is immediately discovered through $u$:
     $$v.d = u.d + 1$$
   - But this contradicts our assumption that $v.d > u.d + 1$!
2. **Case B: $v$ is BLACK.**
   - If $v$ is already black, it has already been dequeued from $Q$.
   - By Queue Monotonicity, vertices that are dequeued before $u$ must satisfy:
     $$v.d \le u.d < u.d + 1$$
   - This again contradicts $v.d > u.d + 1$!
3. **Case C: $v$ is GREY.**
   - If $v$ is grey, it was discovered earlier through some other vertex $w$ that was dequeued before $u$.
   - Because $w$ was dequeued before $u$, by Queue Monotonicity:
     $$w.d \le u.d$$
   - When $v$ was discovered from $w$, its distance was set to:
     $$v.d = w.d + 1 \le u.d + 1$$
   - This once again directly contradicts $v.d > u.d + 1$!

### Conclusion:
In all three possible cases, a direct mathematical contradiction arises.
Thus, no such vertex $v$ can exist.
$$v.d = \delta(s, v) \quad \forall v \in V \quad \blacksquare$$

---

# 4. BFS Trees & Edge Classification

## Structure of the Breadth-First Tree
During execution, the predecessor pointers $v.p$ define a directed subgraph:
$$G_\pi = (V_\pi, E_\pi)$$
where $V_\pi = \{v \in V : v.p \neq \text{NIL}\} \cup \{s\}$, and $E_\pi = \{(v.p, v) : v \in V_\pi - \{s\}\}$.
$G_\pi$ is a directed, rooted tree known as the **Breadth-First Tree**.

---

## Edge Classification in Undirected Graphs (Tree & Cross Edges)

In an undirected graph, every edge $(u, v) \in E$ falls into one of two categories relative to the BFS tree:
1. **Tree Edge:** An edge that belongs to the BFS tree. Encountered when vertex $v$ is discovered for the first time ($v$ is WHITE).
2. **Cross Edge:** An edge $(u, v)$ that is NOT part of the tree.
   - Connects vertices within the **same level**: $u.d = v.d$.
   - OR connects vertices in **adjacent levels**: $|u.d - v.d| = 1$.

> [!IMPORTANT]
> **Fundamental Theorem on Undirected BFS Edges:**
> In a BFS of an undirected graph, **NO edge can connect vertices whose levels differ by 2 or more**!
> $$|u.d - v.d| \le 1 \quad \forall (u, v) \in E$$
> Furthermore, an undirected graph BFS contains **NO Back edges** and **NO Forward edges**!

### Proof of the Level-Difference Lemma:
Assume for contradiction that there exists an edge $(u, v)$ such that $v.d > u.d + 1$.
Consider the moment $u$ is dequeued:
- Edge $(u, v)$ is inspected.
- If $v$ is white, $v.d$ becomes $u.d + 1$.
- If $v$ is grey or black, $v$ was already discovered, so $v.d \le u.d + 1$.
In all scenarios, $v.d \le u.d + 1$. By symmetry, $u.d \le v.d + 1$. Thus, $|u.d - v.d| \le 1$. $\blacksquare$

---

## Edge Classification in Directed Graphs

In a directed graph, BFS edges can be:
1. **Tree Edges:** $(u, v)$ where $v$ is WHITE when scanned.
2. **Back Edges:** $(u, v)$ where $v$ is an ancestor of $u$ in the BFS tree ($v.d \le u.d$).
3. **Cross Edges:** $(u, v)$ where $v$ is neither an ancestor nor descendant of $u$.
4. **Forward Edges:** CANNOT EXIST!
   - A forward edge would go from an ancestor $u$ to a non-child descendant $v$ with $v.d \ge u.d + 2$.
   - But if edge $u \to v$ existed, when $u$ was dequeued, $v$ would have been discovered immediately with $v.d = u.d + 1$, preventing it from having distance $\ge u.d + 2$.

---

# 5. Core Algorithmic Applications (From Lecture Slides)

## Cycle Detection in Undirected Graphs via BFS

**Problem (Slide 22):** *Given an undirected graph, report if the graph contains a cycle.*

### Principle:
In an undirected graph, if BFS explores an edge $(u, v)$ from current vertex $u$ and encounters a neighbor $v$ that has already been visited ($v.color \neq \text{WHITE}$) and is **not the immediate parent of $u$** ($v \neq u.p$), then a cycle exists!

```
         [ u ] ────── (Back / Cross Edge) ──────► [ v ]
           ▲                                        ▲
            \                                      /
             \─── [ LCA: Lowest Common Ancestor ] ┘
```

```text
HAS-CYCLE-UNDIRECTED-BFS(G):
    for each u ∈ G.V do
        u.color = WHITE
        u.p = NIL
        
    for each s ∈ G.V do
        if s.color == WHITE then
            s.color = GREY
            Q = EMPTY_QUEUE()
            ENQUEUE(Q, s)
            
            while not IS_EMPTY(Q) do
                u = DEQUEUE(Q)
                for each v ∈ G.Adj[u] do
                    if v.color == WHITE then
                        v.color = GREY
                        v.p = u
                        ENQUEUE(Q, v)
                    else if v != u.p then
                        return TRUE // Cycle detected!
                u.color = BLACK
                
    return FALSE // Acyclic (Forest)
```
- **Complexity:** $O(V + E)$.

---

## Connected Components: Counting and Labeling

**Problem (Slide 22):** *Count the number of connected components in an undirected graph.*

### Implementation:
Using our `BFS-GENERAL` driver loop:
1. Initialize `component_count = 0`.
2. Iterate through all $v \in V$. If $v.color == \text{WHITE}$:
   - Increment `component_count += 1`.
   - Run BFS from $v$, stamping every visited vertex with `comp_id = component_count`.
3. Return `component_count`.
- **Complexity:** $\Theta(V + E)$ time, $\Theta(V)$ space.

---

## Finding a Mother Vertex (Universal Source) in a Directed Graph

**Problem (Slide 22):** *Can you find a source vertex of a connected directed graph using BFS?*
A **mother vertex** (or universal source) is a vertex $v$ from which all other vertices in $G$ are reachable via directed paths.

### Algorithmic Approach:
1. In a directed graph, if a mother vertex exists, it belongs to the "source" strongly connected component.
2. If we run BFS from a candidate $u$:
   - If BFS visits all $V$ vertices, $u$ is a mother vertex.
   - If not, vertices not visited by $u$ could potentially reach $u$, but $u$ cannot reach them.
3. **Optimal Strategy:** Run Kosaraju/DFS finish-time identification in $O(V + E)$ to find the last finished vertex $v^*$, then run one final BFS from $v^*$ to verify if $\text{count}(\text{visited}) == |V|$.

---

## Bipartite Graph Testing (Two-Colorability & Odd Cycle Detection)

**Problem (Slide 22):** *Report whether a given undirected graph is bipartite.*

### Definition:
A graph is **bipartite** if its vertex set can be partitioned into two independent sets $U$ and $V$ such that every edge connects a vertex in $U$ to a vertex in $V$.
> **Theorem:** A graph is bipartite if and only if it contains **no odd-length cycles**.

### BFS Two-Coloring Algorithm:
Because BFS partitions vertices into concentric distance layers ($d = 0, 1, 2, 3, \dots$):
- Assign all even-distance vertices ($d \pmod 2 == 0$) to Color 1 (Red).
- Assign all odd-distance vertices ($d \pmod 2 == 1$) to Color 2 (Blue).
- An odd-length cycle exists if and only if there is an edge connecting two vertices in the **same level**: $u.d == v.d$.

```text
IS-BIPARTITE-BFS(G):
    for each u ∈ G.V do
        u.color = -1 // Unvisited
        
    for each s ∈ G.V do
        if u.color == -1 then
            s.color = 0
            Q = EMPTY_QUEUE()
            ENQUEUE(Q, s)
            
            while not IS_EMPTY(Q) do
                u = DEQUEUE(Q)
                for each v ∈ G.Adj[u] do
                    if v.color == -1 then
                        v.color = 1 - u.color // Invert color (0 -> 1, 1 -> 0)
                        ENQUEUE(Q, v)
                    else if v.color == u.color then
                        return FALSE // Same color on edge => Odd cycle!
                        
    return TRUE // Graph is bipartite
```
- **Complexity:** $O(V + E)$ time, $O(V)$ space.

---

## Tree Diameter via Two-BFS (Double BFS Algorithm & Formal Proof)

**Problem (Slide 23 & Image):**
*Compute the diameter of an undirected tree using BFS. Diameter: The maximum distance between any two vertices in the tree, measured by the number of edges in the unique simple path connecting them.*

```
       (u) Arbitrary Node
        │
       BFS 1
        ▼
       (v) Farthest node from u (Guaranteed to be an extreme diameter leaf!)
        │
       BFS 2
        ▼
       (w) Farthest node from v
       Distance dist(v, w) == Tree Diameter!
```

### The Double BFS Algorithm:
1. Select an **arbitrary vertex** $u \in V$.
2. Run BFS starting from $u$. Find the vertex $v$ that has the maximum distance from $u$:
   $$v = \arg\max_{x \in V} \text{dist}(u, x)$$
3. Run a second BFS starting from $v$. Find the vertex $w$ that has the maximum distance from $v$:
   $$w = \arg\max_{x \in V} \text{dist}(v, x)$$
4. The diameter of the tree is:
   $$\text{Diameter}(T) = \text{dist}(v, w)$$
   The unique path from $v$ to $w$ is the longest simple path in the tree.

### Formal Proof of Correctness:
Let $A$ and $B$ be two endpoints of a true diameter path in tree $T$:
$$\text{Diameter}(T) = \text{dist}(A, B)$$
Let $u$ be the arbitrary starting node, and let $v$ be the farthest node from $u$ found in BFS 1.

**Goal:** Prove that $v$ must be an endpoint of some diameter path (i.e., there exists a diameter path starting at $v$).

#### Case 1: The path from $u$ to $v$ shares at least one node $x$ with diameter path $A \dots B$.
Assume the diameter path from $A$ to $B$ passes through $x$, and the path from $u$ to $v$ intersects the diameter path at $x$:
- Distance from $u$ to $A$: $\text{dist}(u, x) + \text{dist}(x, A)$.
- Distance from $u$ to $B$: $\text{dist}(u, x) + \text{dist}(x, B)$.
- Distance from $u$ to $v$: $\text{dist}(u, x) + \text{dist}(x, v)$.

Because BFS 1 chose $v$ as the farthest node from $u$:
$$\text{dist}(u, v) \ge \text{dist}(u, A) \implies \text{dist}(u, x) + \text{dist}(x, v) \ge \text{dist}(u, x) + \text{dist}(x, A)$$
Subtracting $\text{dist}(u, x)$:
$$\text{dist}(x, v) \ge \text{dist}(x, A)$$

Now calculate the path length between $v$ and $B$:
$$\text{dist}(v, B) = \text{dist}(v, x) + \text{dist}(x, B) \ge \text{dist}(A, x) + \text{dist}(x, B) = \text{dist}(A, B)$$
Since $\text{dist}(A, B)$ is already the maximum possible distance in the tree, we must have:
$$\text{dist}(v, B) = \text{dist}(A, B) = \text{Diameter}(T)$$
Thus, $v$ is indeed an endpoint of a valid diameter path!

#### Case 2: The path from $u$ to $v$ does not intersect $A \dots B$.
Since $T$ is connected, there is a unique shortest path connecting path $u \dots v$ to path $A \dots B$. A symmetric inequality applies, confirming that replacing one diameter endpoint with $v$ produces an equal or greater path length, which must equal the diameter.

### Why Does Double BFS Fail on General Graphs with Cycles? (ClassPractice Q5c Deep-Dive)

**Exam Question (ClassPractice Quiz-2, Q5c):**
> *Select correct statements: (c) The depth of a breadth-first search tree on an undirected graph $G = (V, E)$ from an arbitrary vertex $v \in V$ is the diameter of the graph $G$.*
> **Answer: FALSE.**

#### 1. Why Single BFS Depth is NOT the Graph Diameter:
- The depth of a BFS tree rooted at vertex $v$ is by definition the **eccentricity** of $v$:
  $$\text{depth}(v) = \max_{u \in V} \delta(v, u) = \epsilon(v)$$
- The **diameter** of graph $G$ is the maximum shortest-path distance between *any* pair of vertices in $G$:
  $$\text{Diameter}(G) = \max_{x, y \in V} \delta(x, y) = \max_{v \in V} \epsilon(v)$$
- The **radius** of graph $G$ is the minimum eccentricity:
  $$\text{Radius}(G) = \min_{v \in V} \epsilon(v)$$
- Therefore, the depth of a BFS tree from an arbitrary vertex $v$ satisfies the bounded inequality:
  $$\text{Radius}(G) \le \text{depth}(v) \le \text{Diameter}(G) \le 2 \cdot \text{depth}(v)$$
- If $v$ happens to be the graph center, $\text{depth}(v) = \text{Radius}(G)$, which can be as small as $\lceil \text{Diameter}(G) / 2 \rceil$.

#### Minimal Counterexample for Single BFS Depth:
Consider a simple line graph $P_5$:
$$v_1 - v_2 - v_3 - v_4 - v_5$$
- True diameter: $\delta(v_1, v_5) = 4$.
- Suppose the arbitrary vertex chosen is the center $v_3$:
  - BFS from $v_3$ yields: $d[v_3] = 0, \; d[v_2] = d[v_4] = 1, \; d[v_1] = d[v_5] = 2$.
  - Depth of the BFS tree is **2**.
  - But graph diameter is **4**! Depth underestimates diameter by a factor of 2!

---

#### 2. Why Even Double-BFS (2-BFS) Fails on General Graphs with Cycles:
While Double-BFS is guaranteed to find the exact diameter on **trees** (acyclic graphs), it fails on general graphs containing cycles!

```
                  (B)
                 /   \
           (x)──(A)   (C)──(y)
                 \   /
                  (D)
```

**Minimal 6-Vertex Counterexample:**
- Let vertices $\{A, B, C, D\}$ form a 4-cycle $C_4$:
  $$(A, B), (B, C), (C, D), (D, A) \in E$$
- Attach tail vertex $x$ to $A$: $(x, A) \in E$.
- Attach tail vertex $y$ to $C$: $(y, C) \in E$.
- **True Diameter:** The longest shortest path in this graph is between $x$ and $y$:
  $$\text{Path: } x - A - B - C - y \quad (\text{length } 4) \implies \text{Diameter} = 4$$

**Execution of Double-BFS:**
1. **BFS 1: Start at arbitrary vertex $B$:**
   - $\delta(B, B) = 0$
   - $\delta(B, A) = 1, \; \delta(B, C) = 1$
   - $\delta(B, x) = 2, \; \delta(B, y) = 2, \; \delta(B, D) = 2$
   - Maximum distance from $B$ is 2. The set of farthest vertices is $\{x, y, D\}$.
   - Suppose tie-breaking selects $v = D$ as the farthest vertex.
2. **BFS 2: Start at $D$:**
   - $\delta(D, D) = 0$
   - $\delta(D, A) = 1, \; \delta(D, C) = 1$
   - $\delta(D, x) = 2, \; \delta(D, y) = 2, \; \delta(D, B) = 2$
   - Maximum distance from $D$ is **2**!
3. **Double-BFS Output:** Reports diameter = **2**.
4. **Discrepancy:** The true graph diameter is **4**!
- **Reason for Failure:** The cycle allows paths to wrap around, causing node $D$ to be equidistant from all peripheral branches. Because $D$ lies on the cycle rather than at an extremal peripheral leaf, BFS 2 fails to reach across the true diameter!
- **Conclusion:** Double-BFS is mathematically proven ONLY for acyclic trees. On general graphs, finding the diameter requires solving All-Pairs Shortest Paths (APSP) in $O(V(V + E))$ by running BFS from *every* vertex.

---

# 6. Advanced Algorithmic Extensions & Design Patterns

## 0-1 BFS Using a Double-Ended Queue (Deque)
**Problem:** Find the shortest path in a graph where every edge weight is either 0 or 1.
- Standard Dijkstra runs in $O(E \log V)$.
- Can we solve it in **$O(V + E)$ linear time**?

### The Deque Trick:
When relaxing edge $(u, v)$ with weight $w \in \{0, 1\}$:
- If $w == 0$: $v.d = u.d$. Vertex $v$ is at the *same* level as $u$. **Push $v$ to the FRONT of the deque.**
- If $w == 1$: $v.d = u.d + 1$. Vertex $v$ is at the *next* level. **Push $v$ to the BACK of the deque.**

This preserves the Queue Monotonicity property: the deque always contains distances differing by at most 1!

```text
ZERO-ONE-BFS(G, s):
    for each u ∈ G.V do
        u.d = ∞
    s.d = 0
    deque = EMPTY_DEQUE()
    PUSH_FRONT(deque, s)
    
    while not IS_EMPTY(deque) do
        u = POP_FRONT(deque)
        for each (v, w) ∈ G.Adj[u] do
            if u.d + w < v.d then
                v.d = u.d + w
                if w == 0 then
                    PUSH_FRONT(deque, v)
                else
                    PUSH_BACK(deque, v)
```
- **Complexity:** $\Theta(V + E)$ time, $\Theta(V)$ space.

---

## Dial's Algorithm ($0-K$ BFS via Bucket Queue)
When edge weights are small integers in $\{0, 1, \dots, K\}$:
- Maintain an array of $K + 1$ buckets (circular buffer of lists).
- Bucket $i$ holds all discovered vertices with distance $i$.
- Dequeue moves sequentially through non-empty buckets.
- **Time Complexity:** $O(V \cdot K + E)$. For constant $K$, this is strictly $O(V + E)$.

---

## Multi-Source BFS (Simultaneous Wavefront Propagation)
**Problem:** Find the minimum distance from every vertex in a graph to the **nearest** source from a given set of source vertices $S = \{s_1, s_2, \dots, s_k\}$ (e.g., nearest fire station).

### Elegant Technique (Virtual Super-Source):
Instead of running BFS $k$ separate times ($O(k(V + E))$):
1. Initialize a single queue $Q$.
2. Enqueue **all sources $s \in S$ simultaneously** at time 0 with $s.d = 0$ and $s.color = \text{GREY}$.
3. Run standard BFS once.
- All sources expand their frontiers concurrently.
- Each vertex is visited once by its closest source.
- **Time Complexity:** Exactly $O(V + E)$!

---

## Bidirectional BFS (Frontier Meet-in-the-Middle)
**Problem:** Find the shortest path between single source $s$ and target $t$.

### Principle:
If branching factor is $b$ and distance is $d$:
- Standard BFS visits $\approx b^d$ vertices.
- Running two simultaneous BFS searches—one forward from $s$ and one backward from $t$—meets in the middle at depth $d/2$.
- Number of visited nodes:
  $$2 \times b^{d/2} \ll b^d$$
- Example: If $b = 10, d = 6$:
  - Standard BFS: $10^6 = 1,000,000$ nodes.
  - Bidirectional BFS: $2 \times 10^3 = 2,000$ nodes (a **$500\times$ speedup**!).

---

## BFS on Implicit State Spaces (Grid Mazes, Word Ladders, Sliding Puzzles)
In many real-world problems, the graph is not explicitly stored in memory as an adjacency list. Instead, vertices represent **states** and edges represent **valid transitions**.
- **Grid Mazes:** Moving Up, Down, Left, Right between open cells `'.'`.
- **Word Ladder:** Transforming "hit" $\to$ "cog" by changing one letter at a time, each intermediate state being a valid dictionary word.
- **8-Puzzle:** Permutations of a $3 \times 3$ sliding board.

BFS is the optimal search algorithm for implicit state spaces because it guarantees finding the **solution with minimum moves/transitions**.

---

## Finding the Shortest Cycle (Girth) in an Unweighted Graph
To find the shortest cycle (girth) of an unweighted undirected graph:
1. For each vertex $v \in V$:
   - Run BFS rooted at $v$.
   - Maintain the BFS distance $d$ from $v$ and parent $p$.
   - When inspecting edge $(x, y)$, if $y$ is already visited and $y \neq x.p$:
     $$\text{Cycle Length} = x.d + y.d + 1$$
   - Record the minimum cycle length found.
2. The minimum over all $V$ BFS runs is the girth of the graph.
- **Time Complexity:** $V \times O(V + E) = O(V^2 + VE)$.

---

# 7. Deep Intuitive Insights, Common Traps & Reasoning Questions

### Can BFS find shortest paths in graphs with weighted edges?
**Answer:** NO, not in general.
BFS implicitly assumes that traversing any edge costs exactly 1 unit of distance. If edges have varying positive weights, a path with 3 edges of weight 1 (total cost 3) is shorter than a path with 1 edge of weight 10 (total cost 10). BFS would examine the 1-edge path first and declare its distance 10, failing to find the true shortest path of weight 3.
*(Exception: graphs where all edge weights are identical, or 0-1 graphs handled via Deque).*

---

### Does changing the order of neighbors in an adjacency list change the BFS distances?
**Answer: NO.**
While changing the order of neighbors in $Adj[u]$ may change the **order in which vertices at the same level are enqueued** and may produce a **different BFS tree structure** ($v.p$ pointers), it will **NEVER change the shortest path distances $v.d$**. The level sets remain mathematically invariant.

---

### Can a black vertex ever be adjacent to a white vertex in BFS?
**Answer: NEVER.**
This is the core invariant of BFS. A vertex $u$ is turned BLACK only after all edges incident to $u$ have been fully scanned. When scanning an edge $(u, v)$, if $v$ was WHITE, it was immediately colored GREY and enqueued. Thus, by the time $u$ becomes BLACK, every neighbor $v$ is either GREY or BLACK.

---

### Why does Double BFS fail to find the diameter of a directed tree (DAG)?
**Answer:** In a directed tree, edges can only be traversed in one direction. Running BFS from an arbitrary node might lead to a leaf with out-degree 0 after only 1 step, completely missing longer branches located upstream or in disjoint subtrees.

---

# 8. Exhaustive Conceptual True / False Question Bank (With Deep Justifications)

---

### Q1: In an unweighted undirected graph, BFS is guaranteed to find the shortest path between any two reachable vertices.
> **Answer: TRUE.**
> **Justification:** By the BFS Shortest Path Theorem, BFS explores vertices in strictly non-decreasing order of distance from the source. The first time a target vertex is reached, the path taken has the minimum possible number of edges.

---

### Q2: In an undirected graph, a cross edge in a BFS tree can connect two vertices whose levels differ by 2.
> **Answer: FALSE.**
> **Justification:** By the Level-Difference Lemma, for any edge $(u, v)$ in an undirected graph, $|u.d - v.d| \le 1$. If $u.d = k$, then when $u$ is explored, neighbor $v$ must have distance at most $k + 1$. A distance difference of 2 or more is mathematically impossible.

---

### Q3: An undirected graph has a cycle if and only if BFS discovers a cross edge connecting two vertices at the same level.
> **Answer: FALSE.**
> **Justification:** An undirected cycle can also be formed by a cross edge connecting vertices at **adjacent levels** ($|u.d - v.d| = 1$). For example, a 4-cycle $C_4$ has cross edges between level 1 and level 2. Same-level cross edges indicate **odd-length cycles**, while adjacent-level cross edges can form **even-length cycles**.

---

### Q4: For a graph represented as an adjacency matrix, the running time of BFS is $O(V + E)$.
> **Answer: FALSE.**
> **Justification:** In an adjacency matrix, finding all neighbors of a dequeued vertex requires iterating through all $V$ entries in its row, regardless of how few edges exist. Across all $V$ vertices, this takes $\sum_{u \in V} O(V) = O(V^2)$ time.

---

### Q5: At any point during BFS execution, the queue can contain vertices with three distinct distance values (e.g., distances 2, 3, and 4).
> **Answer: FALSE.**
> **Justification:** By the Queue Monotonicity Lemma, $v_{\text{tail}}.d \le v_{\text{head}}.d + 1$. The distances present in the queue at any given instant can span at most two consecutive integer values $\{k, k + 1\}$.

---

### Q6: If an undirected graph is disconnected, BFS called with a single source will terminate and leave some vertices colored white.
> **Answer: TRUE.**
> **Justification:** A single invocation of `BFS(G, s)` visits only the connected component containing $s$. Vertices in other disconnected components remain unreachable, keeping their initial WHITE color and $\infty$ distance.

---

### Q7: In a directed graph, a BFS tree cannot contain any back edges.
> **Answer: FALSE.**
> **Justification:** Directed BFS trees frequently contain back edges (edges directed from a descendant to an ancestor). For instance, a directed cycle $s \to a \to b \to s$ produces tree edges $s \to a$ and $a \to b$, while edge $b \to s$ is a back edge. (It is *forward edges* that cannot exist in directed BFS!).

---

### Q8: The Double BFS algorithm finds the exact diameter of any connected, unweighted graph.
> **Answer: FALSE.**
> **Justification:** Double BFS is provably correct only for **trees** (acyclic graphs). On graphs with cycles, it can get trapped in local topological features and fail to find the true graph diameter.

---

### Q9: A graph is bipartite if and only if running BFS on every component produces no cross edges between vertices at the same level.
> **Answer: TRUE.**
> **Justification:** An edge between two vertices at the same level $u.d == v.d$ forms an odd-length cycle of length $2 \cdot u.d + 1 - 2 \cdot \text{LCA}.d$. A graph is bipartite if and only if it has no odd cycles.

---

### Q10: In 0-1 BFS, pushing 0-weight edges to the front and 1-weight edges to the back of a deque guarantees $O(V + E)$ running time.
> **Answer: TRUE.**
> **Justification:** The 0-weight edges do not increment the current distance, so placing them at the front maintains the non-decreasing monotonicity of the deque without requiring a priority queue. Every vertex is enqueued/dequeued at most a constant number of times.

---

### Q11: The Universal Sink problem on an $n \times n$ adjacency matrix can be solved in $O(V)$ time.
> **Answer: TRUE.**
> **Justification:** Using the two-pointer elimination technique, every test of $A[i, j]$ eliminates either row $i$ or column $j$ from candidacy, identifying the sole potential sink in at most $2n$ tests.

---

### Q12: If all vertices in an undirected graph have degree at least 2, the graph must contain a cycle.
> **Answer: TRUE.**
> **Justification:** A tree or forest with $n$ vertices and $k$ components has $|E| = n - k \le n - 1$ edges. The sum of degrees is $2|E| \le 2n - 2$. If every vertex has degree $\ge 2$, the sum of degrees is $\ge 2n$, meaning $|E| \ge n > n - 1$. By the Pigeonhole Principle, a cycle must exist.

---

### Q13: Multi-Source BFS with $k$ sources requires $O(k(V + E))$ time.
> **Answer: FALSE.**
> **Justification:** By enqueuing all $k$ sources into the queue initially with distance 0, BFS runs in a single unified pass taking exactly $O(V + E)$ time.

---

### Q14: In BFS, once a vertex is colored black, its distance $d$ will never be updated again.
> **Answer: TRUE.**
> **Justification:** In standard BFS, a vertex is assigned its final, provably optimal shortest-path distance the moment it is discovered (turned GREY). By the time it turns BLACK, it has been dequeued and is never processed again.

---

### Q15: Bidirectional BFS always expands strictly fewer nodes than unidirectional BFS.
> **Answer: FALSE.**
> **Justification:** While bidirectional BFS typically reduces the search space from $O(b^d)$ to $O(b^{d/2})$, in pathological graphs (e.g., highly dense graphs or when one frontier expands to encompass almost all vertices immediately), the number of visited nodes can be comparable to unidirectional BFS.

---

### Q16: The space complexity of BFS can exceed $O(V)$ if the graph is dense ($E = \Theta(V^2)$).
> **Answer: FALSE.**
> **Justification:** The queue and state arrays (`color`, `d`, `p`) store at most one entry per **vertex**. The number of edges $E$ does not increase the queue's capacity, which is bounded by $|V|$. Auxiliary space remains $\Theta(V)$.

---

### Q17: A BFS tree is always a Minimum Spanning Tree (MST) in an unweighted connected graph.
> **Answer: TRUE.**
> **Justification:** In an unweighted graph, all edge weights equal 1. Any spanning tree has exactly $|V| - 1$ edges and total weight $|V| - 1$. Since every spanning tree has the identical total weight, every spanning tree—including any BFS tree—is a valid MST.

---

### Q18: Reversing all edges in a directed graph preserves the BFS distances from a source $s$.
> **Answer: FALSE.**
> **Justification:** Reversing edges reverses path reachability. If $s \to u$ was a directed path, reversing edges creates path $u \to s$. $u$ may no longer even be reachable from $s$ in the reversed graph.

---

### Q19: In BFS on an unweighted graph, the predecessor pointers $v.p$ form a tree where every path from root $s$ to any vertex $v$ is a shortest path.
> **Answer: TRUE.**
> **Justification:** By the inductive correctness of BFS, following predecessor pointers $v \to v.p \to v.p.p \to \dots \to s$ reconstructs a path with exactly $v.d = \delta(s, v)$ edges.

---

### Q20: Dial's algorithm is preferred over 0-1 BFS when edge weights can be arbitrary non-negative real numbers.
> **Answer: FALSE.**
> **Justification:** Dial's algorithm requires integer weights bounded by a small maximum $K$, as it allocates an array of $K + 1$ buckets. For arbitrary real-valued weights, standard Dijkstra with a Fibonacci or binary heap must be used.

---

### Q21: An undirected graph contains an odd-length cycle if and only if BFS discovers an edge between two vertices with $u.d == v.d$.
> **Answer: TRUE.**
> **Justification:** An edge between two vertices at identical distance $k$ creates a cycle of length $k + k + 1 - 2 \cdot \text{dist}(\text{LCA}) = 2(k - \text{dist}(\text{LCA})) + 1$, which is algebraically odd.

---

### Q22: If graph $G$ has $|V|$ vertices and $|E| = |V| - 1$ edges and is connected, it is a tree.
> **Answer: TRUE.**
> **Justification:** This is a fundamental theorem of graph theory: any connected graph with $|V|$ vertices and $|V| - 1$ edges is minimally connected and acyclic, hence a tree.

---

### Q23: The order of vertices in the adjacency list affects whether a graph is reported as bipartite by BFS.
> **Answer: FALSE.**
> **Justification:** Bipartiteness is an intrinsic topological property of the graph (absence of odd cycles). Adjacency list order may alter the specific traversal path, but it will never create or destroy an odd cycle.

---

### Q24: In a grid maze where diagonal moves are permitted, standard BFS can still find the shortest path in terms of number of moves.
> **Answer: TRUE.**
> **Justification:** As long as each move (orthogonal or diagonal) costs 1 move, the graph remains unweighted, and standard BFS finds the path with the minimum number of moves.

---

### Q25: Breadth-First Search can be implemented using a Stack instead of a Queue without altering its level-order exploration.
> **Answer: FALSE.**
> **Justification:** A stack enforces Last-In, First-Out (LIFO) semantics, which dives deep into the graph and transforms the traversal into Depth-First Search (DFS). FIFO queue ordering is mandatory for level-by-level breadth exploration.

---

### Q26: The depth of a breadth-first search tree on an undirected graph $G = (V, E)$ from an arbitrary vertex $v \in V$ is always equal to the diameter of the graph $G$. (ClassPractice Q5c)
> **Answer: FALSE.**
> **Justification:** The depth of a BFS tree rooted at $v$ is merely the **eccentricity** $\epsilon(v) = \max_u \delta(v, u)$ of vertex $v$, which satisfies $\text{Radius}(G) \le \text{depth}(v) \le \text{Diameter}(G)$. In a path graph $P_5$ ($v_1 - v_2 - v_3 - v_4 - v_5$), starting BFS at center $v_3$ yields a tree of depth 2, whereas the true graph diameter is $\delta(v_1, v_5) = 4$.

---

### Q27: Running two BFS traversals (Double BFS) is guaranteed to compute the exact diameter of any connected undirected graph.
> **Answer: FALSE.**
> **Justification:** Double-BFS is provably correct **only for acyclic graphs (trees)**. On general graphs with cycles, the farthest node from an arbitrary starting vertex may lie on a cycle rather than at an extremal diameter boundary. For example, in a 4-cycle $C_4$ with two opposing leaves $x, y$ attached, Double-BFS starting at a cycle node can output diameter 2 instead of the true diameter 4.

---

### Q28: If graph $G$ is unweighted, running Bellman-Ford and running BFS from source $s$ will always produce the exact same predecessor tree $\pi$.
> **Answer: FALSE.**
> **Justification:** Although both algorithms compute identical shortest-path distances $d[v] = \delta(s, v)$, tie-breaking between multiple shortest paths of equal length depends on edge scanning order. BFS resolves ties by FIFO queue arrival, whereas Bellman-Ford resolves ties by the static permutation of its global edge list, potentially producing different shortest-path trees.

