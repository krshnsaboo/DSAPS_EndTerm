# Mastering Johnson's Algorithm: The Intuitive & Reasoning Guide

> **Target Audience:** Anyone preparing for conceptual, reasoning, and interview/exam questions on All-Pairs Shortest Paths (such as IIIT-H DSAPS, GATE, and advanced algorithms exams).  
> **Prerequisites:** Basic knowledge of what a graph is, and knowing that Dijkstra finds shortest paths quickly (but requires non-negative weights) while Bellman-Ford can handle negative weights (but is slower).  
> **Core Focus:** Deep conceptual intuition, mathematical clarity made simple, and mastering "why" things work (and what breaks if you change them). No implementation code needed!

---

## Table of Contents
1. [The Big Picture: What Problem Are We Solving?](#1-the-big-picture-what-problem-are-we-solving)
2. [Why Existing Algorithms Aren't Enough](#2-why-existing-algorithms-arent-enough)
   - [Idea 0: Why not run Dijkstra from every vertex?](#idea-0-why-not-run-dijkstra-from-every-vertex)
   - [Idea 1: Why not run Bellman-Ford from every vertex?](#idea-1-why-not-run-bellman-ford-from-every-vertex)
   - [Idea 2: What about Floyd-Warshall?](#idea-2-what-about-floyd-warshall)
   - [The "Naive Fix" That Fails: Why not just add a big number to all edges?](#the-naive-fix-that-fails-why-not-just-add-a-big-number-to-all-edges)
3. [The Core Genius of Johnson's Algorithm: Vertex Potentials](#3-the-core-genius-of-johnsons-algorithm-vertex-potentials)
   - [The Elevation / Mountain Analogy](#the-elevation--mountain-analogy)
   - [The Telescoping Property (Why Shortest Paths Don't Change)](#the-telescoping-property-why-shortest-paths-dont-change)
4. [Where Does the Magic Potential $h(v)$ Come From?](#4-where-does-the-magic-potential-hv-come-from)
   - [The Secret: The Triangle Inequality](#the-secret-the-triangle-inequality)
5. [The Dummy Source Vertex $s$: Why Is It Essential?](#5-the-dummy-source-vertex-s-why-is-it-essential)
   - [Why do we need a NEW vertex $s$?](#why-do-we-need-a-new-vertex-s)
   - [Why connect $s$ to EVERY vertex with weight 0?](#why-connect-s-to-every-vertex-with-weight-0)
   - [Why must $s$ have in-degree 0?](#why-must-s-have-in-degree-0)
   - [Can edges from $s$ have a weight other than 0?](#can-edges-from-s-have-a-weight-other-than-0)
6. [Step-by-Step Walkthrough with a Concrete Example](#6-step-by-step-walkthrough-with-a-concrete-example)
7. [Complexity Breakdown: Why Johnson's Wins on Sparse Graphs](#7-complexity-breakdown-why-johnsons-wins-on-sparse-graphs)
8. [The Exam Reasoning Question Bank (Variations & Traps)](#8-the-exam-reasoning-question-bank-variations--traps)
   - [Q1: What happens if we run Bellman-Ford from an existing vertex $A$?](#q1-what-happens-if-we-run-bellman-ford-from-an-existing-vertex-a)
   - [Q2: What if we add 0-weight edges from an existing vertex $A$ to unreachable vertices?](#q2-what-if-we-add-0-weight-edges-from-an-existing-vertex-a-to-unreachable-vertices)
   - [Q3: Why can't we add a constant $+C$ to all edges?](#q3-why-cant-we-add-a-constant-c-to-all-edges)
   - [Q4: Could adding the dummy vertex $s$ create new shortest paths between original vertices?](#q4-could-adding-the-dummy-vertex-s-create-new-shortest-paths-between-original-vertices)
   - [Q5: Could adding $s$ create a negative cycle?](#q5-could-adding-s-create-a-negative-cycle)
   - [Q6: What happens if the original graph has a negative cycle?](#q6-what-happens-if-the-original-graph-has-a-negative-cycle)
   - [Q7: What if all edge weights in the original graph are already non-negative?](#q7-what-if-all-edge-weights-in-the-original-graph-are-already-non-negative)
   - [Q8: Can Johnson's algorithm handle undirected graphs with negative edges?](#q8-can-johnsons-algorithm-handle-undirected-graphs-with-negative-edges)
   - [Q9: Does reweighting change which paths are shortest? What about the actual distance?](#q9-does-reweighting-change-which-paths-are-shortest-what-about-the-actual-distance)
9. [Summary Checklist for Exams](#9-summary-checklist-for-exams)

---

## 1. The Big Picture: What Problem Are We Solving?

We want to solve the **All-Pairs Shortest Path (APSP)** problem:
> Given a directed graph $G = (V, E)$ with real-valued edge weights (which may be **positive, zero, or negative**), find the shortest distance between **every pair of vertices** $(u, v)$.

If the graph contains a **negative-weight cycle** (a cycle whose edge weights sum to a negative number), shortest paths are undefined because traversing the cycle infinitely gives a distance of $-\infty$. The algorithm should detect this and stop.

---

## 2. Why Existing Algorithms Aren't Enough

Let's look at the standard shortest-path algorithms we already know and see why none of them are fully satisfying on their own:

| Algorithm | Type | Handles Negative Edges? | Time Complexity (Single Run) | Time Complexity for All-Pairs |
|:---|:---:|:---:|:---:|:---:|
| **Dijkstra** | Single-Source | ❌ **NO** | $O(E + V \log V)$ (fast!) | $O(V \cdot E + V^2 \log V)$ |
| **Bellman-Ford** | Single-Source | ✔️ **YES** | $O(V \cdot E)$ (slower) | $O(V^2 \cdot E)$ |
| **Floyd-Warshall** | All-Pairs | ✔️ **YES** | $O(V^3)$ | $O(V^3)$ |

### Idea 0: Why not run Dijkstra from every vertex?
Dijkstra's algorithm is remarkably fast! If we could run Dijkstra from every single vertex, the total time would be:
$$V \times O(E + V \log V) = O(V \cdot E + V^2 \log V)$$
On a **sparse graph** (where $E \approx V$), this is roughly $O(V^2 \log V)$, which is **much faster** than $O(V^3)$.

**The fatal blocker:** Dijkstra's algorithm fundamentally **fails** if even a single edge has a negative weight. Dijkstra is greedy: once it finalizes a vertex, it assumes that distance can never improve. A negative edge later on breaks this assumption completely.

---

### Idea 1: Why not run Bellman-Ford from every vertex?
Bellman-Ford handles negative edges gracefully. So why not just run Bellman-Ford $|V|$ times (once starting from each vertex)?
- Total time: $|V| \times O(V \cdot E) = O(V^2 \cdot E)$.
- On a dense graph ($E \approx V^2$), this is $O(V^4)$, which is terrible.
- Even on a sparse graph ($E \approx V$), this is $O(V^3)$, which is sluggish compared to Dijkstra.

---

### Idea 2: What about Floyd-Warshall?
Floyd-Warshall is simple and elegant ($3$ nested loops), running in $O(V^3)$ time and handling negative edges.
- On **dense graphs** ($E \approx V^2$), $O(V^3)$ is great.
- But on **sparse graphs** (e.g., planar graphs, road networks, trees, web graphs where $E = O(V)$), Floyd-Warshall still grinds through all $V^3$ operations! We want something that scales with $E$.

---

### The "Naive Fix" That Fails: Why not just add a big number to all edges?
This is the **#1 trap question** asked in exams!

> **Student's Idea:**  
> *"Find the most negative edge in the graph, say $-5$. Add $+5$ (or $+6$) to every single edge in the graph! Now all edges are $\ge 0$, and we can just run Dijkstra from every vertex!"*

#### Why does this FAIL completely?
Because **different paths between the same two vertices can have different numbers of edges!**

When you add $+C$ to every edge:
- A path with $1$ edge increases in weight by $1 \times C$.
- A path with $3$ edges increases in weight by $3 \times C$.

Adding a constant heavily **penalizes paths with more edges**, even if they were originally much shorter!

```
       --- (weight 5) ---> 
   u                         v
       - (1) -> x - (1) -> y - (1) -> 
```

- **Original weights:**
  - Direct path $u \to v$: 1 edge, weight $= 5$.
  - Path $u \to x \to y \to v$: 3 edges, weight $= 1 + 1 + 1 = 3$.
  - **Shortest path:** $u \to x \to y \to v$ (cost 3 vs 5).
- **Now add $+5$ to every edge:**
  - Direct path $u \to v$: new weight $= 5 + 5 = 10$.
  - 3-edge path: new weight $= (1+5) + (1+5) + (1+5) = 6 + 6 + 6 = 18$.
  - **Shortest path has switched to the direct edge!** (cost 10 vs 18).

Adding a constant to all edges **distorts the shortest paths**. It is completely wrong!

---

## 3. The Core Genius of Johnson's Algorithm: Vertex Potentials

Johnson's breakthrough idea was:
> *"Can we change edge weights so that every edge becomes non-negative ($\ge 0$), BUT in such a way that the shortest path between ANY two vertices remains EXACTLY the same?"*

Yes, we can! And the method comes from physics: **potential energy**.

---

### The Elevation / Mountain Analogy
Imagine each vertex $v$ in the graph is a city at some altitude (elevation) $h(v)$ above sea level.

When you walk along an edge from city $u$ to city $v$:
- The original effort (cost) of the road is $w(u, v)$.
- Now, define the **reweighted cost** $\hat{w}(u, v)$ as:
$$\hat{w}(u, v) = w(u, v) + h(u) - h(v)$$

Notice what this formula means:
- You add your starting altitude $h(u)$.
- You subtract your ending altitude $h(v)$.

Now consider traveling along an entire path of cities: $v_0 \to v_1 \to v_2 \to \dots \to v_k$.

What is the total reweighted cost of this path?
$$\hat{w}(p) = \hat{w}(v_0, v_1) + \hat{w}(v_1, v_2) + \dots + \hat{w}(v_{k-1}, v_k)$$

Substitute the formula for each edge:
$$\hat{w}(p) = \Big(w(v_0, v_1) + h(v_0) - h(v_1)\Big) + \Big(w(v_1, v_2) + h(v_1) - h(v_2)\Big) + \dots + \Big(w(v_{k-1}, v_k) + h(v_{k-1}) - h(v_k)\Big)$$

Look at the middle terms:
- $- h(v_1) + h(v_1) = 0$
- $- h(v_2) + h(v_2) = 0$
- ... every intermediate altitude cancels out!

This is called a **telescoping sum**. What remains at the end?
$$\hat{w}(p) = w(p) + h(v_0) - h(v_k)$$

---

### The Telescoping Property (Why Shortest Paths Don't Change)

Look at that magnificent result:
$$\hat{w}(p) = w(p) + h(\text{start}) - h(\text{end})$$

Think carefully about what this means:
- Consider **any two paths** $p_1$ and $p_2$ that both start at $A$ and end at $B$.
- Path $p_1$ has reweighted cost: $w(p_1) + h(A) - h(B)$.
- Path $p_2$ has reweighted cost: $w(p_2) + h(A) - h(B)$.
- The quantity $\big(h(A) - h(B)\big)$ is **IDENTICAL for both paths!**
- It does **NOT** depend on how many edges the path has!
- It does **NOT** depend on which intermediate vertices the path visits!
- It depends **ONLY** on the fixed endpoints $A$ and $B$.

Therefore:
$$w(p_1) \le w(p_2) \iff \hat{w}(p_1) \le \hat{w}(p_2)$$

> **Golden Rule of Reweighting:**  
> A path $p$ from $u$ to $v$ is a shortest path under the original weights $w$ **if and only if** it is a shortest path under the reweighted weights $\hat{w}$.  
> **Reweighting does NOT change which path is the shortest!**

Once we find the shortest reweighted distance $\hat{\delta}(u, v)$ using Dijkstra, we can recover the true original distance with simple algebra:
$$\delta(u, v) = \hat{\delta}(u, v) - h(u) + h(v)$$

---

## 4. Where Does the Magic Potential $h(v)$ Come From?

We have established that *any* potential function $h$ preserves shortest paths.  
However, we also need one more crucial condition:

> We need **all reweighted edges to be non-negative**:
> $$\hat{w}(u, v) \ge 0 \quad \text{for ALL edges } (u, v) \in E$$

Let's write down what that requires:
$$w(u, v) + h(u) - h(v) \ge 0$$
Rearranging terms:
$$h(v) \le h(u) + w(u, v)$$

---

### The Secret: The Triangle Inequality
Look closely at that inequality:
$$h(v) \le h(u) + w(u, v)$$

Where have you seen this before in computer science?  
**In shortest-path theory! This is the Triangle Inequality!**

If $h(x)$ is the shortest-path distance $\delta(s, x)$ from some source vertex $s$ to vertex $x$, then by definition:
$$\delta(s, v) \le \delta(s, u) + w(u, v)$$
*(The shortest path from $s$ to $v$ cannot be longer than the shortest path from $s$ to $u$ plus the direct edge from $u$ to $v$.)*

This is extraordinary:
If we can set:
$$h(v) = \delta(s, v)$$
then the triangle inequality **automatically guarantees** that $h(v) \le h(u) + w(u, v)$, which means:
$$\hat{w}(u, v) = w(u, v) + h(u) - h(v) \ge 0 \quad \text{for EVERY edge!}$$

---

## 5. The Dummy Source Vertex $s$: Why Is It Essential?

Now comes the most frequently examined part of Johnson's algorithm:
To compute $h(v) = \delta(s, v)$ for all vertices, where should the source $s$ come from?

Can we just pick an existing vertex $A \in V$? **NO!**  
Johnson's algorithm adds a **brand-new dummy vertex** $s \notin V$.

Let's understand every design decision behind vertex $s$:

```
        (s) [New Dummy Vertex]
       / | \
    0 /  |0 \ 0
     v   v   v
    (u) (v) (w)  <-- Original Graph G
```

### Why do we need a NEW vertex $s$?
If you choose an existing vertex $A \in V$:
1. $A$ might not be able to reach every vertex in a directed graph! For any unreachable vertex $x$, $\delta(A, x) = +\infty$. As soon as you have $\infty$ in your potentials, the formula $h(u) - h(v)$ yields undefined expressions like $\infty - \infty$.
2. If $A$ cannot reach some part of the graph, and that part contains a negative edge $(x, y)$, Bellman-Ford from $A$ will never relax $(x, y)$. That edge will not satisfy the condition, keeping $\hat{w}(x, y) < 0$.
3. If there is a negative cycle in a component unreachable from $A$, Bellman-Ford will **fail to detect it**.

A new vertex $s$ is guaranteed to reach **every single vertex** because we explicitly draw an edge from $s$ to every vertex.

---

### Why connect $s$ to EVERY vertex with weight 0?
1. By adding directed edges $(s, v)$ of weight 0 to every $v \in V$, every vertex $v$ is reachable from $s$ via a path of length at most 0.
2. Therefore, $\delta(s, v) \le 0$ (it is a finite real number, not $+\infty$).
3. Because $s$ has a path to every vertex, **every edge $(u, v) \in E$ is examined and relaxed** by Bellman-Ford.
4. If there is a negative cycle **anywhere** in the graph, $s$ can reach it (via the edge from $s$ to any node in that cycle). Bellman-Ford is guaranteed to detect it!

---

### Why must $s$ have in-degree 0?
Look at the edges we added: they are directed **from $s$ to $v$**.  
There are **NO edges entering $s$** ($\text{in-degree}(s) = 0$).

Why is this critical?
- Because $s$ has no incoming edges, $s$ **cannot be part of any cycle**!
- Therefore, adding vertex $s$ and its edges can **never create a new cycle**, and can **never create a false negative cycle**.
- The set of cycles in the augmented graph $G'$ is **100% identical** to the set of cycles in the original graph $G$.

---

### Can edges from $s$ have a weight other than 0?
Yes! Suppose instead of weight 0, we connected $s$ to every vertex with weight $C = 5$.
- Then every distance $\delta(s, v)$ would simply increase by 5: $h'(v) = h(v) + 5$.
- Now calculate the reweighted edge:
  $$\hat{w}'(u, v) = w(u, v) + h'(u) - h'(v) = w(u, v) + (h(u) + 5) - (h(v) + 5) = w(u, v) + h(u) - h(v)$$
- The constant $+5$ completely cancels out!
- We use $0$ simply because it is the cleanest, simplest choice and ensures $h(v) \le 0$.

---

## 6. Step-by-Step Walkthrough with a Concrete Example

Let's walk through an actual example from start to finish.

### The Original Graph $G$:
Vertices: $\{A, B, C\}$  
Edges:
- $(A, B)$ with weight $-2$  (negative edge!)
- $(B, C)$ with weight $4$
- $(A, C)$ with weight $3$

```
      -2
(A) ------> (B)
 \           |
  \ 3        | 4
   \         v
    ------> (C)
```

Notice:
- No negative cycles exist.
- But edge $(A, B)$ is negative ($-2$), so standard Dijkstra fails from vertex $A$.

---

### Step 1: Add dummy vertex $s$ with 0-weight edges
Add $s$ with edges:
- $(s, A) = 0$
- $(s, B) = 0$
- $(s, C) = 0$

---

### Step 2: Run Bellman-Ford from $s$ to find potentials $h(v)$
- Initialize distances: $\text{dist}[s] = 0, \text{dist}[A] = 0, \text{dist}[B] = 0, \text{dist}[C] = 0$.
- **Relax edges:**
  - Relax $(A, B)$ of weight $-2$:  
    $\text{dist}[B] = \min(0, \text{dist}[A] + (-2)) = \min(0, 0 - 2) = -2$.
  - Relax $(A, C)$ of weight $3$:  
    $\text{dist}[C] = \min(0, \text{dist}[A] + 3) = \min(0, 3) = 0$.
  - Relax $(B, C)$ of weight $4$:  
    $\text{dist}[C] = \min(0, \text{dist}[B] + 4) = \min(0, -2 + 4) = \min(0, 2) = 0$.
- No more changes occur. No negative cycles detected.

**Our potentials $h(v)$ are:**
$$h(A) = 0, \quad h(B) = -2, \quad h(C) = 0$$

---

### Step 3: Reweight all edges: $\hat{w}(u, v) = w(u, v) + h(u) - h(v)$

Let's calculate the new weights:
- **Edge $(A, B)$:**  
  $$\hat{w}(A, B) = w(A, B) + h(A) - h(B) = -2 + 0 - (-2) = \mathbf{0} \ge 0$$
- **Edge $(B, C)$:**  
  $$\hat{w}(B, C) = w(B, C) + h(B) - h(C) = 4 + (-2) - 0 = \mathbf{2} \ge 0$$
- **Edge $(A, C)$:**  
  $$\hat{w}(A, C) = w(A, C) + h(A) - h(C) = 3 + 0 - 0 = \mathbf{3} \ge 0$$

**Every reweighted edge is now non-negative ($\ge 0$)!**  
The negative edge $-2$ has vanished into $0$, without distorting any path relations!

---

### Step 4: Run Dijkstra from every vertex on the reweighted graph
Now that all edge weights are $\ge 0$, we can safely remove $s$ and run Dijkstra from each vertex:

#### Dijkstra from $A$:
- Path $A \to B$: $\hat{\delta}(A, B) = 0$.
- Path $A \to B \to C$: $\hat{\delta}(A, C) = 0 + 2 = 2$ (cheaper than direct edge $A \to C$ which is $3$).

---

### Step 5: Convert reweighted distances back to original distances
Formula: $\delta(u, v) = \hat{\delta}(u, v) - h(u) + h(v)$

- **Distance $A \to B$:**
  $$\delta(A, B) = \hat{\delta}(A, B) - h(A) + h(B) = 0 - 0 + (-2) = \mathbf{-2}$$
  *(Matches original shortest path $A \to B$ of weight $-2$!)*

- **Distance $A \to C$:**
  $$\delta(A, C) = \hat{\delta}(A, C) - h(A) + h(C) = 2 - 0 + 0 = \mathbf{2}$$
  *(Matches original path $A \to B \to C$ of weight $-2 + 4 = 2$!)*

Everything works out with mathematical perfection.

---

## 7. Complexity Breakdown: Why Johnson's Wins on Sparse Graphs

Let $V = |V|$ and $E = |E|$.

1. **Step 1:** Add vertex $s$ and $V$ edges $\implies O(V)$ time.
2. **Step 2:** Run Bellman-Ford once from $s$ on graph with $V+1$ vertices and $E+V$ edges:
   $$\text{Time} = O(V \cdot E)$$
3. **Step 3:** Reweight all $E$ edges $\implies O(E)$ time.
4. **Step 4:** Run Dijkstra $V$ times using a Fibonacci Heap (or binary heap):
   - One Dijkstra run: $O(E + V \log V)$.
   - $V$ runs of Dijkstra:
     $$\text{Time} = O(V \cdot E + V^2 \log V)$$
5. **Step 5:** Convert all $V^2$ distances back $\implies O(V^2)$ time.

### Total Time Complexity:
$$\mathbf{O(V \cdot E + V^2 \log V)}$$

### Comparison with Floyd-Warshall:
- **Floyd-Warshall:** Always takes $\Theta(V^3)$.
- **Johnson's Algorithm:** Takes $O(V \cdot E + V^2 \log V)$.

Look what happens when the graph is **sparse** (i.e., $E = O(V)$):
- Floyd-Warshall: $O(V^3)$
- Johnson's: $O(V \cdot V + V^2 \log V) = \mathbf{O(V^2 \log V)}$

For $V = 10,000$ vertices:
- $V^3 = 1,000,000,000,000$ operations (infeasible!).
- $V^2 \log V \approx 10^8 \times 14 \approx 1.4 \times 10^9$ operations (computable in a few seconds!).

> **Takeaway:**  
> Johnson's algorithm is specifically designed to be the fastest APSP algorithm for **sparse graphs with negative edges**.

---

## 8. The Exam Reasoning Question Bank (Variations & Traps)

Here are the exact types of conceptual and edge-case questions asked in top exams. Master these explanations and you will be able to answer any variation.

---

### Q1: What happens if we run Bellman-Ford from an existing vertex $A$?
*(This was Case 1 of the DSAPS EndSem exam!)*

> **Question:** Can we skip creating a new vertex $s$, and instead simply pick an existing vertex $A \in V$, run Bellman-Ford from $A$, and define $h(x) = \delta(A, x)$?

**Answer:** **NO, this fails in general.**

**Why? (The 3 fatal flaws):**
1. **Unreachability / Undefined Potentials:** If vertex $A$ cannot reach some vertex $x$, then $\delta(A, x) = +\infty$. For an edge $(u, v)$ between two unreachable vertices, the formula $\hat{w}(u, v) = w(u, v) + h(u) - h(v)$ involves $\infty - \infty$, which is mathematically undefined. If you try to set $h(x) = 0$ for unreachable vertices, any negative edge between unreachable vertices will remain negative ($\hat{w}(u, v) = w(u, v) < 0$), causing Dijkstra to fail.
2. **Undetected Negative Cycles:** If there is a negative-weight cycle in a connected component that $A$ cannot reach, Bellman-Ford from $A$ will never explore it and will **fail to detect the negative cycle**.
3. **Triangle Inequality Fails for Unreached Edges:** Bellman-Ford only guarantees $h(v) \le h(u) + w(u, v)$ for vertices reached from $A$. For any unreached edge, the guarantee collapses.

---

### Q2: What if we add 0-weight edges from an existing vertex $A$ to unreachable vertices?
*(This was Case 2 of the DSAPS EndSem exam!)*

> **Question:** Professor Ben proposes: Pick an existing vertex $A$. For every vertex $x$ that $A$ does not have an edge to, add a directed edge $(A, x)$ with weight $0$. Then run Bellman-Ford from $A$ to compute potentials. Does this work?

**Answer:** **NO, this also fails completely.**

**Why? (The False Negative Cycle trap):**
- In standard Johnson's algorithm, the dummy source $s$ is **outside** $V$, so $s$ has **no incoming edges** ($\text{in-degree}(s) = 0$). It can never be part of a cycle.
- But vertex $A$ is an **existing internal vertex**. It may already have incoming edges from other vertices in the graph!
- Suppose in the original graph, there is a path from some vertex $X$ to $A$ with negative weight (e.g., $w(X \to A) = -2$). If there was no edge from $A$ to $X$, the graph had no cycle.
- Following Professor Ben's idea, we add a directed edge $(A, X)$ with weight $0$.
- **Disaster:** We have just created a brand-new cycle:
  $$A \to X \to A \quad \text{with weight } 0 + (-2) = -2 < 0$$
- This is an **artificial negative cycle** created by the modification!
- When Bellman-Ford runs from $A$, it detects this false negative cycle and aborts, reporting that the graph is invalid—even though the original graph was completely valid and had no negative cycles!

**Minimal Counterexample:**
$V = \{A, B\}$, single edge $B \to A$ with weight $-1$.  
The original graph is an acyclic DAG.  
Ben's rule adds $A \to B$ of weight $0$.  
Cycle $A \to B \to A$ has weight $-1$. Bellman-Ford falsely reports a negative cycle!

---

### Q3: Why can't we add a constant $+C$ to all edges?

**Answer:**  
Because different paths between the same two endpoints have different numbers of edges. Adding $+C$ to each edge increases a path's cost by $k \cdot C$, where $k$ is the number of edges. This penalizes longer paths (in terms of edge count) and changes which path is the shortest.  
In contrast, vertex reweighting $\hat{w}(u, v) = w(u, v) + h(u) - h(v)$ telescopes to $w(p) + h(\text{start}) - h(\text{end})$, which adds the **exact same constant** to every path between the same two endpoints, regardless of edge count.

---

### Q4: Could adding the dummy vertex $s$ create new shortest paths between original vertices?

**Answer:** **NO.**  
Because $s$ has **in-degree 0** (no edges enter $s$).  
Any path between two original vertices $u$ and $v$ that passes through $s$ would require an edge entering $s$, which does not exist. Thus, no path between original vertices can ever traverse $s$.

---

### Q5: Could adding $s$ create a negative cycle?

**Answer:** **NO.**  
A cycle requires returning to the starting vertex. Because $s$ has no incoming edges, no cycle can include $s$. The set of cycles in $G \cup \{s\}$ is identical to the set of cycles in $G$.

---

### Q6: What happens if the original graph has a negative cycle?

**Answer:**  
Because $s$ has an edge $(s, v)$ with weight 0 to every vertex $v \in V$, $s$ has a directed path to every component and cycle in the graph.  
When Bellman-Ford runs from $s$, it traverses the negative cycle on its $|V|^{\text{th}}$ iteration and correctly reports that a negative cycle exists. Johnson's algorithm then terminates immediately.

---

### Q7: What if all edge weights in the original graph are already non-negative?

**Answer:**  
- If all edge weights are already $\ge 0$, Johnson's algorithm is unnecessary overhead! You can simply run Dijkstra directly from every vertex without any reweighting.
- However, if you *did* run Johnson's algorithm:
  - Since all edges are $\ge 0$ and edges from $s$ are $0$, the shortest distance from $s$ to any vertex $v$ is $\delta(s, v) = 0$.
  - Thus, $h(v) = 0$ for all $v$.
  - The reweighted edge weights would be $\hat{w}(u, v) = w(u, v) + 0 - 0 = w(u, v)$ (unchanged).
  - The algorithm still works and produces correct results.

---

### Q8: Can Johnson's algorithm handle undirected graphs with negative edges?

**Answer:** **NO.**  
In an undirected graph, an undirected edge $\{u, v\}$ with negative weight $w < 0$ acts as two directed edges $(u, v)$ and $(v, u)$, both with weight $w$.  
Traversing back and forth: $u \to v \to u$ produces a cycle of weight $w + w = 2w < 0$.  
Therefore, **any negative edge in an undirected graph automatically constitutes a negative cycle!** Shortest paths are undefined.

---

### Q9: Does reweighting change which paths are shortest? What about the actual distance?

**Answer:**
- **Which path is shortest:** **UNCHANGED.** The shortest path under $w$ is identically the shortest path under $\hat{w}$.
- **The numerical distance value:** **CHANGED.** The distance under $\hat{w}$ is $\hat{\delta}(u, v) = \delta(u, v) + h(u) - h(v)$.  
  To get the true original distance, we must convert it back:
  $$\delta(u, v) = \hat{\delta}(u, v) - h(u) + h(v)$$

---

## 9. Summary Checklist for Exams

When answering exam questions on Johnson's Algorithm, remember these four pillars:

1. **The Goal:** All-Pairs Shortest Path for sparse graphs with negative edge weights in $O(V \cdot E + V^2 \log V)$ time.
2. **The Telescoping Equation:**  
   $$\hat{w}(p) = w(p) + h(\text{start}) - h(\text{end})$$
   Proves that the ranking of all paths between two vertices is strictly preserved.
3. **The Non-Negativity Condition:**  
   $$\hat{w}(u, v) \ge 0 \iff h(v) \le h(u) + w(u, v)$$
   This is the Triangle Inequality, satisfied by setting $h(v) = \delta(s, v)$.
4. **The Dummy Source $s$ Must:**
   - Be an **external vertex** ($s \notin V$) with **$\text{in-degree}(s) = 0$** (prevents creating false cycles).
   - Have **edges to all vertices** (guarantees finite potentials and catches all negative cycles).
