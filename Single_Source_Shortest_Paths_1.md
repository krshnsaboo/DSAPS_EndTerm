# Single-Source Shortest Paths I: Bellman-Ford, DAGs & Generic Relaxation
## Advanced Algorithmic Reasoning, Failure Analyses & Exam Slip-Test Deep-Dives

---

## Table of Contents
1. [Core Foundations & Shortest-Path Theory](#1-core-foundations--shortest-path-theory)
   - [Problem Formulation & Mathematical Modeling](#problem-formulation--mathematical-modeling)
   - [Optimal Substructure: Cut-and-Paste Proof](#optimal-substructure-cut-and-paste-proof)
   - [Negative Edge Weights vs. Negative Cycles ($-\infty$ Divergence)](#negative-edge-weights-vs-negative-cycles--\infty-divergence)
   - [The Triangle Inequality & Upper-Bound Invariants](#the-triangle-inequality--upper-bound-invariants)
2. [Why Relaxation Steps Work & Why Flawed Algorithms Fail (Slide 8 Showdown)](#2-why-relaxation-steps-work--why-flawed-algorithms-fail-slide-8-showdown)
   - [The Core Invariant of `Relax(u, v, w)`](#the-core-invariant-of-relaxu-v-w)
   - [Student A (Arbitrary Vertex Scan): Why It Fails with Stale Parents](#student-a-arbitrary-vertex-scan-why-it-fails-with-stale-parents)
   - [Student B (Topological Sort): Why It is 100% Correct in $\Theta(V + E)$](#student-b-topological-sort-why-it-is-100-correct-in-\thetav--e)
   - [Student C (BFS Queue with Unvisited Guard): Why It Fails on Multi-Edge Paths](#student-c-bfs-queue-with-unvisited-guard-why-it-fails-on-multi-edge-paths)
3. [The Bellman-Ford Algorithm: Invariants & Convergence](#3-the-bellman-ford-algorithm-invariants--convergence)
   - [Algorithmic Principle & Complete Pseudocode](#algorithmic-principle--complete-pseudocode)
   - [Inductive Proof of Convergence in $|V| - 1$ Passes](#inductive-proof-of-convergence-in-v---1-passes)
   - [Negative-Weight Cycle Detection Mechanism & Cycle Sum Invariant](#negative-weight-cycle-detection-mechanism--cycle-sum-invariant)
4. [Exam Deep-Dive: Edge Orders & Tree Discrepancies (ClassPractice Questions 5 & 15)](#4-exam-deep-dive-edge-orders--tree-discrepancies-classpractice-questions-5--15)
   - [Can Bellman-Ford and Dijkstra Produce Different Shortest-Path Trees? (Question 5a)](#can-bellman-ford-and-dijkstra-produce-different-shortest-path-trees-question-5a)
   - [Why Permuting Edge Relaxation Order Preserves Correctness (Question 15a)](#why-permuting-edge-relaxation-order-preserves-correctness-question-15a)
   - [Can Bellman-Ford Converge in Exactly One Iteration? (The Lucky Permutation)](#can-bellman-ford-converge-in-exactly-one-iteration-the-lucky-permutation)
   - [Worst-Case Iteration Scenarios & Pathological Chains](#worst-case-iteration-scenarios--pathological-chains)
   - [Early Termination Optimization via Change Detection](#early-termination-optimization-via-change-detection)
5. [Shortest Paths and Longest Paths in DAGs](#5-shortest-paths-and-longest-paths-in-dags)
   - [DAG-Shortest-Paths in $\Theta(V + E)$](#dag-shortest-paths-in-\thetav--e)
   - [Longest Path in a DAG (Critical Path Method & Project Scheduling)](#longest-path-in-a-dag-critical-path-method--project-scheduling)
6. [Exhaustive Conceptual True / False Question Bank (With Deep Justifications)](#6-exhaustive-conceptual-true--false-question-bank-with-deep-justifications)

---

# 1. Core Foundations & Shortest-Path Theory

## Problem Formulation & Mathematical Modeling
Let $G = (V, E)$ be a directed graph with weight function $\omega: E \to \mathbb{R}$.
- Path weight: $\omega(p) = \sum_{i=1}^k \omega(v_{i-1}, v_i)$.
- Shortest-path distance:
  $$\delta(s, v) = \begin{cases} 
  \min \{\omega(p) : s \xrightarrow{p} v\} & \text{if } v \text{ is reachable from } s \\ 
  \infty & \text{if } v \text{ is unreachable} \\ 
  -\infty & \text{if a path from } s \text{ to } v \text{ traverses a negative cycle} 
  \end{cases}$$

---

## Optimal Substructure: Cut-and-Paste Proof
> **Theorem:** Any subpath of a shortest path is itself a shortest path.

*Proof:* Let $p = s \xrightarrow{p_{1}} u \xrightarrow{p_{2}} v \xrightarrow{p_{3}} w$ be a shortest path. If $p_2$ were not a shortest path from $u$ to $v$, there would exist a strictly shorter path $p'_2$ from $u$ to $v$. Replacing $p_2$ with $p'_2$ would yield a total path weight $\omega(p_1) + \omega(p'_2) + \omega(p_3) < \omega(p)$, contradicting the optimality of $p$. $\blacksquare$

---

## Negative Edge Weights vs. Negative Cycles ($-\infty$ Divergence)
- **Negative Edge Weights Alone:** If no negative cycles exist, all shortest paths are simple paths with at most $|V| - 1$ edges.
- **Negative-Weight Cycles:** If cycle $C$ has $\sum_{e \in C} \omega(e) < 0$ and is reachable from $s$, traversing $C$ repeatedly drives the path weight to $-\infty$. Shortest paths do not exist.

---

## The Triangle Inequality & Upper-Bound Invariants
1. **Triangle Inequality (Lemma 24.10):**
   $$\delta(s, v) \le \delta(s, u) + \omega(u, v) \quad \forall (u, v) \in E$$
2. **Upper-Bound Property (Lemma 24.11):**
   $$d[v] \ge \delta(s, v) \quad \forall v \in V \text{ at all times}$$
   Once $d[v] = \delta(s, v)$, its value never changes.

---

# 2. Why Relaxation Steps Work & Why Flawed Algorithms Fail (Slide 8 Showdown)

## The Core Invariant of `Relax(u, v, w)`
```text
RELAX(u, v, w):
    if d[v] > d[u] + w(u, v) then
        d[v] = d[u] + w(u, v)
        π[v] = u
```
- **Convergence Property:** If $s \leadsto u \to v$ is a shortest path and $d[u] = \delta(s, u)$, relaxing edge $(u, v)$ guarantees that $d[v] = \delta(s, v)$.

---

## Student A (Arbitrary Vertex Scan): Why It Fails with Stale Parents

```text
Student A:
    for each u in V[G] with d[u] != ∞ do
        for each v in Adj[u] do
            Relax(u, v, w)
```
> **VERDICT: FAILS COMPLETELY.**
> In an arbitrary vertex array iteration, child vertices may be processed before their parents have finalized their distances. If node $B$ appears before node $A$ in the array, $B$ relaxes its outgoing edges using a stale, temporary $d[B]$. When $A$ later relaxes edge $(A, B)$ and lowers $d[B]$, $B$'s children are **never revisited**, leaving them with stale, incorrect distances!

---

## Student B (Topological Sort): Why It is 100% Correct in $\Theta(V + E)$

```text
Student B:
    topologically sort G
    Initialize(G, s)
    for each u in V[G] in topological order do
        for each v in Adj[u] do
            Relax(u, v, w)
```
> **VERDICT: 100% CORRECT.**
> In a topological sort, all edges point strictly forward. By the time vertex $u$ is processed, **every single incoming path to $u$ has already been relaxed**. Therefore, $d[u] = \delta(s, u)$ is mathematically guaranteed before $u$ relaxes its outgoing edges! Runs in optimal $\Theta(V + E)$ time.

---

## Student C (BFS Queue with Unvisited Guard): Why It Fails on Multi-Edge Paths

```text
Student C:
    Initialize(G, s); Add s to Queue Q
    while Q not empty do
        n = Dequeue(Q)
        for all neighbors v of n do
            Relax(n, v, w)
            if v unvisited then Add v to Q; mark v visited
```
> **VERDICT: FAILS COMPLETELY.**
> Student C uses a BFS queue and marks nodes as "visited". If vertex $v$ has a direct 1-edge path of weight 10 and an alternative 3-edge path of weight 2, the 1-edge path reaches $v$ first, marks $v$ as visited, and prevents $v$ from ever being re-enqueued when the true lower-weight path arrives!

---

# 3. The Bellman-Ford Algorithm: Invariants & Convergence

```text
BELLMAN-FORD(G, w, s):
    INITIALIZE-SINGLE-SOURCE(G, s)
    for i = 1 to |G.V| - 1 do
        for each (u, v) ∈ G.E do
            RELAX(u, v, w)
            
    for each (u, v) ∈ G.E do
        if d[v] > d[u] + w(u, v) then
            return FALSE // Negative cycle detected!
    return TRUE
```

---

## Inductive Proof of Convergence in $|V| - 1$ Passes

> **Theorem:** After $i$ passes of Bellman-Ford, if the shortest path from $s$ to $v$ contains $i$ edges, then $d[v] = \delta(s, v)$.

### Proof by Mathematical Induction:
- **Base Case ($i = 0$):** $d[s] = 0 = \delta(s, s)$. Holds.
- **Inductive Step:** Let $p = \langle v_0, v_1, \dots, v_i \rangle$ be a shortest path with $i$ edges ($v_0 = s$).
  By induction, after $i - 1$ passes, $d[v_{i-1}] = \delta(s, v_{i-1})$.
  In pass $i$, edge $(v_{i-1}, v_i)$ is relaxed. By the Convergence Property:
  $$d[v_i] = \delta(s, v_i)$$
Because any simple shortest path has at most $|V| - 1$ edges, all reachable vertices achieve their true shortest distances within $|V| - 1$ passes. $\blacksquare$

---

## Negative-Weight Cycle Detection Mechanism & Cycle Sum Invariant

Suppose $G$ contains a reachable negative cycle $C = \langle x_0, x_1, \dots, x_k = x_0 \rangle$ with $\sum \omega < 0$.
If Bellman-Ford did not detect it, the triangle inequality would hold for every edge on $C$:
$$d[x_i] \le d[x_{i-1}] + \omega(x_{i-1}, x_i)$$
Summing around the cycle:
$$\sum_{i=1}^k d[x_i] \le \sum_{i=1}^k d[x_{i-1}] + \sum_{i=1}^k \omega(x_{i-1}, x_i)$$
Since the vertices are identical, the distance sums cancel out:
$$0 \le \sum_{i=1}^k \omega(x_{i-1}, x_i) < 0$$
Contradiction ($0 \le \text{negative number}$)! Thus, at least one edge on the cycle **must fail** the check on pass $|V|$, correctly reporting `FALSE`. $\blacksquare$

---

# 4. Exam Deep-Dive: Edge Orders & Tree Discrepancies (ClassPractice Questions 5 & 15)

## Can Bellman-Ford and Dijkstra Produce Different Shortest-Path Trees? (Question 5a)

**Exam Question (ClassPractice Question 5a):**
*Given a graph $G = (V, E)$ with positive edge weights, the Bellman-Ford algorithm and Dijkstra's algorithm can produce different shortest-path trees despite always producing the same shortest-path weights. True or False?*

> **Answer: TRUE.**

### Concrete Demonstration:
Consider a diamond graph:
- Source: $s$. Destination: $t$.
- Path 1: $s \to A \to t$, weights $(s, A) = 2, (A, t) = 3$. Total weight = 5.
- Path 2: $s \to B \to t$, weights $(s, B) = 3, (B, t) = 2$. Total weight = 5.

```
          (A)
         /   \
       2/     \3
       ▼       ▼
     (s)       (t)
       \       ▲
       3\     /2
         ▼   /
          (B)
```

- Both paths have identical shortest weight $\delta(s, t) = 5$.
- **Dijkstra:** Extracts $A$ first ($d[A] = 2 < d[B] = 3$). $A$ relaxes $t$, setting $\pi[t] = A$. When $B$ is later extracted, $d[B] + \omega(B, t) = 3 + 2 = 5$, which is NOT strictly smaller than $d[t] = 5$. Predecessor remains $\pi[t] = A$.
- **Bellman-Ford:** If the edge list lists edge $(B, t)$ after $(A, t)$, it may update $\pi[t] = B$ depending on whether $\le$ or $<$ is used, or if edges are listed in reverse order: $(s, B), (B, t), (s, A), (A, t) \implies \pi[t] = B$.
- **Conclusion:** Both algorithms compute identical shortest distances ($d[t] = 5$), but their predecessor trees $G_\pi$ can be structurally distinct!

---

## Why Permuting Edge Relaxation Order Preserves Correctness (Question 15a)

**Exam Question (ClassPractice Question 15a):**
*Changing the ordering in which edges are relaxed in the Bellman-Ford algorithm, never changes the correctness of the algorithm. True or False?*

> **Answer: TRUE.**

### Rigorous Justification:
The proof of convergence relies strictly on the **Path-Relaxation Property**: as long as every edge in $E$ is relaxed at least once during each outer pass, the $i$-th edge along any simple shortest path is guaranteed to be relaxed during pass $i$ (or earlier). Changing the intra-pass edge ordering or permuting the list between passes does not violate this guarantee.

---

## Can Bellman-Ford Converge in Exactly One Iteration? (The Lucky Permutation)

> **Answer: YES!**

If edges in $E$ are ordered according to the **topological order of the shortest-path tree**, all edges along every shortest path are relaxed in sequential order in a **single pass**:
- Edge $(s, v_1)$ is relaxed $\implies d[v_1] = \delta(s, v_1)$.
- Later in the same list, edge $(v_1, v_2)$ is relaxed $\implies d[v_2] = \delta(s, v_2)$.
- The algorithm converges in iteration 1!

---

## Worst-Case Iteration Scenarios & Pathological Chains
In a linear chain $s \to v_1 \to v_2 \to \dots \to v_{n-1}$ where edges are stored in reverse order:
$$E = [(v_{n-2}, v_{n-1}), \dots, (s, v_1)]$$
Each pass propagates the distance by only 1 edge. It takes all $|V| - 1$ passes to reach $v_{n-1}$.

---

## Early Termination Optimization via Change Detection
If a pass of $|E|$ relaxations finishes without modifying any distance value, the algorithm terminates immediately, saving unnecessary iterations:

```text
if not updated then return TRUE // Converged early!
```

---

# 5. Shortest Paths and Longest Paths in DAGs

## DAG-Shortest-Paths in $\Theta(V + E)$
Runs in linear time for arbitrary edge weights because cycles (and negative cycles) cannot exist.

## Longest Path in a DAG (Critical Path Method & Project Scheduling)
Replace $\min$ with $\max$ during relaxation along topological order:
$$\text{dist}[v] = \max_{(u, v) \in E} \{\text{dist}[u] + \omega(u, v)\}$$
Runs in $\Theta(V + E)$ time.

---

# 6. Exhaustive Conceptual True / False Question Bank (With Deep Justifications)

---

### Q1: Bellman-Ford and Dijkstra can produce different shortest-path trees on graphs with positive weights.
> **Answer: TRUE.**
> **Justification:** Multiple shortest paths of equal length allow tie-breaking differences to select different predecessor parents.

---

### Q2: Changing the edge permutation per iteration in Bellman-Ford can result in incorrect shortest path weights.
> **Answer: FALSE.**
> **Justification:** Every edge is relaxed in each pass, guaranteeing the Path-Relaxation Property regardless of permutation.

---

### Q3: In a DAG with negative edges, the DAG-Shortest-Paths algorithm can enter an infinite loop.
> **Answer: FALSE.**
> **Justification:** A DAG contains no cycles; topological relaxation is strictly linear and terminates in $\Theta(V + E)$ time.

---

### Q4: If Bellman-Ford converges in 1 iteration, the graph must be a DAG.
> **Answer: FALSE.**
> **Justification:** The graph can contain cycles, but if the edge list order aligns with the shortest-path tree, it converges in pass 1.

---

### Q5: Finding the longest simple path in a general directed graph is NP-hard, but in a DAG it is solvable in $O(V + E)$ time.
> **Answer: TRUE.**
> **Justification:** Acyclicity allows dynamic programming along topological order to compute longest paths in linear time.
