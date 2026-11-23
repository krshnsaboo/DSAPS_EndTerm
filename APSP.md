# All-Pairs Shortest Paths (APSP): The Definitive Master Guide

---

## Table of Contents
1. [Introduction & Problem Formulation](#1-introduction--problem-formulation)
   - [Formal Problem Definition](#formal-problem-definition)
   - [Graph Representations: Adjacency Matrix vs. Adjacency List](#graph-representations-adjacency-matrix-vs-adjacency-list)
   - [The Role of Negative Edge Weights & Negative-Weight Cycles](#the-role-of-negative-edge-weights--negative-weight-cycles)
   - [Path Representations: Predecessor Matrix ($\Pi$) vs. Intermediate Vertex Matrix ($P$)](#path-representations-predecessor-matrix-\pi-vs-intermediate-vertex-matrix-p)
2. [Baseline Approaches: Running Single-Source Shortest Paths (SSSP) $V$ Times](#2-baseline-approaches-running-single-source-shortest-paths-sssp-v-times)
   - [Unweighted Graphs: Breadth-First Search (BFS) $\times V$](#unweighted-graphs-breadth-first-search-bfs-\times-v)
   - [Directed Acyclic Graphs (DAGs): Topological Sort + SSSP $\times V$](#directed-acyclic-graphs-dags-topological-sort--sssp-\times-v)
   - [Non-Negative Edge Weights: Dijkstra's Algorithm $\times V$](#non-negative-edge-weights-dijkstras-algorithm-\times-v)
   - [General Edge Weights: Bellman-Ford Algorithm $\times V$](#general-edge-weights-bellman-ford-algorithm-\times-v)
   - [Why Naive Reweighting Fails](#why-naive-reweighting-fails)
   - [Johnson's Algorithm for Sparse Graphs (Reweighting Technique)](#johnsons-algorithm-for-sparse-graphs-reweighting-technique)
3. [Dynamic Programming Approach 1: Matrix Multiplication Formulation](#3-dynamic-programming-approach-1-matrix-multiplication-formulation)
   - [Subproblem Formulation by Edge Bounding](#subproblem-formulation-by-edge-bounding)
   - [Recursive Definition](#recursive-definition)
   - [The $(\min, +)$ Tropical Semiring Analogy](#the-\min--tropical-semiring-analogy)
   - [Slow-APSP Algorithm: Bottom-Up Dynamic Programming ($O(V^4)$)](#slow-apsp-algorithm-bottom-up-dynamic-programming-ov4)
   - [Faster-APSP via Repeated Squaring ($O(V^3 \log V)$)](#faster-apsp-via-repeated-squaring-ov3-\log-v)
   - [Comprehensive Step-by-Step Example (5-Vertex CLRS Graph)](#comprehensive-step-by-step-example-5-vertex-clrs-graph)
4. [Dynamic Programming Approach 2: The Floyd-Warshall Algorithm](#4-dynamic-programming-approach-2-the-floyd-warshall-algorithm)
   - [The Structure of Shortest Paths & Vertex Subsets](#the-structure-of-shortest-paths--vertex-subsets)
   - [Floyd-Warshall Recursive Definition](#floyd-warshall-recursive-definition)
   - [Standard Algorithm Formulation ($n+1$ Matrices)](#standard-algorithm-formulation-n1-matrices)
   - [Space Optimization: $O(V^3) \to O(V^2)$ (Ping-Pong Buffer) $\to O(V^2)$ In-Place](#space-optimization-ov3-\to-ov2-ping-pong-buffer-\to-ov2-in-place)
   - [Rigorous Mathematical Proof of In-Place Correctness](#rigorous-mathematical-proof-of-in-place-correctness)
   - [Negative-Weight Cycle Detection](#negative-weight-cycle-detection)
   - [Identifying Vertices with $-\infty$ Distances](#identifying-vertices-with--\infty-distances)
   - [Shortest Path Reconstruction](#shortest-path-reconstruction)
     - [Method 1: Intermediate Pointer Matrix $P$ (Recursive Method)](#method-1-intermediate-pointer-matrix-p-recursive-method)
     - [Method 2: Predecessor Matrix $\Pi$ (Iterative / Standard Method)](#method-2-predecessor-matrix-\pi-iterative--standard-method)
   - [Comprehensive Trace 1: 3-Vertex Graph with Negative Edge](#comprehensive-trace-1-3-vertex-graph-with-negative-edge)
   - [Comprehensive Trace 2: 6-Vertex Graph](#comprehensive-trace-2-6-vertex-graph)
5. [Transitive Closure & Warshall's Algorithm](#5-transitive-closure--warshalls-algorithm)
   - [Reachability Problem Definition](#reachability-problem-definition)
   - [Boolean Formulation of Warshall's Algorithm](#boolean-formulation-of-warshalls-algorithm)
   - [Bitwise Parallelism Optimization ($O(V^3 / w)$)](#bitwise-parallelism-optimization-ov3--w)
6. [Master Comparative Summary Matrix](#6-master-comparative-summary-matrix)
7. [Algorithmic Design Variations & Advanced Complex Problems](#7-algorithmic-design-variations--advanced-complex-problems)
   - [Problem 1: Dynamic Graph Update — Edge Weight Decrease ($O(V^2)$)](#problem-1-dynamic-graph-update--edge-weight-decrease-ov2)
   - [Problem 2: Dynamic Graph Update — Adding a New Vertex ($O(V^2)$)](#problem-2-dynamic-graph-update--adding-a-new-vertex-ov2)
   - [Problem 3: Finding the Minimum Weight Cycle (Shortest Cycle)](#problem-3-finding-the-minimum-weight-cycle-shortest-cycle)
   - [Problem 4: Finding the Girth of an Undirected Graph](#problem-4-finding-the-girth-of-an-undirected-graph)
   - [Problem 5: Minimax Paths (Widest Path / Maximum Bottleneck Path)](#problem-5-minimax-paths-widest-path--maximum-bottleneck-path)
   - [Problem 6: Maximum Reliability Paths (Multiplicative Semiring)](#problem-6-maximum-reliability-paths-multiplicative-semiring)
   - [Problem 7: Graph Diameter, Radius, and Center](#problem-7-graph-diameter-radius-and-center)
   - [Problem 8: Counting the Number of Shortest Paths](#problem-8-counting-the-number-of-shortest-paths)
   - [Problem 9: Currency Arbitrage Detection](#problem-9-currency-arbitrage-detection)
   - [Problem 10: Sub-Cubic APSP Algorithms (Theoretical Frontiers)](#problem-10-sub-cubic-apsp-algorithms-theoretical-frontiers)
8. [Deep Intuitive Insights, Common Traps & Reasoning Questions](#8-deep-intuitive-insights-common-traps--reasoning-questions)
   - [Why MUST the $k$-loop be outermost in Floyd-Warshall?](#why-must-the-k-loop-be-outermost-in-floyd-warshall)
   - [Can intermediate vertices be processed in any arbitrary permutation?](#can-intermediate-vertices-be-processed-in-any-arbitrary-permutation)
   - [Why does an undirected graph with negative weights break Floyd-Warshall?](#why-does-an-undirected-graph-with-negative-weights-break-floyd-warshall)
   - [Why is Floyd-Warshall often faster than Dijkstra in practice on dense graphs?](#why-is-floyd-warshall-often-faster-than-dijkstra-in-practice-on-dense-graphs)
   - [Edge Bounding vs. Vertex Bounding: Structural Comparison](#edge-bounding-vs-vertex-bounding-structural-comparison)
9. [Exhaustive Conceptual True / False Question Bank (With Deep Justifications)](#9-exhaustive-conceptual-true--false-question-bank-with-deep-justifications)

---

# 1. Introduction & Problem Formulation

## Formal Problem Definition
Let $G = (V, E)$ be a directed graph, where:
- $V = \{v_1, v_2, \dots, v_n\}$ is the set of vertices ($|V| = n$).
- $E \subseteq V \times V$ is the set of directed edges ($|E| = m$).
- $\omega: E \to \mathbb{R}$ is a weight function mapping edges to real-valued weights.

A **path** $p$ from vertex $v_i$ to vertex $v_j$ is a sequence of vertices $\langle u_0, u_1, \dots, u_l \rangle$ such that $u_0 = v_i$, $u_l = v_j$, and $(u_{t-1}, u_t) \in E$ for all $1 \le t \le l$. The weight of path $p$ is the sum of the weights of its constituent edges:
$$\omega(p) = \sum_{t=1}^l \omega(u_{t-1}, u_t)$$

The **shortest-path distance** $\delta(u, v)$ from $u$ to $v$ is defined as:
$$\delta(u, v) = \begin{cases} 
\min \{\omega(p) : u \xrightarrow{p} v\} & \text{if there is a path from } u \text{ to } v \\
\infty & \text{if there is no path from } u \text{ to } v \\
-\infty & \text{if there exists a path from } u \text{ to } v \text{ that traverses a negative-weight cycle}
\end{cases}$$

The **All-Pairs Shortest Paths (APSP)** problem requires computing the shortest-path distance between **every pair of vertices** $(v_i, v_j) \in V \times V$.
The output is represented as an $n \times n$ matrix $D = (d_{ij})$ where:
$$d_{ij} = \delta(v_i, v_j) \quad \forall 1 \le i, j \le n$$

---

## Graph Representations: Adjacency Matrix vs. Adjacency List

### 1. Adjacency Matrix Representation
For dense graphs or matrix-based APSP algorithms, graph $G$ is represented as an $n \times n$ weight matrix $W = (\omega_{ij})$:
$$\omega_{ij} = \begin{cases} 
0 & \text{if } i = j \\
\omega(v_i, v_j) & \text{if } i \neq j \text{ and } (v_i, v_j) \in E \\
\infty & \text{if } i \neq j \text{ and } (v_i, v_j) \notin E 
\end{cases}$$

> [!NOTE]
> **Why is $\omega_{ii} = 0$?**
> Assuming the graph contains no negative-weight cycles, the shortest path from any vertex $v_i$ to itself is the empty path containing 0 edges, whose weight is 0. If a self-loop $(v_i, v_i)$ has weight $w < 0$, it is a negative-weight cycle of length 1, making $\delta(v_i, v_i) = -\infty$.

### 2. Adjacency List Representation
For sparse graphs where $|E| \ll |V|^2$, an array of $|V|$ linked lists or dynamic arrays stores the outgoing edges for each vertex. This representation is ideal when running Single-Source Shortest Path (SSSP) algorithms (like Dijkstra or Bellman-Ford) from each vertex.

---

## The Role of Negative Edge Weights & Negative-Weight Cycles

1. **Negative Edge Weights with No Negative Cycles:**
   - Shortest paths are well-defined.
   - A shortest path between any pair of vertices must be a **simple path** (no repeated vertices).
   - Any simple path in a graph with $n$ vertices contains at most $n - 1$ edges.
2. **Negative-Weight Cycles:**
   - A cycle $C = \langle u_0, u_1, \dots, u_k, u_0 \rangle$ has negative weight if $\sum_{t=1}^k \omega(u_{t-1}, u_t) + \omega(u_k, u_0) < 0$.
   - If a path from $u$ to $v$ can reach $C$ and then proceed to $v$, the cycle can be traversed an arbitrary number of times to decrease the total path weight without bound:
     $$\delta(u, v) = -\infty$$
   - Under such circumstances, shortest paths are undefined for any pair $(u, v)$ whose paths intersect a negative-weight cycle.
   - Algorithms must be capable of **detecting negative-weight cycles** and reporting their existence.

---

## Path Representations: Predecessor Matrix ($\Pi$) vs. Intermediate Vertex Matrix ($P$)

To reconstruct the actual paths rather than merely computing their lengths, auxiliary matrices are maintained during algorithm execution:

### 1. Predecessor Matrix $\Pi = (\pi_{ij})$
$\pi_{ij}$ denotes the predecessor of vertex $v_j$ on a shortest path from $v_i$ to $v_j$:
- If $i = j$ or there is no path from $v_i$ to $v_j$, $\pi_{ij} = \text{NIL}$.
- If $i \neq j$ and a shortest path exists, $\pi_{ij}$ is the immediate predecessor vertex to $v_j$.
- **Path Reconstruction:** Traces backwards from target $v_j$ to source $v_i$:
  $$v_j \leftarrow \pi_{ij} \leftarrow \pi_{i, \pi_{ij}} \dots \leftarrow v_i$$

### 2. Intermediate Vertex Matrix $P = (p_{ij})$ (Lecture Slides Convention)
$p_{ij}$ stores the **highest-indexed intermediate vertex** on a shortest path from $v_i$ to $v_j$:
- Initially, $p_{ij} = 0$ for all $i, j$ (indicating no intermediate vertices; the edge is direct or absent).
- When a shorter path through vertex $v_k$ is discovered, $p_{ij} \leftarrow k$.
- **Path Reconstruction:** Uses a divide-and-conquer recursive procedure `path(q, r)`:
  1. If $P[q, r] \neq 0$:
     - Recursively print intermediate vertices on the subpath from $q$ to $P[q, r]$.
     - Print vertex $P[q, r]$.
     - Recursively print intermediate vertices on the subpath from $P[q, r]$ to $r$.

```text
PRINT-ALL-PAIRS-SHORTEST-PATH(P, D, q, r):
    if D[q, r] == ∞:
        print("No path from " + q + " to " + r)
        return
    print(q)
    PATH(P, q, r)
    if q != r:
        print(r)

PATH(P, q, r):
    k = P[q, r]
    if k != 0:
        PATH(P, q, k)
        print(k)
        PATH(P, k, r)
```

---

# 2. Baseline Approaches: Running Single-Source Shortest Paths (SSSP) $V$ Times

A natural strategy to solve APSP is to invoke a Single-Source Shortest Path (SSSP) algorithm $V$ times, treating each vertex $v \in V$ as the source.

```
       ┌─────────────────────────────────────────────────────┐
       │                   APSP via SSSP                     │
       └──────────────────────────┬──────────────────────────┘
                                  │
         ┌────────────────────────┼────────────────────────┐
         ▼                        ▼                        ▼
  Unweighted Graph       Non-Negative Weights        General Weights
  BFS × V                Dijkstra × V                Bellman-Ford × V
  O(V(V + E))            O(V(V log V + E))           O(V² E)
                         (Fibonacci Heap)            (Sparse: O(V³), Dense: O(V⁴))
                                                           │
                                                           ▼
                                                    Can we do better?
                                                    Johnson's Algorithm
                                                    O(V² log V + VE)
```

---

## Unweighted Graphs: Breadth-First Search (BFS) $\times V$
When all edge weights are uniform (e.g., $\omega(e) = 1$):
- SSSP is solved via BFS in $O(V + E)$ time.
- Running BFS from every vertex $v \in V$ yields all-pairs shortest paths.
- **Time Complexity:**
  $$T(V, E) = V \times O(V + E) = O(V^2 + VE)$$
  - Sparse graphs ($E = O(V)$): $O(V^2)$
  - Dense graphs ($E = O(V^2)$): $O(V^3)$
- **Space Complexity:** $O(V^2)$ to store the distance matrix.

---

## Directed Acyclic Graphs (DAGs): Topological Sort + SSSP $\times V$
When the graph has no directed cycles, arbitrary (even negative) edge weights are permissible without risk of negative cycles:
- Compute a topological sort in $O(V + E)$ time.
- For each vertex, relax edges in topological order in $O(V + E)$ time.
- **Time Complexity:**
  $$T(V, E) = O(V + E) + V \times O(V + E) = O(V^2 + VE)$$

---

## Non-Negative Edge Weights: Dijkstra's Algorithm $\times V$
When $\omega(e) \ge 0$ for all $e \in E$, Dijkstra's algorithm solves SSSP. Running it $V$ times yields APSP. The complexity depends on the Priority Queue implementation:

| Priority Queue Implementation | SSSP Time | APSP Time ($V \times \text{SSSP}$) | Sparse Graph ($E = O(V)$) | Dense Graph ($E = O(V^2)$) |
| :--- | :--- | :--- | :--- | :--- |
| **Array (Unsorted)** | $O(V^2)$ | $O(V^3)$ | $O(V^3)$ | $O(V^3)$ |
| **Binary Min-Heap** | $O((V + E)\log V)$ | $O(V(V + E)\log V)$ | $O(V^2 \log V)$ | $O(V^3 \log V)$ |
| **Fibonacci Heap** | $O(V \log V + E)$ | $O(V^2 \log V + VE)$ | $O(V^2 \log V)$ | $O(V^3)$ |

> [!TIP]
> On sparse graphs ($E = O(V)$), Dijkstra with a binary or Fibonacci heap runs in $O(V^2 \log V)$, which significantly outperforms $O(V^3)$ algorithms. However, on dense graphs ($E = \Theta(V^2)$), Dijkstra with a binary heap runs in $O(V^3 \log V)$, which is asymptotically slower than Floyd-Warshall's $\Theta(V^3)$.

---

## General Edge Weights: Bellman-Ford Algorithm $\times V$
When edge weights can be negative:
- Bellman-Ford computes SSSP in $O(VE)$ time and detects negative-weight cycles reachable from the source.
- Running Bellman-Ford from every vertex $v \in V$:
  $$T(V, E) = V \times O(VE) = O(V^2 E)$$
  - Sparse graphs ($E = O(V)$): $O(V^3)$
  - Dense graphs ($E = O(V^2)$): $O(V^4)$
- Running Bellman-Ford $V$ times on dense graphs is extremely inefficient ($\Theta(V^4)$).

---

## Why Naive Reweighting Fails

A tempting heuristic to eliminate negative weights is to find the minimum edge weight:
$$w_{\min} = \min_{(u, v) \in E} \omega(u, v)$$
If $w_{\min} < 0$, add a constant $C = |w_{\min}|$ to every edge in the graph:
$$\widehat{\omega}(u, v) = \omega(u, v) + C \ge 0$$
Then run Dijkstra $V$ times.

> [!CAUTION]
> **This approach is fundamentally flawed and produces incorrect shortest paths!**

### Mathematical Proof of Failure
Let $p_1$ and $p_2$ be two distinct paths between vertices $u$ and $v$:
- Path $p_1$ consists of $k_1$ edges with original weight $\omega(p_1)$.
- Path $p_2$ consists of $k_2$ edges with original weight $\omega(p_2)$.

Under the modified weight function $\widehat{\omega}$:
$$\widehat{\omega}(p_1) = \omega(p_1) + k_1 \cdot C$$
$$\widehat{\omega}(p_2) = \omega(p_2) + k_2 \cdot C$$

If $k_1 \neq k_2$, the modified weights are penalized unequally based on the **number of edges** in the path.

### Concrete Counterexample
Consider two paths from $A$ to $B$:
- **Path 1:** $A \to B$ (1 edge), weight $\omega = 2$.
- **Path 2:** $A \to C \to D \to B$ (3 edges), weights $-1, -1, 1$. Total weight $\omega = -1 + (-1) + 1 = -1$.

Clearly, Path 2 is shorter: $\omega(p_2) = -1 < \omega(p_1) = 2$.

Now apply naive reweighting with $C = 1$ ($w_{\min} = -1$):
- $\widehat{\omega}(A \to B) = 2 + 1 = 3$.
- $\widehat{\omega}(A \to C \to D \to B) = (-1 + 1) + (-1 + 1) + (1 + 1) = 0 + 0 + 2 = 2$.
Here it worked. But let $C = 5$:
- $\widehat{\omega}(p_1) = 2 + 5 = 7$.
- $\widehat{\omega}(p_2) = -1 + 3 \times 5 = 14$.
Now Path 1 has weight 7 while Path 2 has weight 14! The algorithm falsely selects Path 1.

**Conclusion:** Naive reweighting does not preserve the relative ordering of path weights because it unfairly penalizes paths with more edges.

---

## Johnson's Algorithm for Sparse Graphs (Reweighting Technique)

Johnson's algorithm uses a mathematically sound reweighting scheme based on **vertex potentials**, preserving shortest paths while ensuring all edge weights are non-negative.

### 1. Reweighting via Vertex Potentials
Assign each vertex $u \in V$ a real value $h(u) \in \mathbb{R}$. Define the reweighted edge weight $\widehat{\omega}(u, v)$ as:
$$\widehat{\omega}(u, v) = \omega(u, v) + h(u) - h(v)$$

### 2. Proof That Shortest Paths Are Preserved
Let $p = \langle v_0, v_1, \dots, v_k \rangle$ be any path from $v_0$ to $v_k$. The weight of $p$ under $\widehat{\omega}$ is:
$$\widehat{\omega}(p) = \sum_{i=1}^k \widehat{\omega}(v_{i-1}, v_i) = \sum_{i=1}^k \left[ \omega(v_{i-1}, v_i) + h(v_{i-1}) - h(v_i) \right]$$

This is a **telescoping sum**:
$$\widehat{\omega}(p) = \sum_{i=1}^k \omega(v_{i-1}, v_i) + \left[ (h(v_0) - h(v_1)) + (h(v_1) - h(v_2)) + \dots + (h(v_{k-1}) - h(v_k)) \right]$$
$$\widehat{\omega}(p) = \omega(p) + h(v_0) - h(v_k)$$

Notice that $h(v_0) - h(v_k)$ depends **only on the start and end vertices**, not on the specific path taken or the number of edges!
Therefore, for any two paths $p_1, p_2$ between $v_0$ and $v_k$:
$$\widehat{\omega}(p_1) \le \widehat{\omega}(p_2) \iff \omega(p_1) \le \omega(p_2)$$
Shortest paths under $\omega$ remain shortest paths under $\widehat{\omega}$.

Moreover, any cycle $c = \langle v_0, v_1, \dots, v_k, v_0 \rangle$ starts and ends at $v_0$:
$$\widehat{\omega}(c) = \omega(c) + h(v_0) - h(v_0) = \omega(c)$$
Cycle weights are completely unchanged! Thus, $G$ contains a negative-weight cycle under $\widehat{\omega}$ if and only if it contains one under $\omega$.

### 3. Finding Potentials $h(v)$ Such That $\widehat{\omega}(u, v) \ge 0$
We require:
$$\widehat{\omega}(u, v) = \omega(u, v) + h(u) - h(v) \ge 0 \implies h(v) - h(u) \le \omega(u, v)$$
This inequality matches the standard **triangle inequality** for shortest paths: $\delta(s, v) \le \delta(s, u) + \omega(u, v)$.

**Construction:**
1. Construct an augmented graph $G' = (V', E')$ where $V' = V \cup \{s\}$, with $s \notin V$.
2. Add directed edges $(s, v)$ of weight 0 for every $v \in V$:
   $$E' = E \cup \{(s, v) : v \in V\}, \quad \omega(s, v) = 0$$
3. Run Bellman-Ford on $G'$ with source $s$.
   - If Bellman-Ford detects a negative-weight cycle, terminate: $G$ contains a negative cycle.
   - Otherwise, set:
     $$h(v) = \delta(s, v) \quad \forall v \in V'$$
4. By the triangle inequality on shortest paths from $s$:
   $$\delta(s, v) \le \delta(s, u) + \omega(u, v) \implies h(v) \le h(u) + \omega(u, v) \implies \omega(u, v) + h(u) - h(v) \ge 0$$
   Therefore, $\widehat{\omega}(u, v) \ge 0$ for all $(u, v) \in E$.

```
               [ s ] (New Source)
              /  |  \   (Weight = 0)
             0   0   0
            /    |    \
           ▼     ▼     ▼
         [ u ]───────►[ v ]
             \  w(u,v)
              \
```

### 4. Johnson's Algorithm Steps
1. Construct $G'$.
2. Run Bellman-Ford from $s$ to compute $h(v)$ for all $v \in V$. If a negative cycle is detected, abort.
3. Compute $\widehat{\omega}(u, v) = \omega(u, v) + h(u) - h(v)$ for all $(u, v) \in E$.
4. For each vertex $u \in V$:
   - Run Dijkstra's algorithm on $G$ with edge weights $\widehat{\omega}$ to compute $\widehat{\delta}(u, v)$ for all $v \in V$.
5. Recover the original shortest-path distances:
   $$\delta(u, v) = \widehat{\delta}(u, v) + h(v) - h(u)$$

### 5. Complexity Analysis of Johnson's Algorithm
- Constructing $G'$: $O(V + E)$
- Bellman-Ford on $G'$ ($|V'| = V+1$, $|E'| = E+V$): $O(V E)$
- Reweighting edges: $O(E)$
- Running Dijkstra $V$ times:
  - Using Fibonacci heap: $V \times O(V \log V + E) = O(V^2 \log V + VE)$
  - Using Binary heap: $V \times O((V + E) \log V) = O(VE \log V + V^2 \log V)$
- Distance conversion: $O(V^2)$

**Total Running Time (Fibonacci Heap):**
$$T(V, E) = O(VE + V^2 \log V + VE) = O(V^2 \log V + VE)$$

> [!IMPORTANT]
> **Asymptotic Dominance of Johnson's Algorithm on Sparse Graphs:**
> For sparse graphs where $E = O(V)$, Johnson's algorithm runs in:
> $$O(V^2 \log V + V^2) = O(V^2 \log V)$$
> This beats Floyd-Warshall's $\Theta(V^3)$ by a full factor of $V / \log V$.

---

# 3. Dynamic Programming Approach 1: Matrix Multiplication Formulation

## Subproblem Formulation by Edge Bounding

We define dynamic programming subproblems parameterized by the **maximum number of edges** permitted in a path.

Let:
$$d_{ij}^{(m)} = \text{weight of a shortest path from } v_i \text{ to } v_j \text{ using at most } m \text{ edges}$$

### Base Case ($m = 0$):
A path containing at most 0 edges can only go from a vertex to itself:
$$d_{ij}^{(0)} = \begin{cases} 
0 & \text{if } i = j \\ 
\infty & \text{if } i \neq j 
\end{cases}$$
In matrix form: $D^{(0)} = I_{\min}$, where $I_{\min}$ is the identity matrix under the tropical semiring (0 on diagonal, $\infty$ elsewhere).

---

## Recursive Definition

To compute $d_{ij}^{(m)}$ from paths with at most $m - 1$ edges:
A shortest path from $v_i$ to $v_j$ with $\le m$ edges consists of:
1. Either a path from $v_i$ to $v_j$ with $\le m-1$ edges (weight $d_{ij}^{(m-1)}$), or
2. A path from $v_i$ to some intermediate vertex $v_k$ with $\le m-1$ edges, followed by the single edge $(v_k, v_j)$ with weight $\omega_{kj}$.

$$d_{ij}^{(m)} = \min \left( d_{ij}^{(m-1)}, \; \min_{1 \le k \le n} \left\{ d_{ik}^{(m-1)} + \omega_{kj} \right\} \right)$$

Because $\omega_{jj} = 0$, the term $d_{ij}^{(m-1)} + \omega_{jj} = d_{ij}^{(m-1)}$ is naturally included in the minimization when $k = j$. Thus, the recurrence simplifies to:
$$d_{ij}^{(m)} = \min_{1 \le k \le n} \left\{ d_{ik}^{(m-1)} + \omega_{kj} \right\} \quad \forall m \ge 1$$

---

## The $(\min, +)$ Tropical Semiring Analogy

Compare this recurrence to classical matrix multiplication. Let $A, B$ be $n \times n$ matrices. Their product $C = A \cdot B$ is:
$$c_{ij} = \sum_{k=1}^n a_{ik} \cdot b_{kj}$$

Replace standard operations $(+, \cdot)$ with $(\min, +)$:
- Addition $\sum \longrightarrow \min$
- Multiplication $\cdot \longrightarrow +$
- Additive identity $0 \longrightarrow \infty$
- Multiplicative identity $1 \longrightarrow 0$

Under this algebraic structure (known as the **min-plus semiring** or **tropical semiring**):
$$D^{(m)} = D^{(m-1)} \odot W$$
where $\odot$ denotes matrix multiplication over the min-plus semiring:
$$(A \odot B)_{ij} = \min_{1 \le k \le n} \{ a_{ik} + b_{kj} \}$$

---

## Slow-APSP Algorithm: Bottom-Up Dynamic Programming ($O(V^4)$)

Because a simple shortest path in a graph with $n$ vertices contains at most $n - 1$ edges, if the graph contains no negative-weight cycles:
$$\delta(v_i, v_j) = d_{ij}^{(n-1)} = d_{ij}^{(n)} = d_{ij}^{(n+1)} = \dots$$

We can compute the sequence of matrices:
$$D^{(1)} = W, \quad D^{(2)} = D^{(1)} \odot W, \quad D^{(3)} = D^{(2)} \odot W, \dots, D^{(n-1)} = D^{(n-2)} \odot W$$

```text
EXTEND-SHORTEST-PATHS(D, W, n):
    let D' be a new n x n matrix
    for i = 1 to n do
        for j = 1 to n do
            D'[i, j] = ∞
            for k = 1 to n do
                D'[i, j] = min(D'[i, j], D[i, k] + W[k, j])
    return D'

SLOW-APSP(W, n):
    D^(1) = W
    for m = 2 to n - 1 do
        D^(m) = EXTEND-SHORTEST-PATHS(D^(m-1), W, n)
    return D^(n-1)
```

### Complexity:
- Each call to `EXTEND-SHORTEST-PATHS` executes 3 nested loops: $\Theta(n^3)$ operations.
- The loop runs $n - 2$ times.
- **Total Time Complexity:** $\Theta(n^4) = \Theta(V^4)$.
- **Space Complexity:** $\Theta(V^2)$.

---

## Faster-APSP via Repeated Squaring ($O(V^3 \log V)$)

Notice that we do not need every intermediate matrix $D^{(2)}, D^{(3)}, \dots, D^{(n-2)}$. We only need $D^{(n-1)}$ (or any $D^{(m)}$ with $m \ge n-1$).

Since min-plus matrix multiplication is **associative**:
$$A \odot (B \odot C) = (A \odot B) \odot C$$
We can use **repeated squaring**:
$$D^{(1)} = W$$
$$D^{(2)} = W^2 = W \odot W$$
$$D^{(4)} = W^4 = W^2 \odot W^2$$
$$D^{(8)} = W^8 = W^4 \odot W^4$$
$$\dots$$
$$D^{(2^{\lceil \log_2(n-1) \rceil})}$$

Since $m \ge n - 1 \implies D^{(m)} = D^{(n-1)}$ (in the absence of negative cycles), the matrix $D^{(2^s)}$ for $2^s \ge n - 1$ contains the exact shortest-path distances!

```text
FASTER-APSP(W, n):
    D^(1) = W
    m = 1
    while m < n - 1 do
        D^(2m) = EXTEND-SHORTEST-PATHS(D^(m), D^(m), n)
        m = 2 * m
    return D^(m)
```

### Complexity:
- The while loop executes $\lceil \log_2(n - 1) \rceil$ iterations.
- Each iteration performs one min-plus matrix multiplication in $\Theta(n^3)$ time.
- **Total Time Complexity:**
  $$\Theta(n^3 \log n) = \Theta(V^3 \log V)$$
- **Space Complexity:** $\Theta(V^2)$.

---

## Comprehensive Step-by-Step Example (5-Vertex CLRS Graph)

Consider the 5-vertex directed graph from slide 8–12 of the lecture:
Vertices: $\{1, 2, 3, 4, 5\}$.
Edges and weights:
- $(1, 2) = 3$, $(1, 3) = 8$, $(1, 5) = -4$
- $(2, 4) = 1$, $(2, 5) = 7$
- $(3, 2) = 4$
- $(4, 1) = 2$, $(4, 3) = -5$
- $(5, 4) = 6$

```
          (1) ──────── 3 ────────► (2)
         ▲ │ \                      │ \
        /  │  \                     │  \
       2   8   -4                   1   7
      /    │     \                  │    \
     /     ▼      ▼                 ▼     ▼
   (4) ◄───┼────── (5) ◄─────────── (4)   (5)
     \     │            6
      \    ▼           /
       ──►(3) ────────┘
```

### Step 0: $D^{(1)} = W$ (Paths with $\le 1$ Edge)
$$D^{(1)} = \begin{pmatrix}
0 & 3 & 8 & \infty & -4 \\
\infty & 0 & \infty & 1 & 7 \\
\infty & 4 & 0 & \infty & \infty \\
2 & \infty & -5 & 0 & \infty \\
\infty & \infty & \infty & 6 & 0
\end{pmatrix}$$

---

### Step 1: Compute $D^{(2)} = D^{(1)} \odot D^{(1)}$ (Paths with $\le 2$ Edges)
$$d_{ij}^{(2)} = \min_{1 \le k \le 5} \{ d_{ik}^{(1)} + d_{kj}^{(1)} \}$$

Let us trace several critical entries:
- **$d_{14}^{(2)}$:**
  $$\min \begin{cases} 
  d_{11}^{(1)} + d_{14}^{(1)} = 0 + \infty = \infty \\
  d_{12}^{(1)} + d_{24}^{(1)} = 3 + 1 = 4 \\
  d_{13}^{(1)} + d_{34}^{(1)} = 8 + \infty = \infty \\
  d_{14}^{(1)} + d_{44}^{(1)} = \infty + 0 = \infty \\
  d_{15}^{(1)} + d_{54}^{(1)} = -4 + 6 = 2 \quad \implies \mathbf{2} \text{ (Path: } 1 \to 5 \to 4\text{)}
  \end{cases}$$
- **$d_{23}^{(2)}$:**
  $$\min \{ d_{21}^{(1)} + d_{13}^{(1)}, d_{22}^{(1)} + d_{23}^{(1)}, d_{23}^{(1)} + d_{33}^{(1)}, d_{24}^{(1)} + d_{43}^{(1)}, d_{25}^{(1)} + d_{53}^{(1)} \}$$
  $$= \min \{ \infty, \infty, \infty, 1 + (-5), \infty \} = \mathbf{-4} \text{ (Path: } 2 \to 4 \to 3\text{)}$$
- **$d_{42}^{(2)}$:**
  $$\min_{k} \{ d_{4k}^{(1)} + d_{k2}^{(1)} \} = \min \{ 2 + 3, \infty + 0, -5 + 4, 0 + \infty, \infty + \infty \} = \min\{5, -1\} = \mathbf{-1} \text{ (Path: } 4 \to 3 \to 2\text{)}$$

Resulting matrix $D^{(2)}$:
$$D^{(2)} = \begin{pmatrix}
0 & 3 & 8 & 2 & -4 \\
3 & 0 & -4 & 1 & 7 \\
\infty & 4 & 0 & 5 & 11 \\
2 & -1 & -5 & 0 & -2 \\
8 & \infty & 1 & 6 & 0
\end{pmatrix}$$

---

### Step 2: Compute $D^{(3)} = D^{(2)} \odot D^{(1)}$ (Paths with $\le 3$ Edges)
- **$d_{13}^{(3)}$:**
  Path $1 \to 5 \to 4 \to 3$: weight $(-4) + 6 + (-5) = -3$.
  $$\min_k \{ d_{1k}^{(2)} + d_{k3}^{(1)} \} = \min \{ 0+8, 3+\infty, 8+0, 2+(-5), -4+\infty \} = 2 + (-5) = \mathbf{-3}$$
- **$d_{25}^{(3)}$:**
  Path $2 \to 4 \to 1 \to 5$: weight $1 + 2 + (-4) = -1$.
  $$\min \{ 3 + (-4), 0 + 7, -4 + \infty, 1 + \infty, 7 + 0 \} = 3 + (-4) = \mathbf{-1}$$

Resulting matrix $D^{(3)}$:
$$D^{(3)} = \begin{pmatrix}
0 & 3 & -3 & 2 & -4 \\
3 & 0 & -4 & 1 & -1 \\
7 & 4 & 0 & 5 & 11 \\
2 & -1 & -5 & 0 & -2 \\
8 & 5 & 1 & 6 & 0
\end{pmatrix}$$

---

### Step 3: Compute $D^{(4)} = D^{(3)} \odot D^{(1)}$ (Paths with $\le 4$ Edges)
- **$d_{12}^{(4)}$:**
  Path $1 \to 5 \to 4 \to 3 \to 2$: weight $(-4) + 6 + (-5) + 4 = 1$.
  $$\min_k \{ d_{1k}^{(3)} + d_{k2}^{(1)} \} = \min \{ 0+3, 3+0, -3+4, 2+\infty, -4+\infty \} = -3 + 4 = \mathbf{1}$$

Resulting matrix $D^{(4)}$:
$$D^{(4)} = \begin{pmatrix}
0 & 1 & -3 & 2 & -4 \\
3 & 0 & -4 & 1 & -1 \\
7 & 4 & 0 & 5 & 3 \\
2 & -1 & -5 & 0 & -2 \\
8 & 5 & 1 & 6 & 0
\end{pmatrix}$$

Since $|V| = 5$, any simple shortest path has at most $5 - 1 = 4$ edges.
Thus:
$$D^{(4)} = D^{(5)} = D^{(6)} = \dots = D$$
$D^{(4)}$ contains the **exact all-pairs shortest path distances**.

---

# 4. Dynamic Programming Approach 2: The Floyd-Warshall Algorithm

The Floyd-Warshall algorithm takes a fundamentally different dynamic programming approach. Instead of bounding the number of *edges* in a path, it bounds the set of **intermediate vertices** through which a path may pass.

---

## The Structure of Shortest Paths & Vertex Subsets

### Definition of an Intermediate Vertex
For a path $p = \langle v_1, v_2, \dots, v_m \rangle$:
- The start vertex $v_1$ and end vertex $v_m$ are **endpoints**.
- Any vertex $v \in \{v_2, v_3, \dots, v_{m-1}\}$ is an **intermediate vertex**.
- A direct edge $\langle v_1, v_2 \rangle$ has **zero intermediate vertices**.

Let the vertices of $G$ be numbered $V = \{1, 2, \dots, n\}$.
For any $k \in \{0, 1, \dots, n\}$, define the restricted subset of vertices:
$$V^{(k)} = \{1, 2, \dots, k\}$$
When $k = 0$, $V^{(0)} = \emptyset$.

### Subproblem Definition
Define:
$$d_{ij}^{(k)} = \text{weight of a shortest path from vertex } i \text{ to vertex } j \text{ with all intermediate vertices drawn from } \{1, 2, \dots, k\}$$

---

## Floyd-Warshall Recursive Definition

Consider a shortest path $p$ from $i$ to $j$ whose intermediate vertices are chosen exclusively from $\{1, 2, \dots, k\}$:

```
            (Case 2: Vertex k is an intermediate vertex)
                          [ k ]
                         ▲     \
                        /       \
     (All int. in {1..k-1})      (All int. in {1..k-1})
                      /           ▼
                   [ i ] ───────► [ j ]
             (Case 1: All int. in {1..k-1})
```

Two mutually exclusive cases arise regarding vertex $k$:

### Case 1: Vertex $k$ is NOT an intermediate vertex on path $p$.
Then all intermediate vertices on $p$ are in $\{1, 2, \dots, k - 1\}$.
Therefore:
$$d_{ij}^{(k)} = d_{ij}^{(k-1)}$$

### Case 2: Vertex $k$ IS an intermediate vertex on path $p$.
Assuming there are no negative-weight cycles, $p$ is a simple path and visits vertex $k$ **exactly once**.
We can decompose $p$ into two subpaths:
1. Subpath $p_1$ from $i$ to $k$: All intermediate vertices are in $\{1, 2, \dots, k - 1\}$.
2. Subpath $p_2$ from $k$ to $j$: All intermediate vertices are in $\{1, 2, \dots, k - 1\}$.

By the **optimal substructure** property of shortest paths, $p_1$ must be a shortest path from $i$ to $k$ with intermediate vertices in $\{1, \dots, k-1\}$, having weight $d_{ik}^{(k-1)}$.
Similarly, $p_2$ must be a shortest path from $k$ to $j$ with intermediate vertices in $\{1, \dots, k-1\}$, having weight $d_{kj}^{(k-1)}$.
Therefore:
$$d_{ij}^{(k)} = d_{ik}^{(k-1)} + d_{kj}^{(k-1)}$$

### Unified Recurrence:
Combining both cases:
$$d_{ij}^{(k)} = \min \left( d_{ij}^{(k-1)}, \; d_{ik}^{(k-1)} + d_{kj}^{(k-1)} \right)$$

### Base Case ($k = 0$):
No intermediate vertices are permitted. Paths must consist of at most a single direct edge:
$$d_{ij}^{(0)} = \omega_{ij} = \begin{cases}
0 & \text{if } i = j \\
\omega(i, j) & \text{if } i \neq j \text{ and } (i, j) \in E \\
\infty & \text{if } i \neq j \text{ and } (i, j) \notin E
\end{cases}$$
Thus, $D^{(0)} = W$.

After $n$ iterations, $V^{(n)} = \{1, 2, \dots, n\} = V$. Every vertex is permitted as an intermediate vertex:
$$D^{(n)} = (\delta(i, j))$$

---

## Standard Algorithm Formulation ($n+1$ Matrices)

```text
FLOYD-WARSHALL(W, n):
    let D^(0) = W
    let P = n x n matrix initialized to 0
    
    for k = 1 to n do
        let D^(k) be a new n x n matrix
        for i = 1 to n do
            for j = 1 to n do
                if D^(k-1)[i, j] > D^(k-1)[i, k] + D^(k-1)[k, j] then
                    D^(k)[i, j] = D^(k-1)[i, k] + D^(k-1)[k, j]
                    P[i, j] = k
                else
                    D^(k)[i, j] = D^(k-1)[i, j]
                    
    // Check for negative-weight cycles
    for i = 1 to n do
        if D^(n)[i, i] < 0 then
            error "Graph contains a negative-weight cycle"
            
    return D^(n), P
```

---

## Space Optimization: $O(V^3) \to O(V^2)$ (Ping-Pong Buffer) $\to O(V^2)$ In-Place

1. **Naive Storage:** Storing all $n + 1$ matrices takes $\Theta(n^3)$ space.
2. **Ping-Pong Buffer (2 Matrices):** Notice that computing $D^{(k)}$ requires only $D^{(k-1)}$. We can maintain just two matrices, $D_{\text{old}}$ and $D_{\text{new}}$, reducing space to $\Theta(n^2)$.
3. **In-Place Computation (1 Matrix):** Can we compute $D$ completely in-place within a single $n \times n$ matrix without any auxiliary matrix?
   **YES!**

```text
FLOYD-WARSHALL-IN-PLACE(D, n):
    // D is initialized to W
    let P be an n x n matrix initialized to 0
    
    for k = 1 to n do
        for i = 1 to n do
            for j = 1 to n do
                if D[i, k] + D[k, j] < D[i, j] then
                    D[i, j] = D[i, k] + D[k, j]
                    P[i, j] = k
                    
    for i = 1 to n do
        if D[i, i] < 0 then
            return "Graph contains a negative-weight cycle"
            
    return D, P
```

---

## Rigorous Mathematical Proof of In-Place Correctness

A critical question in algorithm design:
*Does overwriting $D[i, j]$ in-place during iteration $k$ corrupt the values needed for subsequent pairs in that same iteration $k$?*

### Formal Theorem:
> **Theorem:** In iteration $k$, updating the matrix $D$ in-place produces the exact same result as using two separate matrices $D^{(k-1)}$ and $D^{(k)}$.

### Proof:
In iteration $k$, the update rule is:
$$D^{(k)}[i, j] = \min \left( D^{(k-1)}[i, j], \; D^{(k-1)}[i, k] + D^{(k-1)}[k, j] \right)$$
For any element to be erroneously computed during an in-place update, one of the operands on the right-hand side—specifically $D[i, k]$ or $D[k, j]$—would have to be overwritten with a new value during iteration $k$ **before** being read to update some $D[i, j]$.

Let us evaluate the update rule for elements located in **row $k$** ($i = k$) and **column $k$** ($j = k$):

1. **For elements in row $k$ ($i = k$):**
   $$D^{(k)}[k, j] = \min \left( D^{(k-1)}[k, j], \; D^{(k-1)}[k, k] + D^{(k-1)}[k, j] \right)$$
   Assuming no negative-weight cycles exist:
   $$D^{(k-1)}[k, k] = 0$$
   Therefore:
   $$D^{(k-1)}[k, k] + D^{(k-1)}[k, j] = 0 + D^{(k-1)}[k, j] = D^{(k-1)}[k, j]$$
   Substituting this back into the minimization:
   $$D^{(k)}[k, j] = \min \left( D^{(k-1)}[k, j], \; D^{(k-1)}[k, j] \right) = D^{(k-1)}[k, j]$$
   **Thus, no entry in row $k$ ever changes during iteration $k$!**

2. **For elements in column $k$ ($j = k$):**
   $$D^{(k)}[i, k] = \min \left( D^{(k-1)}[i, k], \; D^{(k-1)}[i, k] + D^{(k-1)}[k, k] \right)$$
   Since $D^{(k-1)}[k, k] = 0$:
   $$D^{(k-1)}[i, k] + 0 = D^{(k-1)}[i, k]$$
   $$D^{(k)}[i, k] = \min \left( D^{(k-1)}[i, k], \; D^{(k-1)}[i, k] \right) = D^{(k-1)}[i, k]$$
   **Thus, no entry in column $k$ ever changes during iteration $k$!**

### Conclusion:
Because the entries in row $k$ and column $k$ remain completely unchanged throughout the entire $k$-th iteration, it does not matter whether $D[i, k]$ and $D[k, j]$ are read before or after any other cell $D[i, j]$ is overwritten. They always hold their original $D^{(k-1)}$ values.
Therefore, the in-place calculation is **100% mathematically correct** and requires only $\Theta(V^2)$ space. $\blacksquare$

---

## Negative-Weight Cycle Detection

How does Floyd-Warshall detect negative-weight cycles?

1. **Diagonal Property:**
   - Initially, $D[i, i] = 0$ for all $i \in \{1, \dots, n\}$.
   - If there is a negative-weight cycle reachable from vertex $i$ that can return to $i$, then the shortest path from $i$ to $i$ will pass through that cycle and have total weight $< 0$.
   - Thus, after step $k$, if vertex $k$ belongs to a negative cycle, the algorithm sets:
     $$D[k, k] = D[k, k] + D[k, k] < 0$$
2. **Detection Condition:**
   After running Floyd-Warshall for all $n$ vertices, check the diagonal elements:
   $$\exists i \in \{1, \dots, n\} \text{ such that } D[i, i] < 0 \iff G \text{ contains at least one negative-weight cycle}$$

---

## Identifying Vertices with $-\infty$ Distances

Detecting that a negative-weight cycle exists is often not enough; we must determine **which pairs of vertices $(i, j)$ have $\delta(i, j) = -\infty$**.

A pair $(i, j)$ has $\delta(i, j) = -\infty$ if and only if there exists some vertex $k$ such that:
1. $k$ is reachable from $i$ ($d_{ik} < \infty$).
2. $k$ is on a negative cycle ($d_{kk} < 0$).
3. $j$ is reachable from $k$ ($d_{kj} < \infty$).

### Post-Processing Algorithm ($O(V^2)$ or $O(V^3)$):
```text
FIND-MINUS-INFINITY-PAIRS(D, n):
    for k = 1 to n do
        if D[k, k] < 0 then
            for i = 1 to n do
                for j = 1 to n do
                    if D[i, k] < ∞ and D[k, j] < ∞ then
                        D[i, j] = -∞
```

---

## Shortest Path Reconstruction

### Method 1: Intermediate Pointer Matrix $P$ (Recursive Method)
As defined in lecture slides 20 & 30:
- $P[i, j] = 0$ initially.
- When $D[i, j] > D[i, k] + D[k, j]$, update $P[i, j] \leftarrow k$.
- $P[i, j]$ records the **highest-indexed intermediate vertex** that provided the minimum path length.

```text
PRINT-PATH(P, D, q, r):
    if D[q, r] == ∞ then
        print "No path"
        return
    print "v" + q
    INTERMEDIATE-PATH(P, q, r)
    if q != r then
        print "v" + r

INTERMEDIATE-PATH(P, q, r):
    if P[q, r] != 0 then
        INTERMEDIATE-PATH(P, q, P[q, r])
        print "v" + P[q, r]
        INTERMEDIATE-PATH(P, P[q, r], r)
```

### Method 2: Predecessor Matrix $\Pi$ (Iterative / Standard Method)
- $\Pi[i, j]$ denotes the immediate predecessor of $j$ on a shortest path from $i$.
- **Initialization ($k = 0$):**
  $$\Pi^{(0)}[i, j] = \begin{cases} 
  \text{NIL} & \text{if } i = j \text{ or } \omega_{ij} = \infty \\ 
  i & \text{if } i \neq j \text{ and } \omega_{ij} < \infty 
  \end{cases}$$
- **Update Rule ($k \ge 1$):**
  $$\Pi^{(k)}[i, j] = \begin{cases} 
  \Pi^{(k-1)}[i, j] & \text{if } D^{(k-1)}[i, j] \le D^{(k-1)}[i, k] + D^{(k-1)}[k, j] \\ 
  \Pi^{(k-1)}[k, j] & \text{if } D^{(k-1)}[i, j] > D^{(k-1)}[i, k] + D^{(k-1)}[k, j] 
  \end{cases}$$

```text
PRINT-PATH-PREDECESSOR(Π, i, j):
    if i == j then
        print i
    else if Π[i, j] == NIL then
        print "No path from " + i + " to " + j
    else
        PRINT-PATH-PREDECESSOR(Π, i, Π[i, j])
        print j
```

---

## Comprehensive Trace 1: 3-Vertex Graph with Negative Edge

Let us trace the example from lecture slides 23–26:
Vertices: $\{1, 2, 3\}$.
Edges and weights:
- $(1, 2) = 4$, $(1, 3) = 5$
- $(2, 1) = 2$
- $(3, 2) = -3$
- No edge $(2, 3)$, no edge $(3, 1)$.

```
           (1)
          ▲   \
       2 /     \ 4, 5
        /       ▼
      (2) ◄──── (3)
           -3
```

### Step 0: Initial Matrices ($k = 0$)
$$D^{(0)} = \begin{pmatrix}
0 & 4 & 5 \\
2 & 0 & \infty \\
\infty & -3 & 0
\end{pmatrix}, \quad 
P^{(0)} = \begin{pmatrix}
0 & 0 & 0 \\
0 & 0 & 0 \\
0 & 0 & 0
\end{pmatrix}$$

---

### Step 1: $k = 1$ (Vertex 1 can be an intermediate node)
We test if paths can be shortened by passing through vertex 1:
- $D^{(1)}[2, 3] = \min(D^{(0)}[2, 3], D^{(0)}[2, 1] + D^{(0)}[1, 3]) = \min(\infty, 2 + 5) = \mathbf{7}$
  Update: $P[2, 3] \leftarrow 1$.
- $D^{(1)}[3, 2] = \min(D^{(0)}[3, 2], D^{(0)}[3, 1] + D^{(0)}[1, 2]) = \min(-3, \infty + 4) = -3$. (No change)
- All other entries remain unchanged.

$$D^{(1)} = \begin{pmatrix}
0 & 4 & 5 \\
2 & 0 & \mathbf{7} \\
\infty & -3 & 0
\end{pmatrix}, \quad 
P^{(1)} = \begin{pmatrix}
0 & 0 & 0 \\
0 & 0 & \mathbf{1} \\
0 & 0 & 0
\end{pmatrix}$$

---

### Step 2: $k = 2$ (Vertices $\{1, 2\}$ can be intermediate nodes)
We test if paths can be shortened by passing through vertex 2:
- $D^{(2)}[1, 3] = \min(D^{(1)}[1, 3], D^{(1)}[1, 2] + D^{(1)}[2, 3]) = \min(5, 4 + 7) = 5$. (No change)
- $D^{(2)}[3, 1] = \min(D^{(1)}[3, 1], D^{(1)}[3, 2] + D^{(1)}[2, 1]) = \min(\infty, -3 + 2) = \mathbf{-1}$
  Update: $P[3, 1] \leftarrow 2$.

$$D^{(2)} = \begin{pmatrix}
0 & 4 & 5 \\
2 & 0 & 7 \\
\mathbf{-1} & -3 & 0
\end{pmatrix}, \quad 
P^{(2)} = \begin{pmatrix}
0 & 0 & 0 \\
0 & 0 & 1 \\
\mathbf{2} & 0 & 0
\end{pmatrix}$$

---

### Step 3: $k = 3$ (Vertices $\{1, 2, 3\}$ can be intermediate nodes)
We test if paths can be shortened by passing through vertex 3:
- $D^{(3)}[1, 2] = \min(D^{(2)}[1, 2], D^{(2)}[1, 3] + D^{(2)}[3, 2]) = \min(4, 5 + (-3)) = \min(4, 2) = \mathbf{2}$
  Update: $P[1, 2] \leftarrow 3$.
- $D^{(3)}[2, 1] = \min(D^{(2)}[2, 1], D^{(2)}[2, 3] + D^{(2)}[3, 1]) = \min(2, 7 + (-1)) = \min(2, 6) = 2$. (No change)

$$D^{(3)} = \begin{pmatrix}
0 & \mathbf{2} & 5 \\
2 & 0 & 7 \\
-1 & -3 & 0
\end{pmatrix}, \quad 
P^{(3)} = \begin{pmatrix}
0 & \mathbf{3} & 0 \\
0 & 0 & 1 \\
2 & 0 & 0
\end{pmatrix}$$

### Path Reconstruction Example on This Graph:
Find the shortest path from vertex 1 to vertex 2:
1. Call `PRINT-PATH(P, D, 1, 2)`.
2. Print `v1`.
3. Call `INTERMEDIATE-PATH(P, 1, 2)`:
   - $P[1, 2] = 3 \neq 0$.
   - Call `INTERMEDIATE-PATH(P, 1, 3)`: $P[1, 3] = 0 \implies$ returns without printing.
   - Print `v3`.
   - Call `INTERMEDIATE-PATH(P, 3, 2)`: $P[3, 2] = 0 \implies$ returns without printing.
4. Print `v2`.
**Reconstructed Path:** $v_1 \to v_3 \to v_2$.
Weight: $\omega(1, 3) + \omega(3, 2) = 5 + (-3) = 2 = D[1, 2]$. Correct!

---

## Comprehensive Trace 2: 6-Vertex Graph

Let us analyze the 6-vertex undirected graph from slides 31 & 32:
Vertices: $\{1, 2, 3, 4, 5, 6\}$.
Edges:
- $(1, 5) = 3$, $(1, 2) = 5$, $(1, 6) = 1$, $(1, 4) = 7$
- $(5, 4) = 3$
- $(2, 6) = 1$, $(2, 3) = 4$
- $(6, 3) = 1$, $(6, 4) = 50$
- $(4, 3) = 2$

### Final Distance Matrix $D^{(6)}$ and Non-Zero $P$ Values:
Entries are formatted as `Distance(P)`:

$$D^{(6)} = \begin{pmatrix}
0 & 2(6) & 2(6) & 4(6) & 3 & 1 \\
2(6) & 0 & 2(6) & 4(6) & 5(6) & 1 \\
2(6) & 2(6) & 0 & 2 & 5(4) & 1 \\
4(6) & 4(6) & 2 & 0 & 3 & 3(3) \\
3 & 5(6) & 5(4) & 3 & 0 & 4(1) \\
1 & 1 & 1 & 3(3) & 4(1) & 0
\end{pmatrix}$$

### Step-by-Step Path Reconstruction Trace:
Let us trace the path from vertex 5 to vertex 2:
1. $D[5, 2] = 5$, $P[5, 2] = 6$.
2. Recursion splits into:
   - Subpath $5 \to 6$: $P[5, 6] = 1$.
     - Subpath $5 \to 1$: $P[5, 1] = 0 \implies$ edge $(5, 1)$.
     - Vertex 1.
     - Subpath $1 \to 6$: $P[1, 6] = 0 \implies$ edge $(1, 6)$.
     $\implies$ Subpath $5 \to 1 \to 6$.
   - Vertex 6.
   - Subpath $6 \to 2$: $P[6, 2] = 0 \implies$ edge $(6, 2)$.
3. Full sequence: $v_5 \to v_1 \to v_6 \to v_2$.
4. Weight verification: $\omega(5, 1) + \omega(1, 6) + \omega(6, 2) = 3 + 1 + 1 = 5 = D[5, 2]$. Exactly matches!

---

# 5. Transitive Closure & Warshall's Algorithm

## Reachability Problem Definition
Given a directed graph $G = (V, E)$, the **transitive closure** is a graph $G^* = (V, E^*)$ where $(i, j) \in E^*$ if and only if there is a directed path from vertex $i$ to vertex $j$ in $G$.
The output is represented as an $n \times n$ boolean matrix $T = (t_{ij})$:
$$t_{ij} = \begin{cases} 
1 & \text{if there is a path from } i \text{ to } j \\ 
0 & \text{otherwise} 
\end{cases}$$

---

## Boolean Formulation of Warshall's Algorithm
Instead of using arithmetic operations $(\min, +)$ over real numbers, Warshall's algorithm operates over the **Boolean semiring** $(\{0, 1\}, \lor, \land)$:

### Base Case ($k = 0$):
$$t_{ij}^{(0)} = \begin{cases} 
1 & \text{if } i = j \text{ or } (i, j) \in E \\ 
0 & \text{otherwise} 
\end{cases}$$

### Recurrence ($k \ge 1$):
$$t_{ij}^{(k)} = t_{ij}^{(k-1)} \lor \left( t_{ik}^{(k-1)} \land t_{kj}^{(k-1)} \right)$$

In English: There is a path from $i$ to $j$ using intermediate vertices in $\{1, \dots, k\}$ if:
1. There was already a path using vertices in $\{1, \dots, k-1\}$, OR
2. There is a path from $i$ to $k$ AND a path from $k$ to $j$, both using vertices in $\{1, \dots, k-1\}$.

```text
TRANSITIVE-CLOSURE(G, n):
    let T be an n x n boolean matrix
    for i = 1 to n do
        for j = 1 to n do
            if i == j or (i, j) in E then
                T[i, j] = 1
            else
                T[i, j] = 0
                
    for k = 1 to n do
        for i = 1 to n do
            for j = 1 to n do
                T[i, j] = T[i, j] or (T[i, k] and T[k, j])
                
    return T
```

---

## Bitwise Parallelism Optimization ($O(V^3 / w)$)

Because Warshall's algorithm uses purely logical operations (`AND`, `OR`), each row of the matrix can be stored as a **bitset** or array of 64-bit unsigned integers (`uint64_t`).

Notice that the inner loop updates row $i$ using row $k$:
$$\text{If } T[i, k] == 1 \implies \text{row}_i = \text{row}_i \lor \text{row}_k$$
$$\text{If } T[i, k] == 0 \implies \text{row}_i \text{ remains unchanged}$$

```cpp
// Highly optimized Warshall in C++ using std::bitset
const int MAXN = 1000;
std::bitset<MAXN> reach[MAXN];

void transitive_closure(int n) {
    for (int k = 0; k < n; ++k) {
        for (int i = 0; i < n; ++i) {
            if (reach[i][k]) {
                reach[i] |= reach[k];
            }
        }
    }
}
```

### Complexity Gain:
- Bitwise `OR` processes $w = 64$ entries simultaneously in a single CPU clock cycle.
- **Time Complexity:**
  $$O\left(\frac{V^3}{w}\right) \approx O\left(\frac{V^3}{64}\right)$$
- For $V = 1000$, standard $V^3 = 10^9$ operations (several seconds); with bitset, $\approx 1.5 \times 10^7$ operations, running in **under 15 milliseconds**!

---

# 6. Master Comparative Summary Matrix

| Algorithm | Graph Assumptions | Edge Weights | Time (Sparse: $E = O(V)$) | Time (Dense: $E = O(V^2)$) | Space Complexity | Negative Cycle Detection? | Primary Use Case / Strengths |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **BFS $\times V$** | Directed / Undirected | Unweighted ($\omega = 1$) | $O(V^2)$ | $O(V^3)$ | $O(V^2)$ | No (N/A) | Optimal for unweighted graphs. |
| **DAG-SSSP $\times V$** | Directed Acyclic Graph | Arbitrary weights | $O(V^2)$ | $O(V^3)$ | $O(V^2)$ | No (acyclic by def.) | Optimal for DAGs. |
| **Dijkstra $\times V$ (Binary Heap)** | Directed / Undirected | Non-negative ($\omega \ge 0$) | $O(V^2 \log V)$ | $O(V^3 \log V)$ | $O(V^2)$ | Cannot handle negative edges | Standard choice for sparse graphs with $\omega \ge 0$. |
| **Dijkstra $\times V$ (Fibonacci Heap)**| Directed / Undirected | Non-negative ($\omega \ge 0$) | $O(V^2 \log V)$ | $O(V^3)$ | $O(V^2)$ | Cannot handle negative edges | Asymptotically superior for dense non-negative graphs. |
| **Bellman-Ford $\times V$** | Directed / Undirected | Arbitrary | $O(V^3)$ | $O(V^4)$ | $O(V^2)$ | Yes (per source) | Highly inefficient; baseline conceptual model only. |
| **Repeated Squaring (Matrix Mult.)**| Directed | Arbitrary | $\Theta(V^3 \log V)$ | $\Theta(V^3 \log V)$ | $\Theta(V^2)$ | Yes ($D^{(2n)} \neq D^{(n)}$) | Elegant algebra; easily parallelized on GPUs. |
| **Floyd-Warshall** | Directed (Undirected if $\omega \ge 0$) | Arbitrary | $\Theta(V^3)$ | $\Theta(V^3)$ | $\Theta(V^2)$ | Yes ($D[i, i] < 0$) | Extremely compact code, in-place, fantastic cache locality. |
| **Johnson's Algorithm** | Directed | Arbitrary | $O(V^2 \log V)$ | $O(V^3)$ | $O(V^2)$ | Yes (via initial Bellman-Ford) | Asymptotically fastest APSP for sparse graphs with arbitrary weights. |
| **Warshall (Transitive Closure)** | Directed | Unweighted / Boolean | $O(V^3 / w)$ | $O(V^3 / w)$ | $O(V^2 / w)$ | No | Bitset-accelerated reachability queries. |

---

# 7. Algorithmic Design Variations & Advanced Complex Problems

## Problem 1: Dynamic Graph Update — Edge Weight Decrease ($O(V^2)$)
**Scenario:** We have already computed the APSP distance matrix $D$ for a graph $G = (V, E)$ using Floyd-Warshall in $O(V^3)$ time. A single directed edge $(u, v)$ has its weight reduced from $\omega(u, v)$ to $\omega'(u, v) < \omega(u, v)$.
**Question:** Can we update the all-pairs shortest paths matrix $D$ in $O(V^2)$ time rather than rerunning Floyd-Warshall from scratch in $O(V^3)$?

### Solution:
**Yes.** Any path whose length decreases must now utilize the modified edge $(u, v)$.
For any arbitrary pair of vertices $(i, j)$:
- The shortest path either does not use $(u, v)$ (its distance remains $D[i, j]$).
- Or it traverses from $i$ to $u$, crosses $(u, v)$, and traverses from $v$ to $j$.

The new shortest distance $D'[i, j]$ is:
$$D'[i, j] = \min \left( D[i, j], \; D[i, u] + \omega'(u, v) + D[v, j] \right)$$

### Algorithm:
```text
UPDATE-APSP-EDGE-DECREASE(D, u, v, w_prime, n):
    for i = 1 to n do
        for j = 1 to n do
            if D[i, u] + w_prime + D[v, j] < D[i, j] then
                D[i, j] = D[i, u] + w_prime + D[v, j]
                // Predecessor / Intermediate matrices updated accordingly
```
- Two nested loops iterating over all pairs $(i, j)$.
- **Time Complexity:** Exactly $\Theta(V^2)$.

---

## Problem 2: Dynamic Graph Update — Adding a New Vertex ($O(V^2)$)
**Scenario:** A new vertex $v_{n+1}$ is added to an existing graph of $n$ vertices along with directed edges to and from existing vertices.
**Question:** Update the $(n+1) \times (n+1)$ distance matrix in $O(V^2)$ time.

### Solution:
1. Initialize the new row $D[n+1, j]$ and column $D[i, n+1]$:
   - For all $j \in \{1, \dots, n\}$, $D[n+1, j] = \min_{1 \le k \le n} \{\omega(v_{n+1}, k) + D[k, j]\}$. (Time: $O(V^2)$).
   - For all $i \in \{1, \dots, n\}$, $D[i, n+1] = \min_{1 \le k \le n} \{D[i, k] + \omega(k, v_{n+1})\}$. (Time: $O(V^2)$).
   - $D[n+1, n+1] = 0$.
2. Run one single Floyd-Warshall outer iteration with $k = n + 1$:
   - For all $i, j \in \{1, \dots, n+1\}$:
     $$D[i, j] = \min(D[i, j], D[i, n+1] + D[n+1, j])$$
   - Time: $\Theta(V^2)$.
- **Total Time Complexity:** $\Theta(V^2)$.

---

## Problem 3: Finding the Minimum Weight Cycle (Shortest Cycle)
**Scenario:** In a directed graph with no negative cycles, find the cycle with the minimum total weight.

### Solution:
In Floyd-Warshall, the diagonal $D[i, i]$ is initialized to 0 because the empty path has 0 edges.
To find non-trivial cycles:
1. Initialize $D[i, i] = \infty$ for all $i \in \{1, \dots, n\}$ (instead of 0).
2. If edge $(i, i)$ exists, initialize $D[i, i] = \omega(i, i)$.
3. Run standard Floyd-Warshall.
4. At termination, $D[i, i]$ holds the length of the shortest non-empty cycle passing through vertex $i$.
5. The global minimum cycle weight is:
   $$\min_{1 \le i \le n} D[i, i]$$
- **Time Complexity:** $\Theta(V^3)$.

---

## Problem 4: Finding the Girth of an Undirected Graph
**Scenario:** Find the length of the shortest cycle (girth) in an undirected, unweighted graph.

> [!WARNING]
> In an undirected graph, simply setting $D[i, i] = \infty$ in Floyd-Warshall fails because the algorithm will traverse a single edge back and forth: $u \to v \to u$ of length 2, which is not a valid simple cycle.

### Solution via BFS:
For an unweighted undirected graph:
- For each vertex $v \in V$, run BFS.
- When an edge $(x, y)$ is encountered to an already-visited vertex $y$ that is not the BFS parent of $x$:
  $$\text{Cycle Length} = \text{dist}(v, x) + \text{dist}(v, y) + 1$$
- Maintain the minimum cycle length found across all BFS trees.
- **Time Complexity:** $V \times O(V + E) = O(V^2 + VE)$.

---

## Problem 5: Minimax Paths (Widest Path / Maximum Bottleneck Path)
**Scenario:** Find a path between every pair $(i, j)$ that **maximizes the minimum edge weight** along the path (maximizing capacity).

### Algebraic Adaptation:
- Replace min with max: $\min \to \max$
- Replace addition with min: $+ \to \min$
- Base case: $D[i, j] = \omega(i, j)$ if $(i, j) \in E$, else $-\infty$; $D[i, i] = \infty$.
- Recurrence:
  $$D^{(k)}[i, j] = \max \left( D^{(k-1)}[i, j], \; \min(D^{(k-1)}[i, k], D^{(k-1)}[k, j]) \right)$$
- **Time Complexity:** $\Theta(V^3)$.

---

## Problem 6: Maximum Reliability Paths (Multiplicative Semiring)
**Scenario:** Each edge $(u, v)$ has an independent probability $p(u, v) \in (0, 1]$ of functioning. Find the most reliable path between all pairs (maximizing the product of edge probabilities).

### Solution via Log-Transform:
We wish to maximize $\prod_{(u, v) \in p} p(u, v)$.
Taking the negative logarithm:
$$\max \prod p(u, v) \iff \min -\sum \ln(p(u, v))$$
Since $0 < p(u, v) \le 1$, $-\ln(p(u, v)) \ge 0$.
All transformed edge weights are **non-negative real numbers**!
1. Set edge weights $w'(u, v) = -\ln(p(u, v))$.
2. Run Floyd-Warshall or Dijkstra $\times V$.
3. Convert distances back: $\text{Reliability}(i, j) = \exp(-D[i, j])$.

Alternatively, formulate directly within Floyd-Warshall:
$$D^{(k)}[i, j] = \max \left( D^{(k-1)}[i, j], \; D^{(k-1)}[i, k] \times D^{(k-1)}[k, j] \right)$$
Base case: $D[i, i] = 1$; $D[i, j] = p(i, j)$ if $(i, j) \in E$, else 0.

---

## Problem 7: Graph Diameter, Radius, and Center
Given the APSP distance matrix $D$:
1. **Eccentricity of a vertex $v$:**
   $$\epsilon(v) = \max_{u \in V} D[v, u]$$
2. **Diameter of the graph:** The maximum distance between any pair of vertices:
   $$\text{Diameter}(G) = \max_{v \in V} \epsilon(v) = \max_{u, v \in V} D[u, v]$$
3. **Radius of the graph:** The minimum eccentricity:
   $$\text{Radius}(G) = \min_{v \in V} \epsilon(v)$$
4. **Center of the graph:** The set of vertices with eccentricity equal to the radius:
   $$\text{Center}(G) = \{v \in V : \epsilon(v) = \text{Radius}(G)\}$$
Once $D$ is computed in $O(V^3)$, all four metrics can be extracted in $\Theta(V^2)$ time.

---

## Problem 8: Counting the Number of Shortest Paths
**Scenario:** Compute not just the shortest distance, but the **number of distinct shortest paths** between every pair of vertices.

### Solution:
Maintain two matrices simultaneously:
- $D[i, j]$: Shortest path weight.
- $C[i, j]$: Number of shortest paths from $i$ to $j$.

### Base Case ($k = 0$):
- $D[i, i] = 0, C[i, i] = 1$.
- For $i \neq j$:
  - If $(i, j) \in E$: $D[i, j] = \omega(i, j), C[i, j] = 1$.
  - If $(i, j) \notin E$: $D[i, j] = \infty, C[i, j] = 0$.

### Induction Step ($k = 1 \dots n$):
For each pair $(i, j)$:
Let candidate distance be $alt = D[i, k] + D[k, j]$.
1. If $alt < D[i, j]$:
   - Shorter path found:
   - $D[i, j] \leftarrow alt$
   - $C[i, j] \leftarrow C[i, k] \times C[k, j]$
2. If $alt == D[i, j]$:
   - Additional paths of identical minimum length discovered:
   - $C[i, j] \leftarrow C[i, j] + (C[i, k] \times C[k, j])$
3. If $alt > D[i, j]$:
   - No change.

---

## Problem 9: Currency Arbitrage Detection
**Scenario:** Given $n$ currencies and exchange rates $R[i, j]$ (1 unit of currency $i$ buys $R[i, j]$ units of currency $j$). Determine if there exists a sequence of currency conversions starting and ending at currency $i$ such that:
$$R[i, k_1] \times R[k_1, k_2] \times \dots \times R[k_m, i] > 1$$

### Solution:
Taking negative logarithms:
$$\prod R > 1 \iff \sum \ln(R) > 0 \iff \sum (-\ln(R)) < 0$$
1. Assign edge weights $\omega(i, j) = -\ln(R[i, j])$.
2. An arbitrage opportunity exists $\iff$ there is a **negative-weight cycle** in the graph!
3. Run Floyd-Warshall in $\Theta(n^3)$ time.
4. If $\exists i$ such that $D[i, i] < 0$, arbitrage exists!
5. The cycle can be reconstructed using $P$ or $\Pi$.

---

## Problem 10: Sub-Cubic APSP Algorithms (Theoretical Frontiers)

For decades, a major open question in theoretical computer science was whether APSP on general graphs can be solved in $O(V^{3-\epsilon})$ for some $\epsilon > 0$.

1. **Seidel's Algorithm (1995):**
   - For **unweighted, undirected graphs**, APSP can be solved in:
     $$O(V^\omega \log V)$$
     where $\omega < 2.373$ is the matrix multiplication exponent (using Coppersmith-Winograd or modern variants).
2. **Ryan Williams' Algorithm (2014):**
   - For **general weighted graphs**, APSP can be solved deterministically in:
     $$O\left(\frac{V^3}{2^{\sqrt{\log V}}}\right)$$
   - This proves that APSP is strictly sub-cubic, though the speedup is slightly sub-polynomial.
3. **The APSP Conjecture:**
   - In fine-grained complexity, it is widely hypothesized that APSP on general graphs cannot be solved in $O(V^{3-\epsilon})$ time for any constant $\epsilon > 0$. Many hardness results for other problems (e.g., dynamic reachability, radius, graph median) rely on this hypothesis.

---

# 8. Deep Intuitive Insights, Common Traps & Reasoning Questions

## Why MUST the $k$-loop be outermost in Floyd-Warshall?

Consider the loop structure of Floyd-Warshall:
```text
for k = 1 to n do         // MUST BE OUTERMOST!
    for i = 1 to n do
        for j = 1 to n do
            D[i, j] = min(D[i, j], D[i, k] + D[k, j])
```

### What happens if you make $i$ or $k$ inner?
Suppose a programmer erroneously writes:
```text
for i = 1 to n do         // WRONG!
    for j = 1 to n do
        for k = 1 to n do
            D[i, j] = min(D[i, j], D[i, k] + D[k, j])
```

### Concrete Failure Demonstration:
Consider a simple linear path:
$$1 \to 2 \to 3 \to 4$$
All edge weights = 1.
Initial matrix $D$:
- $D[1, 2] = 1$, $D[2, 3] = 1$, $D[3, 4] = 1$.
- All other off-diagonal entries = $\infty$.

When the erroneous code runs for $i = 1$:
- For $j = 4$, it loops $k \in \{1, 2, 3, 4\}$ to find $\min(D[1, k] + D[k, 4])$.
  - $k=1$: $D[1, 1] + D[1, 4] = 0 + \infty = \infty$.
  - $k=2$: $D[1, 2] + D[2, 4] = 1 + \infty = \infty$ (because $D[2, 4]$ has NOT been computed yet; $i = 2$ hasn't executed!).
  - $k=3$: $D[1, 3] + D[3, 4] = \infty + 1 = \infty$ (because $D[1, 3]$ was never discovered).
  - $k=4$: $D[1, 4] + D[4, 4] = \infty + 0 = \infty$.
- The algorithm finishes $i = 1$ with $D[1, 4] = \infty$!
- Because $i$ is never revisited, $D[1, 4]$ remains $\infty$ forever.

### Intuitive Reason:
The outer loop $k$ represents the **mathematical induction step**. In iteration $k$, the algorithm computes shortest paths through the restricted intermediate universe $\{1, \dots, k\}$ using the already-complete results from $\{1, \dots, k-1\}$. If $k$ is not outermost, paths of length $> 2$ cannot propagate transitively across intermediate vertices.

---

## Can intermediate vertices be processed in any arbitrary permutation?

**Question:** Does Floyd-Warshall require vertices to be processed strictly in order $1, 2, \dots, n$? What if we process them in an arbitrary permutation $\sigma = \langle \sigma_1, \sigma_2, \dots, \sigma_n \rangle$?

### Answer:
**YES, ANY PERMUTATION WORKS PERFECTLY!**
Let $\sigma$ be any arbitrary permutation of $V$.
Define the nested subsets:
$$S_0 = \emptyset, \quad S_k = S_{k-1} \cup \{\sigma_k\}$$
At step $k$, $S_k$ contains $k$ arbitrary vertices. The recurrence:
$$D^{(k)}[i, j] = \min \left( D^{(k-1)}[i, j], \; D^{(k-1)}[i, \sigma_k] + D^{(k-1)}[\sigma_k, j] \right)$$
correctly computes shortest paths restricted to intermediate vertices in $S_k$.
When $k = n$, $S_n = V$, guaranteeing that all intermediate vertices have been permitted regardless of the insertion order!

---

## Why does an undirected graph with negative weights break Floyd-Warshall?

In a directed graph, a single negative edge $(u, v)$ with weight $-5$ does not necessarily form a negative cycle unless there is a return path from $v$ to $u$ of weight $< 5$.

In an **undirected graph**, an edge between $u$ and $v$ with weight $\omega(u, v) = -5$ implies:
$$\omega(u \to v) = -5 \quad \text{AND} \quad \omega(v \to u) = -5$$
This immediately forms a **2-cycle**:
$$u \to v \to u \quad \text{with weight } (-5) + (-5) = -10 < 0$$
Any undirected graph containing even a **single negative-weight edge** inherently contains an infinite negative-weight cycle!
Therefore, APSP is undefined ($-\infty$) for any vertices in the connected component containing that edge.

---

## Why is Floyd-Warshall often faster than Dijkstra in practice on dense graphs?

Asymptotically:
- Dijkstra $\times V$ with binary heap: $O(V^3 \log V)$.
- Dijkstra $\times V$ with array: $O(V^3)$.
- Floyd-Warshall: $\Theta(V^3)$.

On paper, Dijkstra with an array has the same $\Theta(V^3)$ complexity as Floyd-Warshall.
However, in practical wall-clock time benchmarks on modern hardware, **Floyd-Warshall can be $5\times$ to $20\times$ faster**. Why?

1. **Memory Locality & Cache Hits:**
   - Floyd-Warshall accesses memory in tight, sequential row-major order: `D[i][j]` inside the innermost loop. This results in near-perfect L1/L2 cache prefetching.
   - Priority-queue Dijkstra does pointer chasing and scattered array indexing, incurring massive cache misses.
2. **Extremely Low Constant Factors:**
   - The inner loop of Floyd-Warshall consists of one addition, one comparison, and one memory write:
     ```c
     int alt = D[i][k] + D[k][j];
     if (alt < D[i][j]) D[i][j] = alt;
     ```
3. **Compiler Vectorization (SIMD):**
   - Modern compilers (GCC, Clang) can vectorize the inner loop using AVX2 or AVX-512 instructions, processing 8 or 16 integers per cycle.

---

## Edge Bounding vs. Vertex Bounding: Structural Comparison

```
┌───────────────────────────────┬──────────────────────────────────┬─────────────────────────────────┐
│ Feature                       │ Matrix Multiplication (APSP)     │ Floyd-Warshall Algorithm        │
├───────────────────────────────┼──────────────────────────────────┼─────────────────────────────────┤
│ Subproblem Parameter          │ Number of edges $m \le l$        │ Intermediate vertex set $\{1..k\}$│
│ Subproblem Dimension          │ $n \times n \times n$            │ $n \times n \times n$           │
│ State Transition Nature       │ Edge extension $(k \to j \in E)$ │ Vertex detour via vertex $k$    │
│ Basic Step Time               │ $\Theta(V^3)$ (One matrix prod)  │ $\Theta(V^2)$ (One matrix sweep)│
│ Number of Steps               │ $\log_2 V$ (Repeated squaring)   │ $V$ steps                       │
│ Total Time Complexity         │ $\Theta(V^3 \log V)$             │ $\Theta(V^3)$                   │
│ In-Place Update Feasible?     │ No (requires separate matrix)    │ Yes (provably safe in-place)    │
│ Space Complexity              │ $\Theta(V^2)$                    │ $\Theta(V^2)$                   │
└───────────────────────────────┴──────────────────────────────────┴─────────────────────────────────┘
```

---

# 9. Exhaustive Conceptual True / False Question Bank (With Deep Justifications)

Here is a comprehensive set of rigorous, conceptual True/False questions testing the deepest nuances of APSP.

---

### Q1: In Floyd-Warshall, exchanging the order of the two inner loops (iterating $j$ before $i$) produces an incorrect result.
> **Answer: FALSE.**
> **Justification:** In iteration $k$, the update for entry $(i, j)$ depends only on $D^{(k-1)}[i, k]$ and $D^{(k-1)}[k, j]$. Because entries in row $k$ and column $k$ do not change during iteration $k$, every pair $(i, j)$ is completely independent of every other pair $(i', j')$ within the same iteration $k$. Thus, looping $i$ then $j$ or $j$ then $i$ produces the identical result. Only $k$ must remain outermost.

---

### Q2: If a graph has no negative cycles, all diagonal entries of $D^{(k)}$ will remain zero throughout all iterations of Floyd-Warshall.
> **Answer: TRUE.**
> **Justification:** $D^{(k)}[i, i] = \min(D^{(k-1)}[i, i], D^{(k-1)}[i, k] + D^{(k-1)}[k, i])$. Since there are no negative cycles, any path from $i$ to $k$ and back to $i$ has weight $D^{(k-1)}[i, k] + D^{(k-1)}[k, i] \ge 0$. Since $D^{(0)}[i, i] = 0$, the minimum will always remain $\min(0, \ge 0) = 0$.

---

### Q3: In Repeated Squaring, $D^{(2m)}$ can be calculated directly by multiplying $D^{(m)} \odot D^{(m)}$ because min-plus matrix multiplication is associative.
> **Answer: TRUE.**
> **Justification:** Matrix multiplication over the $(\min, +)$ semiring is strictly associative: $(A \odot B) \odot C = A \odot (B \odot C)$. Therefore, computing $W^{2m} = W^m \odot W^m$ is algebraically equivalent to multiplying $W$ by itself $2m$ times.

---

### Q4: If all edge weights in a graph are squared ($w'(e) = (w(e))^2$), the shortest paths remain unchanged.
> **Answer: FALSE.**
> **Justification:** Squaring is a non-linear transformation. Consider Path $A$ with two edges of weight 3 (total = 6) and Path $B$ with one edge of weight 5 (total = 5). Path $B$ is initially shorter ($5 < 6$). After squaring: Path $A$ has weight $3^2 + 3^2 = 9 + 9 = 18$. Path $B$ has weight $5^2 = 25$. Now Path $A$ is shorter ($18 < 25$)! The shortest path changes.

---

### Q5: In Floyd-Warshall, if $D[i, i] < 0$ at the end of the algorithm, then vertex $i$ is guaranteed to be part of a negative-weight cycle.
> **Answer: TRUE.**
> **Justification:** $D[i, i]$ represents the length of a path starting at $i$, visiting intermediate vertices, and returning to $i$. A path starting and ending at $i$ with weight $< 0$ is, by definition, a negative-weight cycle containing $i$.

---

### Q6: Running Dijkstra's algorithm $V$ times can be used to solve APSP on any graph that has negative edge weights, provided it has no negative cycles.
> **Answer: FALSE.**
> **Justification:** Dijkstra's algorithm fundamentally assumes non-negative edge weights ($\omega(e) \ge 0$). The greedy choice property fails when negative edges are present, even if there are no negative cycles.

---

### Q7: If a directed graph has negative edge weights but no negative cycles, Johnson's algorithm will always find the correct all-pairs shortest paths in $O(V^2 \log V + VE)$ time.
> **Answer: TRUE.**
> **Justification:** Johnson's algorithm computes a potential function $h(v)$ using Bellman-Ford in $O(VE)$ time, reweights all edges to $\widehat{\omega}(u, v) \ge 0$ while preserving shortest path equivalence, and then executes Dijkstra $V$ times using a Fibonacci heap in $O(V(V \log V + E))$ time.

---

### Q8: Adding a positive constant $C > 0$ to every edge in a graph preserves the shortest path between every pair of vertices.
> **Answer: FALSE.**
> **Justification:** Adding $C$ to every edge increases the weight of a path with $k$ edges by $k \cdot C$. A path with more edges is penalized more heavily than a path with fewer edges, which can alter the shortest path.

---

### Q9: In Floyd-Warshall, the intermediate vertices $\{v_1, \dots, v_n\}$ must be indexed according to a topological sort of the graph.
> **Answer: FALSE.**
> **Justification:** Floyd-Warshall operates on general directed graphs, which may contain cycles and therefore have no topological sort. Furthermore, any arbitrary permutation of the vertices yields the exact same final distance matrix.

---

### Q10: Transitive closure can be solved in $O(V^3 / w)$ time using bitwise operations, where $w$ is the machine word size.
> **Answer: TRUE.**
> **Justification:** Warshall's algorithm computes $T[i, j] = T[i, j] \lor (T[i, k] \land T[k, j])$. For fixed $i$ and $k$, if $T[i, k] = 1$, the operation updates row $i$ by computing $\text{row}_i = \text{row}_i \lor \text{row}_k$. A bitset performs 64 bitwise OR operations per clock cycle, accelerating the algorithm by a factor of $w = 64$.

---

### Q11: In the matrix multiplication formulation of APSP, $d_{ij}^{(n-1)} = d_{ij}^{(n)}$ if and only if the graph has no negative-weight cycles.
> **Answer: TRUE.**
> **Justification:** If there are no negative cycles, any shortest path is simple and contains at most $n - 1$ edges. Allowing an $n$-th edge cannot reduce the path weight. Conversely, if a negative cycle is reachable, an $n$-th edge can traverse the cycle again to yield $d_{ij}^{(n)} < d_{ij}^{(n-1)}$.

---

### Q12: The Floyd-Warshall algorithm can correctly find all shortest path distances in an undirected graph that contains at least one negative edge.
> **Answer: FALSE.**
> **Justification:** In an undirected graph, a single negative edge $(u, v)$ with weight $w < 0$ forms a 2-cycle $u \to v \to u$ with total weight $2w < 0$. This is a negative-weight cycle, causing distances to diverge to $-\infty$.

---

### Q13: In Floyd-Warshall, the space complexity can be reduced from $O(V^2)$ to $O(V)$ by computing one row at a time.
> **Answer: FALSE.**
> **Justification:** In iteration $k$, updating any row $i$ requires reading values from row $k$ ($D[k, j]$). Because row $k$ must be accessible while updating all other rows, the entire $n \times n$ matrix must reside in memory simultaneously. The lower bound for space in Floyd-Warshall is $\Omega(V^2)$.

---

### Q14: Repeated squaring computes the exact matrix sequence $D^{(1)}, D^{(2)}, D^{(3)}, D^{(4)}, \dots$
> **Answer: FALSE.**
> **Justification:** Repeated squaring computes powers of two: $D^{(1)}, D^{(2)}, D^{(4)}, D^{(8)}, \dots$. It skips non-power-of-two intermediate matrices like $D^{(3)}, D^{(5)}$, etc.

---

### Q15: The predecessor matrix $\Pi$ produced by Floyd-Warshall allows reconstructing a shortest path between any pair $(u, v)$ in $O(V)$ time, where $V$ is the number of vertices.
> **Answer: TRUE.**
> **Justification:** Any simple shortest path contains at most $V - 1$ edges. Following predecessor pointers $\pi_{u, v}$ from target $v$ back to source $u$ requires at most $V$ lookups, running in $O(V)$ (or $O(L)$ where $L$ is path length) time.

---

### Q16: If a directed graph is a DAG, APSP can be solved in $O(V^2 + VE)$ time, even if edge weights are negative.
> **Answer: TRUE.**
> **Justification:** A DAG cannot contain cycles (hence no negative cycles). We can topologically sort the graph in $O(V + E)$ time and run DAG-SSSP from each of the $V$ vertices in $O(V + E)$ time, giving total time $V \times O(V + E) = O(V^2 + VE)$.

---

### Q17: In Johnson's algorithm, the auxiliary source vertex $s$ must be connected to every vertex $v \in V$ with directed edges of weight 0, rather than undirected edges.
> **Answer: TRUE.**
> **Justification:** The edges must be directed from $s$ to $v$ ($s \to v$) so that $s$ can reach every vertex in $G'$. If undirected edges were added, edges could be traversed from $v$ back to $s$, potentially creating new negative cycles that did not exist in the original graph $G$.

---

### Q18: In Floyd-Warshall, checking if $D[i, j] < 0$ for any $i, j$ is sufficient to detect a negative-weight cycle.
> **Answer: FALSE.**
> **Justification:** A negative entry $D[i, j] < 0$ simply means the shortest path from $i$ to $j$ has negative total weight, which is completely valid in graphs with negative edges. A negative-weight cycle is indicated **only when a diagonal entry** $D[i, i] < 0$.

---

### Q19: If edge weights are strictly positive, Floyd-Warshall and Johnson's algorithms both solve APSP, but Johnson's algorithm is asymptotically faster on sparse graphs.
> **Answer: TRUE.**
> **Justification:** On sparse graphs ($E = O(V)$), Johnson's algorithm runs in $O(V^2 \log V)$, whereas Floyd-Warshall runs in $\Theta(V^3)$. $O(V^2 \log V)$ is asymptotically strictly faster than $\Theta(V^3)$.

---

### Q20: If we run Floyd-Warshall on a graph with negative cycles, the algorithm enters an infinite loop.
> **Answer: FALSE.**
> **Justification:** Floyd-Warshall consists of three strictly bounded `for` loops running from 1 to $n$. It executes exactly $n^3$ iterations and **always terminates**. It simply produces incorrect or negative diagonal distances reflecting the negative cycles.

---

### Q21: In the pointer matrix $P$ where $P[i, j] = k$ records the intermediate vertex, $P[i, j] = 0$ implies there is no path between $i$ and $j$.
> **Answer: FALSE.**
> **Justification:** $P[i, j] = 0$ means there are **no intermediate vertices** on the shortest path from $i$ to $j$. This occurs when the path consists of a single direct edge $(i, j)$ with $D[i, j] < \infty$, OR when there is no path ($D[i, j] = \infty$). One must check if $D[i, j] < \infty$ to distinguish between a direct edge and the absence of a path.

---

### Q22: The tropical semiring $(\mathbb{R} \cup \{\infty\}, \min, +)$ satisfies all algebraic properties of a field.
> **Answer: FALSE.**
> **Justification:** A field requires every element to have an additive inverse. In the tropical semiring, the "addition" operation is $\min$. For a real number $x$, there is no element $y$ such that $\min(x, y) = \infty$ (the additive identity). Thus, additive inverses do not exist; it is a semiring, not a field.

---

### Q23: The diameter of a disconnected graph is undefined or $\infty$.
> **Answer: TRUE.**
> **Justification:** The diameter is the maximum shortest-path distance over all pairs: $\max_{u, v} \delta(u, v)$. If the graph is disconnected, there exist pairs with $\delta(u, v) = \infty$, making the diameter $\infty$.

---

### Q24: In Johnson's algorithm, the potential function values satisfy $h(v) \le 0$ for all $v \in V$.
> **Answer: TRUE.**
> **Justification:** For each $v \in V$, there is a direct edge $(s, v)$ of weight 0 from the auxiliary source $s$. Thus, the shortest path distance $\delta(s, v)$ can never exceed the weight of the direct edge: $h(v) = \delta(s, v) \le \omega(s, v) = 0$.

---

### Q25: Floyd-Warshall can be parallelized on a GPU with $O(n)$ parallel time using $n^2$ processors.
> **Answer: TRUE.**
> **Justification:** Because the $k$-th iteration performs $n^2$ mutually independent updates across all pairs $(i, j)$, each iteration $k$ can execute in $O(1)$ parallel time using $n^2$ processing cores. Across $n$ sequential iterations of $k$, the total parallel execution time is $O(n)$.

---

## Final Synthesis Checklist for Exams & Interviews

When analyzing an APSP problem, immediately walk through this decision tree:
1. **Graph unweighted?**
   $\implies$ Use **BFS $\times V$** in $O(V^2 + VE)$ time.
2. **Graph a DAG?**
   $\implies$ Use **Topological Sort + SSSP $\times V$** in $O(V^2 + VE)$ time.
3. **Graph has non-negative edge weights ($\omega \ge 0$)?**
   - Sparse ($E \ll V^2$): **Dijkstra $\times V$ with Binary Heap** in $O(VE \log V)$.
   - Dense ($E \approx V^2$): **Floyd-Warshall** in $\Theta(V^3)$ (best practical cache performance) or **Dijkstra $\times V$ with Fibonacci Heap** in $O(V^3)$.
4. **Graph has arbitrary edge weights (positive and negative)?**
   - Sparse ($E \ll V^2$): **Johnson's Algorithm** in $O(V^2 \log V + VE)$.
   - Dense ($E \approx V^2$): **Floyd-Warshall** in $\Theta(V^3)$.
5. **Need reachability / boolean connectivity only?**
   $\implies$ **Bitset-accelerated Warshall's Algorithm** in $O(V^3 / 64)$ time.
6. **Detecting negative cycles?**
   $\implies$ Check if any **diagonal entry** $D[i, i] < 0$ in Floyd-Warshall, or check if initial **Bellman-Ford** fails in Johnson's algorithm.
7. **Negative weight in undirected graph?**
   $\implies$ Instant negative 2-cycle; shortest paths are undefined ($-\infty$).

