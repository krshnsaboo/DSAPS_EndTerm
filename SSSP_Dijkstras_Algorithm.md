# Single-Source Shortest Paths II: Dijkstra's Algorithm & Priority Queues

---

## Table of Contents
1. [Core Principles & The Greedy Choice Paradigm](#1-core-principles--the-greedy-choice-paradigm)
   - [The Non-Negative Weight Invariant ($\omega(e) \ge 0$)](#the-non-negative-weight-invariant-\omegae-\ge-0)
   - [The Explored Set $S$ and Frontier Queue $Q$](#the-explored-set-s-and-frontier-queue-q)
   - [Complete Pseudocode](#complete-pseudocode)
2. [Step-by-Step Execution Trace (Lecture Benchmark Graph)](#2-step-by-step-execution-trace-lecture-benchmark-graph)
   - [The 5-Vertex Benchmark Graph (Slides 2–7)](#the-5-vertex-benchmark-graph-slides-27)
   - [Detailed Iteration Table ($S, Q, d, \pi$)](#detailed-iteration-table-s-q-d-\pi)
3. [Theoretical Rigor: Mathematical Proof of Correctness](#3-theoretical-rigor-mathematical-proof-of-correctness)
   - [Proof by Contradiction (Slide 10 Structure)](#proof-by-contradiction-slide-10-structure)
   - [Why the Non-Negative Edge Weight Assumption is Non-Negotiable](#why-the-non-negative-edge-weight-assumption-is-non-negotiable)
4. [Priority Queue Implementations & Complexity Analysis (Slide 8–9)](#4-priority-queue-implementations--complexity-analysis-slide-89)
   - [Operation Breakdown: `Extract-Min`, `Relax`, and `Decrease-Key`](#operation-breakdown-extract-min-relax-and-decrease-key)
   - [Linear Array (Unsorted List): $O(V^2)$](#linear-array-unsorted-list-ov2)
   - [Binary Min-Heap: $O((V + E) \log V)$](#binary-min-heap-ov--e-\log-v)
   - [Fibonacci Heap: $O(V \log V + E)$](#fibonacci-heap-ov-\log-v--e)
   - [Mega-Comparison Table of Priority Queues](#mega-comparison-table-of-priority-queues)
5. [In-Depth Analysis of Slide Questions & Edge Scenarios (Slides 11–13)](#5-in-depth-analysis-of-slide-questions--edge-scenarios-slides-1113)
   - [Slide 11 Q1: What Happens When Negative Edges Are Introduced?](#slide-11-q1-what-happens-when-negative-edges-are-introduced)
   - [Slide 11 Q2: Can Dijkstra Be Modified to Handle Negative Weights? (SPFA)](#slide-11-q2-can-dijkstra-be-modified-to-handle-negative-weights-spfa)
   - [Slide 12 Q3: Bounded Small Integer Weights ($1 \le w \le W$): Dial's Algorithm](#slide-12-q3-bounded-small-integer-weights-1-\le-w-\le-w-dials-algorithm)
   - [Slide 13 Q4: Shortest Paths with at Most $K$ Edges (Layered Graph / DP)](#slide-13-q4-shortest-paths-with-at-most-k-edges-layered-graph--dp)
6. [Exam Deep-Dive: Negative Weights, Traps & Counterexamples (ClassPractice Analysis)](#6-exam-deep-dive-negative-weights-traps--counterexamples-classpractice-analysis)
   - [ClassPractice Q13: Step-by-Step Dijkstra Trace on Graph with Negative Edges](#classpractice-q13-step-by-step-dijkstra-trace-on-graph-with-negative-edges)
   - [ClassPractice Q10: Dijkstra Vertex Visitation Order Trace](#classpractice-q10-dijkstra-vertex-visitation-order-trace)
   - [ClassPractice Q15: Critical Theorems on Negative Edges & Longest Paths](#classpractice-q15-critical-theorems-on-negative-edges--longest-paths)
   - [ClassPractice Q5a: Bellman-Ford vs. Dijkstra Shortest-Path Tree Divergence](#classpractice-q5a-bellman-ford-vs-dijkstra-shortest-path-tree-divergence)
   - [Why Naive Reweighting ($w + C$) Destroys Shortest Paths](#why-naive-reweighting-w--c-destroys-shortest-paths)
7. [Architectural Insights: Dijkstra vs. BFS vs. Prim's MST](#7-architectural-insights-dijkstra-vs-bfs-vs-prims-mst)
8. [Exhaustive Conceptual True / False Question Bank (With Deep Justifications)](#8-exhaustive-conceptual-true--false-question-bank-with-deep-justifications)

---

# 1. Core Principles & The Greedy Choice Paradigm

## The Non-Negative Weight Invariant ($\omega(e) \ge 0$)
Dijkstra's algorithm solves the Single-Source Shortest Paths (SSSP) problem under the strict precondition that **all edge weights are non-negative**:
$$\omega(u, v) \ge 0 \quad \forall (u, v) \in E$$

### The Core Greedy Intuition:
If all edge weights are non-negative, traversing additional edges can **never decrease** the total weight of a path.
Therefore, if we select a vertex $u$ with the minimum tentative distance estimate among all unfinalized vertices, **no future path through any other unfinalized vertex can ever reach $u$ with a smaller weight**. Its current distance estimate is guaranteed to be its final shortest-path distance!

---

## The Explored Set $S$ and Frontier Queue $Q$

Dijkstra maintains two partitions of the vertex set $V$:
1. **Set $S$ (Finalized Vertices):** Vertices for which the true shortest-path distance from source $s$ has already been determined ($d[u] = \delta(s, u)$).
2. **Priority Queue $Q = V \setminus S$ (Unfinalized Frontier):** Vertices whose shortest paths are still tentative, keyed on their current distance estimates $d[v]$.

```
       [ Finalized Set S ]                 [ Frontier Queue Q = V - S ]
     ┌──────────────────────┐            ┌───────────────────────────────┐
     │  (s)   (x)           │            │  (u: 7)     (v: 13)    (y: 8) │
     │  d=0   d=5           │            └───────────────┬───────────────┘
     └──────────────────────┘                            │
                ▲                                        │ Extract-Min:
                │                                        ▼ selects (u: 7)
                └────────────────────────────────────────┘
```

---

## Complete Pseudocode

```text
DIJKSTRA(G, w, s):
    INITIALIZE-SINGLE-SOURCE(G, s)
    S = ∅
    Q = BUILD-PRIORITY-QUEUE(G.V) // keyed by d[v]
    
    while not IS-EMPTY(Q) do
        u = EXTRACT-MIN(Q)
        S = S ∪ {u}
        
        for each v ∈ G.Adj[u] do
            if v ∈ Q and d[v] > d[u] + w(u, v) then
                d[v] = d[u] + w(u, v)
                π[v] = u
                DECREASE-KEY(Q, v, d[v])
```

---

# 2. Step-by-Step Execution Trace (Lecture Benchmark Graph)

## The 5-Vertex Benchmark Graph (Slides 2–7)
Vertices: $\{s, u, v, x, y\}$.
Edges and weights:
- $(s, u) = 10$, $(s, x) = 5$
- $(u, v) = 1$, $(u, x) = 2$
- $(x, u) = 3$, $(x, v) = 9$, $(x, y) = 2$
- $(v, y) = 4$
- $(y, s) = 7$, $(y, v) = 6$

Source: $s$.

```
           (u) ────── 1 ──────► (v)
          ▲ │ \                 │ ▲
       10/  │  \2               │  \4
        /   │   ▼               │   \
      (s)   │3   (x) ──── 2 ──►(y)───┘ (v to y is 4, y to v is 6)
        \   │   ▲              /
        5\  ▼  /              /7
          ►(x) ──────────────┘
```

---

## Detailed Iteration Table ($S, Q, d, \pi$)

### Step 0: Initialization (Slide 2)
- $d[s] = 0, \quad d[u] = \infty, \quad d[v] = \infty, \quad d[x] = \infty, \quad d[y] = \infty$.
- $S = \emptyset$.
- $Q = \{s: 0, \; u: \infty, \; v: \infty, \; x: \infty, \; y: \infty\}$.

---

### Iteration 1: Extract-Min $\implies u = s$ ($d[s] = 0$) (Slide 3)
- Add $s$ to $S \implies S = \{s\}$.
- Relax neighbors of $s$:
  - $(s, u, 10): d[u] = \min(\infty, 0 + 10) = \mathbf{10}, \; \pi[u] = s$.
  - $(s, x, 5): d[x] = \min(\infty, 0 + 5) = \mathbf{5}, \; \pi[x] = s$.
- $Q = \{x: 5, \; u: 10, \; y: \infty, \; v: \infty\}$.

---

### Iteration 2: Extract-Min $\implies u = x$ ($d[x] = 5$) (Slide 4)
- Add $x$ to $S \implies S = \{s, x\}$.
- Relax neighbors of $x$:
  - $(x, u, 3): d[u] = \min(10, 5 + 3) = \mathbf{8}, \; \pi[u] = x$.
  - $(x, v, 9): d[v] = \min(\infty, 5 + 9) = \mathbf{14}, \; \pi[v] = x$.
  - $(x, y, 2): d[y] = \min(\infty, 5 + 2) = \mathbf{7}, \; \pi[y] = x$.
- $Q = \{y: 7, \; u: 8, \; v: 14\}$.

---

### Iteration 3: Extract-Min $\implies u = y$ ($d[y] = 7$) (Slide 5)
- Add $y$ to $S \implies S = \{s, x, y\}$.
- Relax neighbors of $y$:
  - $(y, s, 7): s \in S$ (ignored / no change).
  - $(y, v, 6): d[v] = \min(14, 7 + 6) = \mathbf{13}, \; \pi[v] = y$.
- $Q = \{u: 8, \; v: 13\}$.

---

### Iteration 4: Extract-Min $\implies u = u$ ($d[u] = 8$) (Slide 6)
- Add $u$ to $S \implies S = \{s, x, y, u\}$.
- Relax neighbors of $u$:
  - $(u, x, 2): x \in S$ (ignored / no change).
  - $(u, v, 1): d[v] = \min(13, 8 + 1) = \mathbf{9}, \; \pi[v] = u$.
- $Q = \{v: 9\}$.

---

### Iteration 5: Extract-Min $\implies u = v$ ($d[v] = 9$) (Slide 7)
- Add $v$ to $S \implies S = \{s, x, y, u, v\} = V$.
- Relax neighbors of $v$:
  - $(v, y, 4): y \in S$ (ignored).
- $Q = \emptyset \implies$ Algorithm terminates!

### Final Results:
| Vertex | Distance $\delta(s, \cdot)$ | Predecessor $\pi$ | Shortest Path |
| :---: | :---: | :---: | :--- |
| **$s$** | 0 | NIL | $s$ |
| **$x$** | 5 | $s$ | $s \to x$ |
| **$y$** | 7 | $x$ | $s \to x \to y$ |
| **$u$** | 8 | $x$ | $s \to x \to u$ |
| **$v$** | 9 | $u$ | $s \to x \to u \to v$ |

---

# 3. Theoretical Rigor: Mathematical Proof of Correctness

> **Theorem:** Let Dijkstra's algorithm be run on a weighted, directed graph $G = (V, E)$ with non-negative weight function $\omega \ge 0$ and source $s$. Then upon termination:
> $$d[v] = \delta(s, v) \quad \forall v \in V$$

---

## Proof by Contradiction (Slide 10 Structure)

Assume for the sake of contradiction that there exists at least one vertex for which $d[v] \neq \delta(s, v)$ upon being added to $S$.
Let $u$ be the **very first vertex added to $S$** for which $d[u] \neq \delta(s, u)$.

By the Upper-Bound Property, $d[u] \ge \delta(s, u)$ always.
Therefore, our assumption implies:
$$d[u] > \delta(s, u)$$

Notice that $u \neq s$, because $s$ is the first vertex added to $S$ and $d[s] = 0 = \delta(s, s)$. Thus, $S \neq \emptyset$ when $u$ is chosen.
Also, $u$ must be reachable from $s$ (otherwise $d[u] = \infty = \delta(s, u)$).

```
         (s) ──────► ... ──────► (x) ──────► (y) ──────► ... ──────► (u)
        [ ────────── In S ────────── ]      [ ──────── Not in S ──────── ]
```

Consider a true shortest path $p$ from $s$ to $u$:
$$p: s \leadsto u$$
Path $p$ starts in $S$ (at $s$) and ends outside $S$ (at $u$).
Let $(x, y)$ be the **first edge on path $p$ that leaves set $S$**:
- $x \in S$
- $y \in V \setminus S$ (possibly $y = u$)

### Deductions:
1. **$x$ is in $S$ before $u$ was selected:**
   Because $u$ was chosen as the *very first* vertex where $d \neq \delta$, vertex $x$ received its exact shortest-path distance:
   $$d[x] = \delta(s, x)$$
2. **$y$ received its optimal distance when $x$ was added to $S$:**
   When $x$ was added to $S$, all its outgoing edges were relaxed, including edge $(x, y)$.
   By the Convergence Property:
   $$d[y] = \delta(s, y)$$
3. **Comparing $\delta(s, y)$ and $\delta(s, u)$:**
   Because $y$ appears on a shortest path from $s$ to $u$, and **all edge weights are non-negative** ($\omega \ge 0$):
   $$\delta(s, y) \le \delta(s, u)$$
4. **Greedy Selection of $u$ over $y$:**
   Both $u$ and $y$ were present in queue $Q$ when the algorithm chose $u$ via `Extract-Min`:
   $$d[u] \le d[y]$$

### Combining Inequalities:
$$d[u] \le d[y] = \delta(s, y) \le \delta(s, u)$$
Thus:
$$d[u] \le \delta(s, u)$$
Combined with the Upper-Bound Property ($d[u] \ge \delta(s, u)$):
$$d[u] = \delta(s, u)$$

**Direct Contradiction!** This contradicts our initial premise that $d[u] > \delta(s, u)$.
Therefore, no such vertex $u$ can exist. Every vertex added to $S$ has $d[v] = \delta(s, v)$. $\blacksquare$

---

## Why the Non-Negative Edge Weight Assumption is Non-Negotiable

Where in the proof above did we rely on $\omega \ge 0$?
In **Step 3**:
$$\delta(s, y) \le \delta(s, u)$$
This holds **if and only if the remaining subpath from $y$ to $u$ has weight $\ge 0$**.
If there are negative edge weights on path $y \leadsto u$, it is entirely possible that:
$$\delta(s, u) < \delta(s, y)$$
If so, $u$ could have a smaller true distance than $y$, but because $d[u]$ has not been relaxed yet, $y$ has a smaller tentative estimate and gets extracted first, breaking the greedy invariant.

---

# 4. Priority Queue Implementations & Complexity Analysis (Slide 8–9)

## Operation Breakdown: `Extract-Min`, `Relax`, and `Decrease-Key`

The running time of Dijkstra's algorithm is governed by three primary operations:
1. `BUILD-MIN-HEAP`: Executed once on $|V|$ vertices.
2. `EXTRACT-MIN` (PickMin): Executed **$|V|$ times** (once per vertex).
3. `DECREASE-KEY` (Update): Executed at most **$|E|$ times** (once per successful edge relaxation).

---

## Linear Array (Unsorted List): $O(V^2)$
- Store $d[v]$ in an unsorted array of size $V$.
- `EXTRACT-MIN`: Linear scan over all remaining vertices in $V \setminus S \implies \Theta(V)$.
  - Total `EXTRACT-MIN` time: $V \times \Theta(V) = \Theta(V^2)$.
- `DECREASE-KEY`: Direct array indexing $d[v] = \text{new\_val} \implies \Theta(1)$.
  - Total `DECREASE-KEY` time: $E \times \Theta(1) = \Theta(E)$.

**Total Running Time:**
$$T(V, E) = \Theta(V^2 + E) = \Theta(V^2)$$
> [!TIP]
> For **dense graphs** where $|E| = \Theta(V^2)$, the array implementation runs in $\Theta(V^2)$, which is asymptotically optimal and often faster than binary heaps due to zero pointer overhead and perfect cache locality.

---

## Binary Min-Heap: $O((V + E) \log V)$
- Maintain a binary heap of vertices keyed on $d[v]$.
- `BUILD-MIN-HEAP`: $O(V)$.
- `EXTRACT-MIN`: Root removal followed by `Heapify` $\implies \Theta(\log V)$.
  - Total `EXTRACT-MIN` time: $V \times \Theta(\log V) = \Theta(V \log V)$.
- `DECREASE-KEY`: Sift-up operation in heap $\implies \Theta(\log V)$.
  - Total `DECREASE-KEY` time: $E \times \Theta(\log V) = \Theta(E \log V)$.

**Total Running Time:**
$$T(V, E) = \Theta((V + E) \log V)$$
For sparse graphs where $|E| = \Theta(V)$:
$$T(V, E) = \Theta(V \log V)$$

---

## Fibonacci Heap: $O(V \log V + E)$
- A collection of heap-ordered trees supporting lazy merges.
- `EXTRACT-MIN`: Amortized $O(\log V)$.
  - Total `EXTRACT-MIN` time: $O(V \log V)$.
- `DECREASE-KEY`: Cascading cuts achieve **amortized $O(1)$ time**!
  - Total `DECREASE-KEY` time: $E \times O(1) = O(E)$.

**Total Running Time:**
$$T(V, E) = O(V \log V + E)$$

---

## Mega-Comparison Table of Priority Queues

| Priority Queue Data Structure | `EXTRACT-MIN` Cost | `DECREASE-KEY` Cost | Total SSSP Time | Sparse Graph ($E = O(V)$) | Dense Graph ($E = \Theta(V^2)$) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Unsorted Array** | $\Theta(V)$ | $\Theta(1)$ | $\Theta(V^2)$ | $\Theta(V^2)$ | **$\Theta(V^2)$ (Optimal)** |
| **Binary Min-Heap** | $\Theta(\log V)$ | $\Theta(\log V)$ | $\Theta((V + E)\log V)$ | **$\Theta(V \log V)$** | $\Theta(V^2 \log V)$ |
| **d-ary Heap ($d = E/V$)** | $\Theta(d \log_d V)$ | $\Theta(\log_d V)$ | $\Theta(E \log_{E/V} V)$ | $\Theta(V \log V)$ | $\Theta(V^2)$ |
| **Fibonacci Heap** | $O(\log V)$ (amortized) | **$O(1)$ (amortized)** | **$O(V \log V + E)$** | **$O(V \log V)$** | **$\Theta(V^2)$** |

---

# 5. In-Depth Analysis of Slide Questions & Edge Scenarios (Slides 11–13)

## Slide 11 Q1: What Happens When Negative Edges Are Introduced?

**Question (Slide 11):** *What happens when you have some negative edges in Dijkstra?*

### The Concrete Counterexample:
Consider vertices $\{s, A, B\}$:
- Edge $(s, A) = 2$
- Edge $(s, B) = 5$
- Edge $(A, B) = -4$

```
          (s)
         /   \
       2/     \5
       ▼       ▼
     (A) ─────► (B)
          -4
```

### Dijkstra Execution:
1. Initialize: $d[s] = 0, d[A] = \infty, d[B] = \infty$.
2. Extract $s$: relaxes $(s, A) \implies d[A] = 2$; relaxes $(s, B) \implies d[B] = 5$.
3. $Q = \{A: 2, B: 5\}$.
4. `Extract-Min` selects $A$ ($d[A] = 2$). **$A$ is added to $S$ (Finalized!)**.
5. Relax $(A, B) \implies d[B] = \min(5, 2 + (-4)) = \mathbf{-2}$.
6. `Extract-Min` selects $B$ ($d[B] = -2$). **$B$ is added to $S$ (Finalized!)**.
7. Distance returned: $d[B] = -2, d[A] = 2$.
Here it found the right answer.

Now reverse the roles:
- $(s, A) = 5$
- $(s, B) = 2$
- $(B, A) = -4$
1. Extract $s$: $d[B] = 2, d[A] = 5$.
2. Extract $B$: $d[B] = 2$ (Finalized!). Relaxes $(B, A) \implies d[A] = \min(5, 2 - 4) = -2$.
3. Extract $A$: $d[A] = -2$ (Finalized!).
Still works.

**Where does it FATALLY FAIL?**
Consider:
- $(s, A) = 1$
- $(s, B) = 2$
- $(B, C) = 2$
- $(C, A) = -5$

```
          (s)
         /   \
       1/     \2
       ▼       ▼
     (A)      (B) ──► 2 ──► (C)
      ▲                      │
      └───────── -5 ─────────┘
```
1. Extract $s$: $d[A] = 1, d[B] = 2$.
2. `Extract-Min` selects $A$ ($d[A] = 1$). **$A$ is permanently added to $S$!**
3. Next, $B$ is extracted ($d[B] = 2$), relaxes $C \implies d[C] = 4$.
4. Next, $C$ is extracted ($d[C] = 4$), relaxes $(C, A)$:
   - Candidate distance to $A$: $d[C] + (-5) = 4 - 5 = \mathbf{-1}$.
   - But **$A$ is already in $S$!**
   - Standard Dijkstra ignores finalized vertices in $S$.
   - **Result:** Dijkstra permanently reports $d[A] = 1$, when the true shortest path $s \to B \to C \to A$ has weight $-1$!

---

## Slide 11 Q2: Can Dijkstra Be Modified to Handle Negative Weights? (SPFA)

**Question (Slide 11):** *Can I modify Dijkstra's algorithm to work for negative weights?*

> **Answer: YES, but at the cost of its $O(V \log V + E)$ time guarantee!**

### The Modification (Permitting Re-Entry into $Q$):
Remove the permanent finalized set $S$. If relaxing an edge $(u, v)$ decreases $d[v]$, re-insert $v$ into queue $Q$ even if it was previously extracted!

```text
MODIFIED-DIJKSTRA-SPFA(G, w, s):
    INITIALIZE-SINGLE-SOURCE(G, s)
    Q = EMPTY_QUEUE()
    ENQUEUE(Q, s)
    in_queue[s] = TRUE
    count[s] = 1
    
    while not IS-EMPTY(Q) do
        u = DEQUEUE(Q)
        in_queue[u] = FALSE
        
        for each v ∈ G.Adj[u] do
            if d[v] > d[u] + w(u, v) then
                d[v] = d[u] + w(u, v)
                π[v] = u
                if not in_queue[v] then
                    ENQUEUE(Q, v)
                    in_queue[v] = TRUE
                    count[v] = count[v] + 1
                    if count[v] > |V| then
                        error "Negative cycle detected!"
```

### Consequences:
1. **Correctness:** Correctly computes shortest paths for graphs with negative edges (and detects negative cycles when any vertex is relaxed $> |V|$ times).
2. **Worst-Case Time Complexity:** Exponential in pathological graphs, or $\Theta(VE)$, exactly like Bellman-Ford!

---

## Slide 12 Q3: Bounded Small Integer Weights ($1 \le w \le W$): Dial's Algorithm

**Problem (Slide 12):**
*Suppose a graph $G = (V, E)$ has small integer edge weights in the range $1$ to $W$ where $W \ll |V|$. Solve SSSP in $O(E + WV) = O(E)$ time.*

### The Data Structure: Dial's Bucket Queue
In standard Dijkstra with edge weights bounded by $W$:
- When vertex $u$ is extracted with minimum distance $d[u]$, any edge $(u, v)$ with weight $w \le W$ produces a new distance:
  $$d[v] \le d[u] + W$$
- Therefore, at any single point in time, the distance values in the priority queue can span a range of **at most $W$ discrete integer values**:
  $$\text{Active Range} \subseteq [d[u], \; d[u] + W]$$

### Implementation via Circular Array of Buckets:
1. Allocate an array of $W + 1$ doubly linked lists (buckets), indexed $0, 1, \dots, W$.
2. Place vertex $v$ into bucket index:
   $$\text{bucket\_idx} = d[v] \pmod{W + 1}$$
3. To perform `Extract-Min`:
   - Maintain a pointer `current_dist` starting at 0.
   - Advance `current_dist` sequentially until finding the first non-empty bucket.
   - Pop a vertex from that bucket in $O(1)$ time.
4. To perform `Decrease-Key`:
   - Remove $v$ from its old bucket and splice it into its new bucket in $O(1)$ time.

```
Array of Buckets: [0..W]
Bucket 0:  [ u ] ──► [ v ]
Bucket 1:  empty
Bucket 2:  [ x ]
...
Bucket W:  [ y ]
```

### Complexity Analysis:
- `Decrease-Key`: $E \times O(1) = O(E)$.
- `Extract-Min`: The `current_dist` pointer only advances forward. The total distance advances by at most $W \cdot |V|$.
  - Total pointer advance steps: $O(W \cdot V)$.
- **Total Time Complexity:**
  $$T(V, E) = O(E + WV)$$
  When $W$ is a small constant (e.g., $W = O(1)$), this runs in **strictly linear $O(E)$ time**!

---

## Slide 13 Q4: Shortest Paths with at Most $K$ Edges (Layered Graph / DP)

**Problem (Slide 13):**
*Find the shortest path from source $s$ to all nodes with at most $K$ edges on a graph with non-negative weights.*

Why does standard Dijkstra fail? Standard Dijkstra cannot track the number of edges used to reach a node; it eagerly finalizes a path based purely on total weight, which might exhaust all $K$ edges prematurely.

### Solution 1: Dynamic Programming (Bellman-Ford Truncated to $K$ Passes)
Let $d[v, k]$ be the shortest path from $s$ to $v$ using **at most $k$ edges**:
$$d[v, k] = \min \left( d[v, k-1], \; \min_{(u, v) \in E} \{ d[u, k-1] + \omega(u, v) \} \right)$$
- Run exactly $K$ passes of Bellman-Ford relaxation.
- **Time Complexity:** $\Theta(K \cdot E)$.

### Solution 2: The Layered Graph Construction
Construct an expanded graph $G' = (V', E')$ consisting of $K + 1$ copies (layers) of the original graph:
- For each $v \in V$, create vertices $v^{(0)}, v^{(1)}, \dots, v^{(K)}$.
- Layer $i$ represents reaching a vertex using exactly $i$ edges.
- For every directed edge $(u, v) \in E$ with weight $w$:
  - Add directed edges from layer $k$ to layer $k + 1$:
    $$(u^{(k)}, v^{(k+1)}) \quad \text{with weight } w, \quad \forall 0 \le k < K$$
- Add 0-weight edges $(v^{(k)}, v^{(k+1)})$ to allow paths with strictly fewer than $K$ edges.
- **Run Dijkstra on $G'$** from source $s^{(0)}$:
  - $|V'| = (K + 1)|V|$
  - $|E'| = K|E|$
  - Running time: $O(K E \log(K V))$.

---

# 6. Exam Deep-Dive: Negative Weights, Traps & Counterexamples (ClassPractice Analysis)

## ClassPractice Q13: Step-by-Step Dijkstra Trace on Graph with Negative Edges

**Problem Statement (ClassPractice Quiz-2, Q13):**
> *Even though graph $G$ shown below has negative edges, fill in the blank with the shortest path to each of the node as given by Dijkstra’s algorithm with vertex $A$ as source vertex. Fill the shortest path weight in alphabetical order of vertices.*

```
                 (B) ──────── 2 ────────► (D)
                ▲   ▲                     │  ▲
              2/     \3                 2/    \1
              /       \                 ▼      \
            (A) ──7──► (C) ── -1 ─────►(D)     (G)
             │ \       │               ▲      ▲
           12│  \-4    │2               │     │
             ▼    \    ▼                │     │
            (E) ───►  (E) ── -7 ──────────────┘
              \      /
             -4\    / (Cycle A-E-A: 12 + (-4) = 8 > 0; no negative cycle)
```

### Graph Specification:
- **Vertices:** $V = \{A, B, C, D, E, F, G\}$
- **Directed Edges & Weights:**
  - Outgoing from $A$: $(A, B) = 2, \; (A, C) = 7, \; (A, E) = 12$
  - Outgoing from $B$: $(B, D) = 2$
  - Outgoing from $C$: $(C, B) = 3, \; (C, D) = -1, \; (C, E) = 2$
  - Outgoing from $D$: $(D, F) = 2$
  - Outgoing from $E$: $(E, A) = -4, \; (E, G) = -7$
  - Outgoing from $F$: $(F, G) = 2$
  - Outgoing from $G$: $(G, D) = 1$

---

### Step-by-Step Dijkstra Execution Trace:

#### Step 0: Initialization
- Source $s = A$.
- Distances: $d[A] = 0, \; d[B] = \infty, \; d[C] = \infty, \; d[D] = \infty, \; d[E] = \infty, \; d[F] = \infty, \; d[G] = \infty$.
- Predecessors: $\pi[v] = \text{NIL} \quad \forall v$.
- Explored Set $S = \emptyset$.
- Priority Queue $Q = \{A: 0, B: \infty, C: \infty, D: \infty, E: \infty, F: \infty, G: \infty\}$.

---

#### Iteration 1: Extract-Min $\implies u = A$ ($d[A] = 0$)
- Add $A$ to $S \implies S = \{A\}$.
- Relax outgoing edges from $A$:
  - Edge $(A, B, 2)$: $d[B] = \min(\infty, 0 + 2) = \mathbf{2}, \; \pi[B] = A$.
  - Edge $(A, C, 7)$: $d[C] = \min(\infty, 0 + 7) = \mathbf{7}, \; \pi[C] = A$.
  - Edge $(A, E, 12)$: $d[E] = \min(\infty, 0 + 12) = \mathbf{12}, \; \pi[E] = A$.
- $Q = \{B: 2, \; C: 7, \; E: 12, \; D: \infty, \; F: \infty, \; G: \infty\}$.

---

#### Iteration 2: Extract-Min $\implies u = B$ ($d[B] = 2$)
- Add $B$ to $S \implies S = \{A, B\}$.
- Relax outgoing edges from $B$:
  - Edge $(B, D, 2)$: $d[D] = \min(\infty, 2 + 2) = \mathbf{4}, \; \pi[D] = B$.
- $Q = \{D: 4, \; C: 7, \; E: 12, \; F: \infty, \; G: \infty\}$.

---

#### Iteration 3: Extract-Min $\implies u = D$ ($d[D] = 4$)
- Add $D$ to $S \implies S = \{A, B, D\}$.
- Relax outgoing edges from $D$:
  - Edge $(D, F, 2)$: $d[F] = \min(\infty, 4 + 2) = \mathbf{6}, \; \pi[F] = D$.
- $Q = \{F: 6, \; C: 7, \; E: 12, \; G: \infty\}$.

---

#### Iteration 4: Extract-Min $\implies u = F$ ($d[F] = 6$)
- Add $F$ to $S \implies S = \{A, B, D, F\}$.
- Relax outgoing edges from $F$:
  - Edge $(F, G, 2)$: $d[G] = \min(\infty, 6 + 2) = \mathbf{8}, \; \pi[G] = F$.
- $Q = \{C: 7, \; G: 8, \; E: 12\}$.

---

#### Iteration 5: Extract-Min $\implies u = C$ ($d[C] = 7$)
- Add $C$ to $S \implies S = \{A, B, D, F, C\}$.
- Relax outgoing edges from $C$:
  - Edge $(C, B, 3)$: $B \in S$ (already finalized with $d[B] = 2 \le 7 + 3$; ignored).
  - Edge $(C, D, -1)$: Candidate distance is $d[C] + \omega(C, D) = 7 + (-1) = 6$. But $d[D] = 4 \le 6$, so no update (and $D \in S$).
  - Edge $(C, E, 2)$: $d[E] = \min(12, 7 + 2) = \mathbf{9}, \; \pi[E] = C$.
- $Q = \{G: 8, \; E: 9\}$.

---

#### Iteration 6: Extract-Min $\implies u = G$ ($d[G] = 8$)
- Add $G$ to $S \implies S = \{A, B, D, F, C, G\}$.
- Relax outgoing edges from $G$:
  - Edge $(G, D, 1)$: Candidate distance is $d[G] + \omega(G, D) = 8 + 1 = 9$. But $d[D] = 4 \le 9$; no update.
- $Q = \{E: 9\}$.

---

#### Iteration 7: Extract-Min $\implies u = E$ ($d[E] = 9$)
- Add $E$ to $S \implies S = \{A, B, C, D, E, F, G\} = V$.
- Relax outgoing edges from $E$:
  - Edge $(E, A, -4)$: $A \in S$ ($9 - 4 = 5 > 0$; no update).
  - Edge $(E, G, -7)$: Candidate distance is $d[E] + \omega(E, G) = 9 + (-7) = \mathbf{2}$.
    **CRITICAL FAILURE POINT:**
    Vertex $G$ has **already been extracted and finalized in Iteration 6** with $d[G] = 8$!
    In standard Dijkstra's algorithm, vertices in $S$ are **never updated** ($v \in Q$ check fails).
    Even if an implementation permits checking vertices in $S$, $G$ was already dequeued, so Dijkstra will **not** propagate this reduction to $D$ or $F$!
- $Q = \emptyset \implies$ Algorithm terminates!

---

### Comparison: Dijkstra Output vs. True Shortest Paths

| Vertex | Value Given by Dijkstra | True Shortest Path $\delta(A, v)$ | True Path Sequence | Did Dijkstra Fail? |
| :---: | :---: | :---: | :--- | :---: |
| **$A$** | **0** | 0 | $A$ | No |
| **$B$** | **2** | 2 | $A \to B$ | No |
| **$C$** | **7** | 7 | $A \to C$ | No |
| **$D$** | **4** | 3 | $A \to C \to E \to G \to D$ ($7 + 2 - 7 + 1 = 3$) | **YES (Overestimated)** |
| **$E$** | **9** | 9 | $A \to C \to E$ ($7 + 2 = 9$) | No |
| **$F$** | **6** | 5 | $A \to C \to E \to G \to D \to F$ ($3 + 2 = 5$) | **YES (Overestimated)** |
| **$G$** | **8** | 2 | $A \to C \to E \to G$ ($7 + 2 - 7 = 2$) | **YES (Severe Error)** |

> [!IMPORTANT]
> **Final Answer to ClassPractice Q13:**
> In alphabetical order of vertices $(A, B, C, D, E, F, G)$, the shortest path weights given by Dijkstra's algorithm are:
> $$\mathbf{0, \; 2, \; 7, \; 4, \; 9, \; 6, \; 8}$$
> Notice that for $D, F, G$, Dijkstra's greedy choice prematurely committed to paths before discovering the super-cheap negative edge $(E, G, -7)$, resulting in sub-optimal values!

---

## ClassPractice Q10: Dijkstra Vertex Visitation Order Trace

**Problem Statement (ClassPractice Quiz-2, Q10):**
> *Fill in the blank with the order in which vertices are visited by Dijkstra when node $A$ is considered as the source. In case of multiple answers possible, you may write any correct solution.*

```
                 (B) ──────── 1 ──────── (E)
                /                         │
              4/                         2│
              /                           ▼
            (A) ─── 3 ───► (C) ─── 3 ───►(F)
              \                           │
             10\                         3│
                ▼                         ▼
                 (D) ──────── 2 ──────── (G)
                   \                     /
                    \────── 2 ──────────/
```
*(Undirected graph with edge weights: $(A, B)=4, (A, C)=3, (A, D)=10, (B, E)=1, (C, F)=3, (D, F)=2, (D, G)=2, (E, F)=2, (F, G)=3$)*.

### Execution Trace:
1. **Initialize:** $d[A] = 0$, all other $d = \infty$.
2. **Visit 1: Extract $A$** ($d[A] = 0$).
   - Relax neighbors of $A$:
     - $d[C] = 0 + 3 = 3$
     - $d[B] = 0 + 4 = 4$
     - $d[D] = 0 + 10 = 10$
3. **Visit 2: Extract $C$** ($\min(3, 4, 10) \implies d[C] = 3$).
   - Relax neighbors of $C$:
     - $d[F] = \min(\infty, 3 + 3) = 6$
4. **Visit 3: Extract $B$** ($\min(4, 6, 10) \implies d[B] = 4$).
   - Relax neighbors of $B$:
     - $d[E] = \min(\infty, 4 + 1) = 5$
5. **Visit 4: Extract $E$** ($\min(5, 6, 10) \implies d[E] = 5$).
   - Relax neighbors of $E$:
     - Edge $(E, F, 2)$: candidate distance $5 + 2 = 7 > d[F]=6$. $d[F]$ remains $6$.
6. **Visit 5: Extract $F$** ($\min(6, 10) \implies d[F] = 6$).
   - Relax neighbors of $F$:
     - Edge $(F, D, 2)$: $d[D] = \min(10, 6 + 2) = \mathbf{8}$.
     - Edge $(F, G, 3)$: $d[G] = \min(\infty, 6 + 3) = \mathbf{9}$.
7. **Visit 6: Extract $D$** ($\min(8, 9) \implies d[D] = 8$).
   - Relax neighbors of $D$:
     - Edge $(D, G, 2)$: candidate distance $8 + 2 = 10 > d[G]=9$. $d[G]$ remains $9$.
8. **Visit 7: Extract $G$** ($d[G] = 9$).

> [!TIP]
> **Definitive Visitation Order:**
> $$\mathbf{A \to C \to B \to E \to F \to D \to G}$$

---

## ClassPractice Q15: Critical Theorems on Negative Edges & Longest Paths

**Problem Statement (ClassPractice Quiz-2, Q15):**
> *Which of the following statements are true?*
> *(a) Changing the ordering in which edges are relaxed in the Bellman-Ford algorithm, never changes the correctness of the algorithm.*
> *(b) If all edges of a graph $G$ are negative, we can modify Dijkstra’s Algorithm to find the Shortest path in this case.*
> *(c) If all edges of a graph $G$ are negative, we can modify Dijkstra’s Algorithm to find the Longest path in this case.*
> *(d) There exists a graph $G$ with positive as well as negative edges, such that output of Dijkstra’s algorithm gives valid shortest path.*

### Rigorous Statement-by-Statement Proofs:

#### Statement (a): TRUE
- Bellman-Ford correctness is an asymptotic guarantee: as long as **every edge** in $E$ is relaxed once per outer iteration, an inductive proof shows that after iteration $k$, all shortest paths using at most $k$ edges are computed correctly. The internal ordering within a pass affects *early convergence*, but **never correctness**.

#### Statement (b): FALSE
- In a graph where all edges are negative, paths with more edges have smaller (more negative) weights. The core greedy property of Dijkstra—that extracting a vertex guarantees its distance will never decrease—is completely broken. If the graph contains any cycle, that cycle is a negative cycle ($\sum \omega < 0$), making shortest paths $-\infty$. Even on a DAG with negative edges, Dijkstra fails to explore multi-edge descending paths.

#### Statement (c): FALSE
- Finding the longest simple path in a general graph is **NP-hard** (reducible to Hamiltonian Path). If Dijkstra could be modified to find the longest path on negative graphs by negating weights or inverting min-heaps, we would have $P = NP$. Furthermore, negating all edge weights converts negative edges into positive edges, but finding the *longest* path in a graph with positive weights cannot be solved by Dijkstra greedy relaxation (a greedy choice cannot avoid getting trapped in sub-optimal branches).

#### Statement (d): TRUE
- Dijkstra's algorithm is a greedy heuristic that is **guaranteed** to work when $\omega(e) \ge 0$, but it is **not guaranteed to fail** when negative edges exist!
- **Concrete Existence Proof (Minimal Counterexample where Dijkstra Succeeds with Negative Edges):**
  Consider graph $G = (V, E)$ with vertices $\{s, a, b\}$:
  - $(s, a) = 5$
  - $(a, b) = -2$
  - $(s, b)$ does not exist.
  - Trace from source $s$:
    1. Extract $s$ ($d[s]=0$). Relax $(s, a) \implies d[a]=5$.
    2. Extract $a$ ($d[a]=5$). Relax $(a, b) \implies d[b]=5 + (-2) = 3$.
    3. Extract $b$ ($d[b]=3$).
    Final distances: $d[s]=0, d[a]=5, d[b]=3$.
    True shortest paths: $\delta(s, s)=0, \delta(s, a)=5, \delta(s, b)=3$.
  - The output of Dijkstra's algorithm is **100% mathematically correct**, despite containing a negative edge $(a, b) = -2$!
  - **Condition for Success:** Dijkstra correctly computes shortest paths on graphs with negative edges whenever the negative edges **do not bypass any already-finalized vertex** (i.e., topological order is fortuitously respected by the priority queue extractions).

---

## ClassPractice Q5a: Bellman-Ford vs. Dijkstra Shortest-Path Tree Divergence

**Problem Statement (ClassPractice Quiz-2, Q5a):**
> *Given a graph $G = (V, E)$ with positive edge weights, the Bellman-Ford algorithm and Dijkstra's algorithm can produce different shortest-path trees despite always producing the same shortest-path weights.*
> **Answer: TRUE.**

### Formal Mathematical Proof & Minimal Counterexample:

Let $G = (V, E)$ have source $s$ and positive edge weights.
Suppose there are **multiple distinct shortest paths** between $s$ and some vertex $v$.
Both Dijkstra and Bellman-Ford will correctly compute the unique shortest-path weight $\delta(s, v)$.
However, the **predecessor pointers $\pi[v]$** depend strictly on the **order in which relaxation ties are broken**:
- Dijkstra breaks ties based on priority queue extraction order and the order edges are listed in the adjacency list of the extracted node.
- Bellman-Ford breaks ties based on the static permutation of the global edge list $E$.

```
                 (s) ────── 1 ──────► (a)
                  │                    │
                 2│                    │1
                  ▼                    ▼
                 (b) ◄─────────────────┘
```
- Edges: $(s, a) = 1, \; (a, b) = 1, \; (s, b) = 2$.
- Both paths from $s$ to $b$ have total length 2:
  - Path 1: $s \to b$ (length 2)
  - Path 2: $s \to a \to b$ (length $1 + 1 = 2$)
- $\delta(s, b) = 2$ is uniquely determined.
- **Dijkstra Execution:**
  - Iteration 1: Extract $s$. Relaxes $(s, a) \implies d[a]=1, \pi[a]=s$. Relaxes $(s, b) \implies d[b]=2, \pi[b]=s$.
  - Iteration 2: Extract $a$ ($d[a]=1$). Inspects $(a, b)$: candidate distance $1 + 1 = 2$.
    - With strict inequality (`if d[v] > d[u] + w`), $2 > 2$ is FALSE $\implies \pi[b] = s$.
    - Tree edges: $E(T_{\text{Dijkstra}}) = \{(s, a), (s, b)\}$.
- **Bellman-Ford Execution:**
  - Suppose the edge list is ordered: $\langle (s, b), (s, a), (a, b) \rangle$.
  - If relaxation uses non-strict inequality (`if d[v] >= d[u] + w`), or if the edge list is ordered $\langle (s, a), (a, b), (s, b) \rangle$:
    Relaxation of $(a, b)$ sets $\pi[b] = a$, and subsequent inspection of $(s, b)$ sees $2 \ge 2$, leaving $\pi[b] = a$.
    - Tree edges: $E(T_{\text{BF}}) = \{(s, a), (a, b)\}$.
- **Conclusion:** $E(T_{\text{Dijkstra}}) \neq E(T_{\text{BF}})$. The shortest-path trees are distinct!

---

## Why Naive Reweighting ($w + C$) Destroys Shortest Paths

A common student trap is proposing: *"If a graph has negative edges, find the minimum edge weight $-M < 0$, add $C = M$ to every edge to make all weights non-negative, and run Dijkstra."*

### Mathematical Proof of Failure:
Let $p_1$ and $p_2$ be two paths between $s$ and $t$:
- Path $p_1$ has $k_1$ edges and total weight $W_1 = \sum_{e \in p_1} \omega(e)$.
- Path $p_2$ has $k_2$ edges and total weight $W_2 = \sum_{e \in p_2} \omega(e)$.

Under the reweighted graph $\omega'(e) = \omega(e) + C$:
$$\omega'(p_1) = \sum_{e \in p_1} (\omega(e) + C) = W_1 + k_1 \cdot C$$
$$\omega'(p_2) = \sum_{e \in p_2} (\omega(e) + C) = W_2 + k_2 \cdot C$$

If $k_1 \neq k_2$, the reweighting adds **unequal penalties** to $p_1$ and $p_2$:
$$\omega'(p_1) - \omega'(p_2) = (W_1 - W_2) + (k_1 - k_2) \cdot C$$

### Minimal Counterexample:
```
           (s) ─────── 1 ───────► (t)   (Path 1: 1 edge, weight 1)
            │                      ▲
          -2│                      │-2
            ▼                      │
           (a) ─────── 1 ────────► (b)  (Path 2: 3 edges, weight -3)
```
- Original weights:
  - $\omega(p_1) = 1$ (1 edge)
  - $\omega(p_2) = (-2) + 1 + (-2) = -3$ (3 edges)
  - Shortest path is $p_2$ ($-3 < 1$).
- Reweight by adding $C = 2$ to make all edges non-negative:
  - $\omega'(s, t) = 1 + 2 = 3$
  - $\omega'(s, a) = -2 + 2 = 0$
  - $\omega'(a, b) = 1 + 2 = 3$
  - $\omega'(b, t) = -2 + 2 = 0$
- New path weights:
  - $\omega'(p_1) = 3$
  - $\omega'(p_2) = 0 + 3 + 0 = 3$ (or if $C=3$, $\omega'(p_1) = 4$ while $\omega'(p_2) = 6 \implies p_1$ becomes shorter!)
- **Conclusion:** Adding a constant penalizes paths with more edges disproportionately, fundamentally mutating the shortest-path geometry!
*(To reweight correctly, one must use Johnson's node-potential formulation: $\omega'(u, v) = \omega(u, v) + h(u) - h(v)$, which telescopes along any path).*

---

# 7. Architectural Insights: Dijkstra vs. BFS vs. Prim's MST

```
┌─────────────────────────────────┬─────────────────────────────────┬─────────────────────────────────┐
│ Breadth-First Search (BFS)      │ Dijkstra's Algorithm            │ Prim's Minimum Spanning Tree    │
├─────────────────────────────────┼─────────────────────────────────┼─────────────────────────────────┤
│ Unweighted graphs ($\omega = 1$) │ Non-negative weights ($\omega \ge 0$)│ Undirected weighted graphs      │
│ FIFO Queue                      │ Min-Priority Queue              │ Min-Priority Queue              │
│ Key = $d[v] = d[u] + 1$         │ Key = $d[v] = d[u] + \omega(u, v)$ │ Key = $\text{key}[v] = \omega(u, v)$   │
│ $O(V + E)$ time                 │ $O(V \log V + E)$ time          │ $O(V \log V + E)$ time          │
│ Cumulative distance from source │ Cumulative distance from source │ Local distance to growing tree  │
└─────────────────────────────────┴─────────────────────────────────┴─────────────────────────────────┘
```

---

# 8. Exhaustive Conceptual True / False Question Bank (With Deep Justifications)

---

### Q1: Dijkstra's algorithm always finds the correct shortest paths if the graph contains negative edges but no negative cycles.
> **Answer: FALSE.**
> **Justification:** The greedy choice property assumes adding an edge cannot decrease path length. A negative edge can make an already-finalized vertex reachable through a cheaper path, which standard Dijkstra fails to discover.

---

### Q2: For dense graphs where $|E| = \Theta(V^2)$, Dijkstra's algorithm implemented with an unsorted array runs in $\Theta(V^2)$ time, which is asymptotically optimal.
> **Answer: TRUE.**
> **Justification:** Reading all $V^2$ edges takes $\Omega(V^2)$ time. Array-based Dijkstra executes $V$ linear scans of $O(V)$ and $E$ updates of $O(1)$, totaling $\Theta(V^2)$.

---

### Q3: Once a vertex is extracted from the priority queue in Dijkstra's algorithm, its distance estimate $d[u]$ will never change again.
> **Answer: TRUE.**
> **Justification:** In standard Dijkstra, extracting a vertex places it into the finalized set $S$. The algorithm never updates the distance of any vertex already in $S$.

---

### Q4: In an undirected graph with non-negative edge weights, Dijkstra's algorithm correctly computes all shortest paths.
> **Answer: TRUE.**
> **Justification:** Dijkstra operates identically on directed and undirected graphs, provided all edge weights are non-negative.

---

### Q5: If all edge weights are distinct positive numbers, the shortest path between any two vertices is unique.
> **Answer: FALSE.**
> **Justification:** A path with two edges $2 + 3 = 5$ has the same total weight as a single edge of weight 5, even though all individual edge weights $\{2, 3, 5\}$ are distinct.

---

### Q6: Adding a constant $C > 0$ to every edge in a graph allows Dijkstra to be run on graphs with negative weights.
> **Answer: FALSE.**
> **Justification:** Adding $C$ to every edge penalizes paths with more edges more heavily than paths with fewer edges, fundamentally altering which path is the shortest.

---

### Q7: Using a Fibonacci heap, Dijkstra's algorithm achieves an asymptotic running time of $O(V \log V + E)$.
> **Answer: TRUE.**
> **Justification:** Fibonacci heaps support `Decrease-Key` in $O(1)$ amortized time and `Extract-Min` in $O(\log V)$ amortized time, yielding $O(V \log V + E)$.

---

### Q8: Dial's algorithm solves SSSP in $O(E)$ time when edge weights are integers in the range $\{1, \dots, W\}$ with $W = O(1)$.
> **Answer: TRUE.**
> **Justification:** Using a bucket queue of size $W + 1$, Dial's algorithm runs in $O(E + WV)$ time, which simplifies to $O(E)$ when $W$ is a constant.

---

### Q9: Dijkstra's algorithm can be used to find the shortest path from all vertices to a single target vertex $t$ in a directed graph.
> **Answer: TRUE.**
> **Justification:** By reversing all directed edges (constructing the transpose graph $G^T$) and running Dijkstra from source $t$, all-to-one shortest paths are computed in a single run.

---

### Q10: If we terminate Dijkstra as soon as the target vertex $t$ is extracted from the priority queue, the computed distance $d[t]$ is guaranteed to be optimal.
> **Answer: TRUE.**
> **Justification:** By the greedy invariant, any vertex extracted from $Q$ has achieved its final shortest-path distance.

---

### Q11: The shortest-path tree computed by Dijkstra is always identical to the Minimum Spanning Tree (MST) computed by Prim's algorithm.
> **Answer: FALSE.**
> **Justification:** A shortest-path tree minimizes path distances from a single source, whereas an MST minimizes the sum of all edge weights in the tree. They are often completely different trees.

---

### Q12: In Dijkstra's algorithm, the total number of `Decrease-Key` operations performed is at most $|E|$.
> **Answer: TRUE.**
> **Justification:** Each directed edge $(u, v)$ is relaxed exactly once when $u$ is extracted from $Q$. A `Decrease-Key` operation occurs only when relaxation successfully lowers $d[v]$.

---

### Q13: If a graph contains a negative-weight cycle, running Dijkstra's algorithm will enter an infinite loop.
> **Answer: FALSE.**
> **Justification:** Standard Dijkstra extracts each vertex from $Q$ exactly once and terminates after $|V|$ iterations. It will not loop infinitely; it simply outputs incorrect distances.

---

### Q14: Prim's algorithm and Dijkstra's algorithm have the exact same time complexity when using the same priority queue data structure.
> **Answer: TRUE.**
> **Justification:** Both algorithms perform $|V|$ `Extract-Min` operations and at most $|E|$ `Decrease-Key` operations.

---

### Q15: Dijkstra's algorithm can be modified to find the path that maximizes the bottleneck edge capacity (widest path) by modifying the relaxation rule.
> **Answer: TRUE.**
> **Justification:** By setting $d[s] = \infty, d[v] = -\infty$, and relaxing with $d[v] = \max(d[v], \min(d[u], \omega(u, v)))$ using a Max-Priority Queue, the widest path is found in $O(E \log V)$ time.

---

### Q16: Bidirectional Dijkstra reduces the search space by expanding from both source and target simultaneously.
> **Answer: TRUE.**
> **Justification:** Meeting in the middle typically halves the radius of the search ball, reducing the number of processed nodes significantly.

---

### Q17: In a graph with non-negative edge weights, the vertex extracted second from the priority queue is always the neighbor of $s$ with the smallest edge weight.
> **Answer: TRUE.**
> **Justification:** In iteration 1, $s$ relaxes all its neighbors with $d[v] = \omega(s, v)$. Because all weights are $\ge 0$, the minimum distance in $Q$ must be $\min_{(s, v)} \omega(s, v)$.

---

### Q18: A d-ary heap with $d = E/V$ provides an asymptotic running time of $O(E \log_{E/V} V)$ for Dijkstra's algorithm.
> **Answer: TRUE.**
> **Justification:** A $d$-ary heap balances the cost of $V$ `Extract-Min` operations ($O(d \log_d V)$) against $E$ `Decrease-Key` operations ($O(\log_d V)$), achieving optimal performance on dense graphs without Fibonacci heap complexity.

---

### Q19: Finding the shortest path with at most $K$ edges on a graph with non-negative weights can be solved using Dijkstra on a layered graph.
> **Answer: TRUE.**
> **Justification:** Constructing $K + 1$ layers enforces that every edge traversal increments the layer index, restricting paths to at most $K$ edges.

---

### Q20: If all edge weights in a graph are identical positive integers, Dijkstra's algorithm behaves identically to Breadth-First Search (BFS).
> **Answer: TRUE.**
> **Justification:** When all edge weights equal $C > 0$, distances advance in uniform multiples of $C$, matching BFS level-by-level queue expansion.

---

### Q21: In Dijkstra's algorithm with a binary heap, using a separate lookup table allows `Decrease-Key` to locate elements in $O(1)$ time before sifting up.
> **Answer: TRUE.**
> **Justification:** Maintaining an array `pos[v]` storing the current index of each vertex in the heap array allows instant $O(1)$ lookup.

---

### Q22: Multiplying all edge weights in a graph by a positive constant $C > 0$ preserves all shortest paths.
> **Answer: TRUE.**
> **Justification:** Scaling all edges by $C > 0$ scales every path weight by exactly $C$, preserving the relative inequalities between all paths.

---

### Q23: Dijkstra's algorithm requires storing predecessor pointers $\pi$ to compute shortest path lengths.
> **Answer: FALSE.**
> **Justification:** Predecessor pointers are needed only to reconstruct the sequence of vertices on the shortest path; distance lengths $d[v]$ are computed independently.

---

### Q24: If graph $G$ has non-negative edge weights and is a DAG, Dijkstra's algorithm is asymptotically faster than the DAG-Shortest-Paths algorithm.
> **Answer: FALSE.**
> **Justification:** DAG-Shortest-Paths runs in $\Theta(V + E)$ without any priority queue overhead, whereas Dijkstra requires at least $O(V \log V + E)$.

---

### Q25: The shortest path between $s$ and $t$ computed by Dijkstra can change if an edge not on the shortest path has its weight increased.
> **Answer: FALSE.**
> **Justification:** Increasing the weight of an edge that is not part of the shortest path cannot decrease the weight of any alternative path, leaving the shortest path intact.

