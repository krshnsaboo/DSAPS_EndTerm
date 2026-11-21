# Mastering Minimum Spanning Trees (MST): The Intuitive, Reasoning & Advanced Exam Guide

> **Target Audience:** Students preparing for challenging university final examinations (such as IIIT-H DSAPS End-Sem), competitive exams (GATE), and advanced algorithms interviews.  
> **Prerequisites:** None assumed! We start from absolute first principles and build up to complex, twisted reasoning problems.  
> **Core Focus:** Deep geometric & structural intuition, mathematical proof techniques (exchange arguments), counterexample construction, and mastering "trick" reasoning questions. No code required!

---

## Table of Contents
1. [The Big Picture: What is a Minimum Spanning Tree?](#1-the-big-picture-what-is-a-minimum-spanning-tree)
   - [What is a Spanning Tree?](#what-is-a-spanning-tree)
   - [What is an MST?](#what-is-an-mst)
   - [Can MSTs Have Negative Edge Weights?](#can-msts-have-negative-edge-weights)
   - [When is the MST Unique?](#when-is-the-mst-unique)
2. [The Twin Pillars: The Cut Property and The Cycle Property](#2-the-twin-pillars-the-cut-property-and-the-cycle-property)
   - [The Fundamental Duality: Cuts vs Cycles](#the-fundamental-duality-cuts-vs-cycles)
   - [Pillar 1: The Cut Property (The Inclusion Rule)](#pillar-1-the-cut-property-the-inclusion-rule)
   - [Pillar 2: The Cycle Property (The Exclusion Rule)](#pillar-2-the-cycle-property-the-exclusion-rule)
   - [Mastering the "Exchange Argument" (Your Universal Proof Weapon)](#mastering-the-exchange-argument-your-universal-proof-weapon)
3. [The Classic Algorithms: Pure Conceptual Mechanics](#3-the-classic-algorithms-pure-conceptual-mechanics)
   - [Kruskal’s Algorithm (Global Greedy)](#kruskals-algorithm-global-greedy)
   - [Prim’s Algorithm (Local Growth)](#prims-algorithm-local-growth)
   - [Borůvka’s Algorithm (Parallel Contraction)](#boruvkas-algorithm-parallel-contraction)
   - [Reverse-Delete Algorithm (Pessimistic Elimination)](#reverse-delete-algorithm-pessimistic-elimination)
   - [Comparison Matrix of MST Algorithms](#comparison-matrix-of-mst-algorithms)
4. [Weight Transformations & Invariance (Exam Favorites)](#4-weight-transformations--invariance-exam-favorites)
   - [Additive Shift: $w'(e) = w(e) + c$](#additive-shift-we--we--c)
   - [Multiplicative Scaling: $w'(e) = c \cdot w(e)$](#multiplicative-scaling-we--c-cdot-we)
   - [Strictly Monotonic Functions: $w'(e) = f(w(e))$](#strictly-monotonic-functions-we--fwe)
   - [The Nonlinear Traps: Squaring, Cubing, and Negatives](#the-nonlinear-traps-squaring-cubing-and-negatives)
5. [Bottleneck Spanning Trees & Structural Variants](#5-bottleneck-spanning-trees--structural-variants)
   - [Minimum Bottleneck Spanning Tree (MBST) vs MST](#minimum-bottleneck-spanning-tree-mbst-vs-mst)
   - [Shortest Path Tree (SPT) vs MST](#shortest-path-tree-spt-vs-mst)
   - [Dynamic Updates: What if an Edge Weight Changes?](#dynamic-updates-what-if-an-edge-weight-changes)
6. [The Advanced & Twisted Exam Question Bank (12 High-Yield Challenges)](#6-the-advanced--twisted-exam-question-bank-12-high-yield-challenges)
   - [Challenge 1: The "Unique Minimum Weight Cycle" Trap](#challenge-1-the-unique-minimum-weight-cycle-trap)
   - [Challenge 2: Kruskal’s Tie-Breaking Completeness](#challenge-2-kruskals-tie-breaking-completeness)
   - [Challenge 3: The Minimum Bottleneck Equivalence Fallacy](#challenge-3-the-minimum-bottleneck-equivalence-fallacy)
   - [Challenge 4: Nonlinear Transformations with Negative Edge Weights](#challenge-4-nonlinear-transformations-with-negative-edge-weights)
   - [Challenge 5: Local Edge Perturbations (Cascade Effects)](#challenge-5-local-edge-perturbations-cascade-effects)
   - [Challenge 6: The Total Incompatibility of SPT and MST](#challenge-6-the-total-incompatibility-of-spt-and-mst)
   - [Challenge 7: The Degree-Constrained Greedy Fallacy (NP-Hardness)](#challenge-7-the-degree-constrained-greedy-fallacy-np-hardness)
   - [Challenge 8: Cycles with Multiple Equal Maximum-Weight Edges](#challenge-8-cycles-with-multiple-equal-maximum-weight-edges)
   - [Challenge 9: The Union of Lightest Edges of All Cuts](#challenge-9-the-union-of-lightest-edges-of-all-cuts)
   - [Challenge 10: Dynamic Repair after Vertex Deletion](#challenge-10-dynamic-repair-after-vertex-deletion)
   - [Challenge 11: The Random Edge Insertion & Cycle-Breaking Algorithm](#challenge-11-the-random-edge-insertion--cycle-breaking-algorithm)
   - [Challenge 12: Independent Percentage Perturbations](#challenge-12-independent-percentage-perturbations)
7. [The 7 Golden Rules for Acing MST Exam Questions](#7-the-7-golden-rules-for-acing-mst-exam-questions)

---

## 1. The Big Picture: What is a Minimum Spanning Tree?

Imagine you are tasked with laying down high-speed fiber-optic cables between $n$ cities. Laying cable between city $u$ and city $v$ costs some amount of money, $w(u, v)$. 

You want to achieve two simple goals:
1. **Connectivity:** Every city must be able to communicate with every other city (directly or indirectly).
2. **Cost Minimization:** You want the **minimum possible total budget**.

You do not need cycles or redundancy. If there is already a path between city $A$ and city $B$, adding another direct link creates a cycle—which wastes money! 

To minimize cost while ensuring connectivity, your network must be a **Tree** that connects (**spans**) all vertices: a **Spanning Tree**.

---

### What is a Spanning Tree?
Let $G = (V, E)$ be a connected, undirected graph with $|V| = n$ vertices and $|E| = m$ edges.  
A **spanning tree** $T = (V, E_T)$ is a subgraph of $G$ such that:
1. $T$ contains **all vertices** of $G$ ($V_T = V$).
2. $T$ is **connected**.
3. $T$ is **acyclic** (contains no cycles).

#### The 4 Golden Mathematical Invariants of a Tree:
If a graph has $n$ vertices, the following statements are completely equivalent:
- $T$ is a tree.
- $T$ is connected and has **exactly $n - 1$ edges**.
- $T$ is acyclic and has **exactly $n - 1$ edges**.
- There is **exactly one simple path** between every pair of vertices in $T$.
- Adding **any new edge** to $T$ creates **exactly one simple cycle**.
- Removing **any edge** from $T$ disconnects it into **exactly two connected components**.

---

### What is an MST?
The **weight of a spanning tree** $T$ is the sum of the weights of its $n - 1$ edges:
$$w(T) = \sum_{e \in T} w(e)$$

A **Minimum Spanning Tree (MST)** is a spanning tree whose total weight is **less than or equal to** the weight of every other spanning tree of $G$:
$$w(T) \le w(T') \quad \forall \text{ spanning trees } T' \text{ of } G$$

---

### Can MSTs Have Negative Edge Weights?
> **Exam Confusion Alert:** Students often confuse Dijkstra with MST algorithms!  
> In Dijkstra, negative edge weights cause disastrous infinite loops or incorrect greedy choices.  
> **Does an MST algorithm break if edges have negative weights?**

**NO! MST algorithms work seamlessly with negative, zero, and positive weights!**

Why?
- Adding a tree edge with negative weight is fantastic: it makes the total tree cost even cheaper!
- Spanning trees are strictly constrained to have **exactly $n - 1$ edges**. You cannot traverse a negative edge infinitely many times to create a $-\infty$ loop because trees are acyclic.
- If you add a constant $+C$ to all edge weights to make them all positive, the weight of *every* spanning tree increases by the exact same amount: $(n - 1) \cdot C$. The tree that was cheapest before remains cheapest now!
- Therefore, **negative weights pose zero issues for Kruskal, Prim, Borůvka, or Reverse-Delete.**

---

### When is the MST Unique?
A graph can have many different MSTs. For example, if all edge weights are $1$, **every** spanning tree is an MST!

- **Sufficient Condition for Uniqueness:**  
  If **every edge in $G$ has a distinct (unique) weight**, then $G$ has a **strictly unique Minimum Spanning Tree**.
- **Is distinct weights a necessary condition?**  
  **NO!** A graph can have identical edge weights and still possess a unique MST.  
  *Example:* A tree graph where some edges have identical weights. Since the graph itself is already a tree, it only has one spanning tree (itself), which is trivially unique!

---

## 2. The Twin Pillars: The Cut Property and The Cycle Property

Virtually every single reasoning, true/false, and proof-based question on MSTs in exams boils down to two mathematical properties: **The Cut Property** and **The Cycle Property**. Master these two, and you master MSTs.

---

### The Fundamental Duality: Cuts vs Cycles

```
        THE CUT PROPERTY                        THE CYCLE PROPERTY
    (Used to INCLUDE edges)                  (Used to EXCLUDE edges)
    
       S            V \ S                         (u)-------(v)
     ( A ) ------ ( C )                         /             \
    /     \  e   /     \                      e1               e2
   ( B )   \----/-- ( D )                       \             /
            cross                                (w)-------(x)
                                                      e_max
   Lightest crossing edge MUST             Heaviest edge on a cycle CANNOT
       belong to the MST.                        belong to the MST.
```

- A **Cut** partitions vertices into two camps. To connect them, an edge *must* cross.
- A **Cycle** provides redundant paths. To avoid cycles, at least one edge *must* be dropped.

---

### Pillar 1: The Cut Property (The Inclusion Rule)

#### What is a Cut?
A **cut** $(S, V \setminus S)$ is simply a partition of the vertices $V$ into two disjoint, non-empty sets $S$ and $V \setminus S$.  
An edge $e = (u, v)$ **crosses the cut** if one endpoint lies in $S$ ($u \in S$) and the other lies in $V \setminus S$ ($v \in V \setminus S$).

#### The Cut Property Statement:
> For **any** cut $(S, V \setminus S)$ of a graph $G$:  
> If an edge $e$ is the **strictly lightest edge crossing the cut**, then $e$ **must belong to every minimum spanning tree** of $G$.  
> *(If multiple crossing edges share the minimum weight, at least one of them belongs to some MST.)*

---

### Pillar 2: The Cycle Property (The Exclusion Rule)

#### What is a Cycle?
A simple cycle $C$ is a closed path $v_1 \to v_2 \to \dots \to v_k \to v_1$ with no repeated vertices (except start and end).

#### The Cycle Property Statement:
> For **any** simple cycle $C$ in a graph $G$:  
> If an edge $e$ is the **strictly heaviest edge on the cycle $C$**, then $e$ **cannot belong to any minimum spanning tree** of $G$.  
> *(If multiple edges on the cycle share the maximum weight, at least one of them can be excluded from an MST.)*

---

### Mastering the "Exchange Argument" (Your Universal Proof Weapon)

In exams, when asked: *"Prove that edge $e$ is in the MST"* or *"Prove that edge $e$ is not in the MST"*, **always use the Exchange Argument**. It never fails.

#### Proof of the Cut Property via Exchange Argument:
1. Let $(S, V \setminus S)$ be a cut, and let $e = (u, v)$ be the strictly lightest edge crossing it.
2. Suppose for contradiction that there is an MST $T$ that **does NOT contain $e$** ($e \notin T$).
3. Since $T$ is a spanning tree, there is a unique simple path $P$ in $T$ connecting $u \in S$ and $v \in V \setminus S$.
4. Because path $P$ starts in $S$ and ends in $V \setminus S$, $P$ must cross the cut at least once. Let $e' \in P$ be an edge that crosses the cut $(S, V \setminus S)$ with $e' \ne e$.
5. Since $e$ is the *strictly lightest* edge crossing this cut:
   $$w(e) < w(e')$$
6. Now, perform the **exchange**: Remove $e'$ from $T$ and add $e$:
   $$T' = (T \setminus \{e'\}) \cup \{e\}$$
7. Because $e'$ was on the path connecting the endpoints of $e$, swapping them maintains connectivity and leaves the number of edges at $n - 1$. Thus, $T'$ is a valid spanning tree!
8. Look at the total weight of $T'$:
   $$w(T') = w(T) - w(e') + w(e) < w(T)$$
9. $w(T') < w(T)$ **contradicts** the assumption that $T$ was a minimum spanning tree!
10. Therefore, no such $T$ can exist. Edge $e$ **must belong to every MST**. $\blacksquare$

#### Proof of the Cycle Property via Exchange Argument:
1. Let $C$ be a cycle, and let $e = (u, v)$ be the strictly heaviest edge in $C$.
2. Suppose for contradiction that there is an MST $T$ that **contains $e$** ($e \in T$).
3. If we remove $e$ from $T$, the tree breaks into two connected components, defining a cut $(S, V \setminus S)$ where $u \in S$ and $v \in V \setminus S$.
4. The remaining edges of cycle $C$, namely $C \setminus \{e\}$, form an alternative path between $u$ and $v$. This path must cross the cut $(S, V \setminus S)$. Let $e' \in C \setminus \{e\}$ be an edge crossing this cut.
5. Since $e$ is the *strictly heaviest* edge in cycle $C$:
   $$w(e') < w(e)$$
6. Perform the **exchange**: Remove $e$ and add $e'$:
   $$T' = (T \setminus \{e\}) \cup \{e'\}$$
7. $T'$ is a valid spanning tree, and its weight is:
   $$w(T') = w(T) - w(e) + w(e') < w(T)$$
8. This contradicts the minimality of $T$! Therefore, $e$ cannot belong to any MST. $\blacksquare$

---

## 3. The Classic Algorithms: Pure Conceptual Mechanics

Every standard MST algorithm is just a different, clever way to apply the Cut Property or the Cycle Property.

```
+-------------------------------------------------------------------------------+
|                        THE SPECTRUM OF MST ALGORITHMS                         |
+-------------------------------------------------------------------------------+
|  ALGORITHM       | PHILOSOPHY            | PRIMARY PROPERTY EXPLOITED         |
+------------------+-----------------------+------------------------------------+
|  Kruskal's       | Global Edge Sorting   | Cut Property (and Cycle avoidance) |
|  Prim's          | Local Tree Expansion  | Pure Cut Property                  |
|  Borůvka's       | Parallel Contraction  | Pure Cut Property (Simultaneous)   |
|  Reverse-Delete  | Global Edge Pruning   | Pure Cycle Property                |
+-------------------------------------------------------------------------------+
```

---

### Kruskal’s Algorithm (Global Greedy)
- **Concept:** Start with an empty forest of $n$ isolated vertices ($0$ edges). Sort all $m$ edges by weight from lightest to heaviest.
- **Rule:** Inspect each edge in increasing order of weight. If adding the edge does not form a cycle, **add it**. If it forms a cycle, **discard it**. Stop when you have $n - 1$ edges.
- **Why it works:** When an edge $e = (u, v)$ is considered and connects two different trees $T_1$ and $T_2$, consider the cut $(T_1, V \setminus T_1)$. Because edges are sorted, $e$ is the lightest remaining edge crossing this cut! By the Cut Property, $e$ safely belongs to the MST.
- **Data Structure:** Uses **Disjoint Set Union (DSU / Union-Find)** to detect cycles in $O(\alpha(V))$ time per edge.
- **Complexity:** $O(E \log E) = O(E \log V)$ (dominated by sorting edges).

---

### Prim’s Algorithm (Local Growth)
- **Concept:** Start at an **arbitrary single vertex** $s$. Maintain a growing tree $T$.
- **Rule:** At every step, look at all edges that cross from the vertices inside $T$ to the vertices outside $T$ (the cut $(V_T, V \setminus V_T)$). Greedily pick the **lightest crossing edge**, add it to $T$, and absorb the new vertex into $T$. Repeat until all vertices are in $T$.
- **Why it works:** At every single step, $(V_T, V \setminus V_T)$ is a valid cut! Prim literally picks the minimum-weight edge crossing this cut. By the Cut Property, that edge is guaranteed to belong to the MST.
- **Independence of Start Vertex:** Does picking a different starting vertex change the MST?
  - If edge weights are distinct: **NO**, Prim will always output the exact same unique MST, regardless of which of the $n$ vertices you start from!
  - If duplicate weights exist, different start vertices might pick different tie-breaking edges, giving different (equally optimal) MSTs.
- **Complexity:** $O(E + V \log V)$ with a Fibonacci heap, or $O(E \log V)$ with a binary heap.

---

### Borůvka’s Algorithm (Parallel Contraction)
- **Concept:** Start with each of the $n$ vertices as its own individual component.
- **Rule:** In each phase, **every component simultaneously** identifies the lightest edge leaving it and marks it. All marked edges are added to the forest. Contract the connected vertices into super-vertices. Repeat until only 1 component remains.
- **Why it works:** For each component $C$, the boundary $(C, V \setminus C)$ forms a cut. The lightest edge leaving $C$ is the lightest crossing edge of that cut, so by the Cut Property, it is guaranteed to be in the MST!
- **Phases:** Each phase reduces the number of components by at least half ($n \to n/2 \to n/4 \dots$). Thus, there are at most $O(\log V)$ phases!
- **Complexity:** $O(E \log V)$. Ideal for parallel computing!

---

### Reverse-Delete Algorithm (Pessimistic Elimination)
- **Concept:** Start with the **entire graph** $T = E$. Sort edges from **heaviest to lightest**.
- **Rule:** For each edge $e$ (from heaviest to lightest), check: *If I remove $e$, does the graph remain connected?*
  - If YES (removing $e$ doesn't disconnect the graph): **Permanently DELETE $e$!**
  - If NO (removing $e$ disconnects the graph): **KEEP $e$!**
- **Why it works:** If removing $e$ leaves the graph connected, $e$ must have been part of a cycle in the remaining edges. Because edges are sorted from heaviest to lightest, $e$ is guaranteed to be the heaviest edge in that cycle. By the Cycle Property, $e$ cannot belong to the MST, so deleting it is optimal!
- **Complexity:** $O(E \log V \cdot (\text{connectivity check})) \approx O(E^2)$ or $O(E \log V (\log \log V)^3)$ with dynamic connectivity.

---

### Comparison Matrix of MST Algorithms

| Feature | Kruskal's | Prim's | Borůvka's | Reverse-Delete |
|:---|:---|:---|:---|:---|
| **Approach** | Edge-centric (Global) | Vertex-centric (Local) | Component-centric (Parallel) | Edge-elimination (Pruning) |
| **Initial State** | Empty forest ($0$ edges) | Single vertex | $V$ isolated trees | Full graph ($E$ edges) |
| **Pillar Used** | Cut Property | Cut Property | Cut Property | Cycle Property |
| **Time Complexity** | $O(E \log V)$ | $O(E + V \log V)$ | $O(E \log V)$ | $O(E^2)$ (naive) |
| **Best Suited For** | Sparse graphs | Dense graphs ($E \approx V^2$) | Parallel implementations | Theoretical proofs & small graphs |

---

## 4. Weight Transformations & Invariance (Exam Favorites)

Exams love to ask: *"If we modify the edge weights by doing $X$, does the MST change?"*  
Here is the mathematical theory behind every possible modification.

---

### Additive Shift: $w'(e) = w(e) + c$
**Verdict:** **The MST NEVER changes.**

**Reason:**  
Every spanning tree of $G$ has **exactly $n - 1$ edges**.  
The weight of any spanning tree $T$ under the new weights is:
$$w'(T) = \sum_{e \in T} (w(e) + c) = \sum_{e \in T} w(e) + (n - 1)c = w(T) + \text{constant}$$

Since every spanning tree increases by the exact same constant $(n - 1)c$, the relative order of all spanning trees is completely preserved:
$$w(T_1) \le w(T_2) \iff w'(T_1) \le w'(T_2)$$
The cheapest tree remains the cheapest tree.

---

### Multiplicative Scaling: $w'(e) = c \cdot w(e)$ where $c > 0$
**Verdict:** **The MST NEVER changes.**

**Reason:**  
$$w'(T) = \sum_{e \in T} c \cdot w(e) = c \sum_{e \in T} w(e) = c \cdot w(T)$$
Multiplying all tree weights by a positive constant $c > 0$ preserves all inequalities:
$$w(T_1) \le w(T_2) \iff c \cdot w(T_1) \le c \cdot w(T_2)$$

*(Warning: If $c < 0$, all inequalities flip, and the MST becomes the **Maximum Spanning Tree**!)*

---

### Strictly Monotonic Functions: $w'(e) = f(w(e))$
> **Theorem:** If $f: \mathbb{R} \to \mathbb{R}$ is a **strictly monotonically increasing function** (meaning $x < y \implies f(x) < f(y)$), then the set of MSTs of $G$ is **completely identical** before and after applying $f$.

**Proof via Kruskal / Cut Property:**  
Look at Kruskal’s algorithm. It only cares about the **sorted relative order** of edges:
$$w(e_1) \le w(e_2) \le \dots \le w(e_m)$$
If $f$ is strictly increasing, applying $f$ produces:
$$f(w(e_1)) \le f(w(e_2)) \le \dots \le f(w(e_m))$$
The sorted order of edges is **100% identical**!  
Since Kruskal inspects edges in the exact same order and makes the exact same cycle decisions, it will produce the **exact same tree**!

---

### The Nonlinear Traps: Squaring, Cubing, and Negatives

| Transformation $f(x)$ | Condition on Edge Weights | Does the MST change? | Explanation |
|:---|:---|:---:|:---|
| **$f(x) = x^3$** | Any real weights (positive, zero, negative) | ❌ **NEVER** | $f(x) = x^3$ is strictly increasing across all real numbers ($\mathbb{R}$). Relative order is strictly preserved. |
| **$f(x) = e^x$ or $2^x$** | Any real weights | ❌ **NEVER** | Exponential functions are strictly increasing everywhere on $\mathbb{R}$. |
| **$f(x) = x^2$** | **All weights $\ge 0$** | ❌ **NEVER** | On $[0, \infty)$, $x^2$ is strictly increasing. Relative order is preserved. |
| **$f(x) = x^2$** | **Graph contains negative weights** | ⚠️ **YES, CAN CHANGE!** | $x^2$ is NOT monotonic over negative numbers! For example, $-10 < 2$, but $(-10)^2 = 100 > 2^2 = 4$. The order flips! |
| **$f(x) = |x|$** | Graph contains negative weights | ⚠️ **YES, CAN CHANGE!** | Absolute value destroys negative ordering (e.g., $-5 < 1$, but $|-5| = 5 > 1$). |

---

## 5. Bottleneck Spanning Trees & Structural Variants

---

### Minimum Bottleneck Spanning Tree (MBST) vs MST
- **Definition:** The **bottleneck edge** of a spanning tree $T$ is the edge with the maximum weight in $T$:
  $$\beta(T) = \max_{e \in T} w(e)$$
- A spanning tree $T$ is a **Minimum Bottleneck Spanning Tree (MBST)** if its bottleneck edge weight is as small as possible among all spanning trees of $G$:
  $$\beta(T) \le \beta(T') \quad \forall \text{ spanning trees } T'$$

#### The Crucial Relationship (High-Yield Exam Fact):
> **Every MST is an MBST, but NOT every MBST is an MST!**
> $$\text{MST} \subsetneq \text{MBST}$$

```
                +---------------------------------------+
                |    Minimum Bottleneck Trees (MBST)    |
                |   +-------------------------------+   |
                |   | Minimum Spanning Trees (MST)  |   |
                |   +-------------------------------+   |
                +---------------------------------------+
```

1. **Why is every MST an MBST?**  
   Suppose $T$ is an MST with bottleneck edge $e_{\max}$. If there existed another tree $T'$ with a strictly smaller maximum edge, then adding edges from $T'$ to $T$ would allow us to replace $e_{\max}$ with a strictly lighter edge, reducing $w(T)$—contradicting the minimality of $T$.
2. **Why is an MBST not necessarily an MST?**  
   An MBST *only cares about its single heaviest edge*. It does not care about the sum of the remaining $n - 2$ edges!  
   *Counterexample:* A graph where one tree has edge weights $\{10, 10, 10\}$ (max is 10, sum is 30) and another tree has edge weights $\{1, 1, 10\}$ (max is 10, sum is 12). Both are MBSTs (both have bottleneck 10), but only the second is an MST!

---

### Shortest Path Tree (SPT) vs MST
Students frequently mix these up. They solve completely different optimization problems:

| Feature | Minimum Spanning Tree (MST) | Shortest Path Tree (SPT from source $s$) |
|:---|:---|:---|
| **Objective** | Minimize the **global sum** of all edges: $\min \sum_{e \in T} w(e)$ | Minimize distance from **source $s$ to each vertex**: $\min \text{dist}(s, v)$ |
| **Reference Point** | Global (no source vertex) | Local (rooted at a specific source $s$) |
| **Path Lengths** | Path between $u$ and $v$ in MST can be very long and suboptimal! | Path from $s$ to $v$ is strictly the shortest possible path. |
| **Algorithm** | Kruskal, Prim, Borůvka | Dijkstra, Bellman-Ford, BFS |

---

### Dynamic Updates: What if an Edge Weight Changes?

Exams frequently ask: *"You have an MST $T$. An edge changes weight. How fast can you update the MST?"*

#### Scenario 1: A non-tree edge $e = (u, v) \notin T$ has its weight DECREASED.
- Adding $e$ to $T$ creates **exactly one cycle** $C$.
- Find cycle $C$ by doing a simple DFS/BFS in $T$ from $u$ to $v$ ($O(V)$ time).
- Find the heaviest edge $e_{\max}$ on cycle $C$.
- By the Cycle Property:
  - If $w'(e) < w(e_{\max})$, drop $e_{\max}$ and include $e$: $T_{\text{new}} = (T \setminus \{e_{\max}\}) \cup \{e\}$.
  - If $w'(e) \ge w(e_{\max})$, the MST remains unchanged ($T_{\text{new}} = T$).
- **Update Time:** $O(V)$.

#### Scenario 2: A tree edge $e = (u, v) \in T$ has its weight INCREASED.
- Removing $e$ from $T$ splits $T$ into **two connected components** $S$ and $V \setminus S$ (a cut).
- Identify components $S$ and $V \setminus S$ via DFS ($O(V)$ time).
- Scan all edges of $G$ to find edges crossing between $S$ and $V \setminus S$ ($O(E)$ time).
- By the Cut Property: Find the minimum-weight edge $e_{\min}$ crossing this cut.
- Replace $e$ with $e_{\min}$: $T_{\text{new}} = (T \setminus \{e\}) \cup \{e_{\min}\}$.
- **Update Time:** $O(E)$.

---

## 6. The Advanced & Twisted Exam Question Bank (12 High-Yield Challenges)

These 12 problems are specifically designed to mirror the tricky, multi-layered reasoning problems seen on advanced exams.

---

### Challenge 1: The "Unique Minimum Weight Cycle" Trap

> **Proposition:** Suppose graph $G = (V, E)$ has distinct edge weights. Let $C$ be the unique cycle in $G$ whose total edge weight $\sum_{e \in C} w(e)$ is strictly smaller than that of any other cycle in $G$.  
> **Question:** Is the strictly lightest edge of $C$ guaranteed to belong to every MST of $G$?

**Verdict:** **NO (FALSE).**

#### The Trap:
It is tempting to think: *"Cycle $C$ is the cheapest cycle in the whole graph, so surely its cheapest edge must be an essential building block of the MST!"*

#### The Counterexample:
Remember: To guarantee an edge is in the MST via the Cut Property, it must be the lightest edge crossing **some cut**. Being light in a cycle does not protect an edge from being the heaviest edge in some *other* cycle!

Consider a graph with vertices $\{A, B, C, D\}$:
- Cycle $C_1$: Triangle on $\{A, B, C\}$ with weights:
  - $w(A, B) = 10$
  - $w(B, C) = 11$
  - $w(C, A) = 12$
  - Total weight of $C_1 = 10 + 11 + 12 = 33$.
  - The lightest edge in $C_1$ is $e = (A, B)$ with weight $10$.
- Now add vertex $D$ connected to $A$ and $B$:
  - $w(A, D) = 1$
  - $w(D, B) = 2$
- This creates Cycle $C_2$: $\{A, D, B, A\}$ with weights $1, 2, 10$.
  - Total weight of $C_2 = 1 + 2 + 10 = 13$.
  - Notice $C_2$ is cheaper than $C_1$, so let's adjust weights so $C_1$ is the unique cheapest cycle:
  - Let $w(A, B) = 10$.
  - Let $w(A, D) = 100, w(D, B) = 100 \implies \text{weight}(C_2) = 210$.
  - Now $C_1$ (weight 33) is the **unique cheapest cycle** in the entire graph!
  - But what if $A$ and $B$ are also connected via another path: $w(A, X) = 1, w(X, Y) = 1, w(Y, B) = 1$?
  - Then cycle $A-X-Y-B-A$ has weight $1 + 1 + 1 + 10 = 13$, which makes it cheaper than $C_1$.
- Let's construct the precise counterexample cleanly:
  We need $e \in C_{\text{min\_cycle}}$ to be excluded from the MST.
  An edge is excluded from the MST if and only if it is the **heaviest edge of some cycle**!
  Can $e$ be the lightest edge in $C$, but the heaviest edge in another cycle $C'$?
  **YES!**
  - Let $e = (u, v)$ have weight $w(e) = 10$.
  - Let cycle $C$ be $u - v - w - u$ with weights: $w(u, v) = 10, w(v, w) = 20, w(w, u) = 20$.
    Total weight of $C = 50$. Edge $e$ is the strictly lightest edge of $C$.
  - Let there be another path between $u$ and $v$ with edges: $w(u, x) = 2, w(x, v) = 3$.
  - Then cycle $C' = u - x - v - u$ has edges $\{2, 3, 10\}$.
  - In cycle $C'$, edge $e$ (weight 10) is the **strictly heaviest edge**!
  - By the Cycle Property, $e$ **CANNOT belong to any MST**!
  - Now, simply make all other cycles in the graph have huge weights (e.g., $w(u, x) = 200, w(x, v) = 300$), so $C$ remains the unique cheapest cycle. But wait, if $w(u, x)$ is huge, $C'$ is no longer lighter than $C$.
  - Can $C$ be the cheapest cycle overall, while $e$ is still excluded?
    - If $e$ is excluded, there must be a cycle $C'$ where $e$ is the heaviest edge.
    - If $e$ is the heaviest edge in $C'$, all other edges in $C'$ have weight $< w(e)$.
    - Then the total weight of $C'$ is $w(e) + \sum_{e' \in C' \setminus \{e\}} w(e') < w(e) + (|C'|-1)w(e) = |C'|w(e)$.
    - Meanwhile, in $C$, $e$ is the *lightest* edge, so all other edges in $C$ have weight $> w(e)$.
    - Thus $w(C) > |C|w(e)$.
    - If $|C'| \le |C|$, then $w(C') < w(C)$, which contradicts $C$ being the cheapest cycle!
    - **AHA! What if $|C'| > |C|$?**
    - Let cycle $C$ have 3 edges: $\{10, 10.1, 10.1\} \implies \text{weight}(C) = 30.2$. Lightest edge is $10$.
    - Let cycle $C'$ have 10 edges: the edge of weight $10$, plus 9 tiny edges of weight $2.1$ each.
    - Total weight of $C' = 10 + 9 \times 2.1 = 10 + 18.9 = 28.9 < 30.2$ (still lighter).
    - What if 9 edges of weight $2.5$? $10 + 22.5 = 32.5 > 30.2$!
    - **BINGO! Look at this construction:**
      - Cycle $C$ has 3 edges: $\{10, 10.1, 10.1\}$ with **total weight = 30.2**.
      - Cycle $C'$ shares edge $e$ (weight 10) with $C$, and has 9 other edges of weight $2.5$ each.
      - Total weight of $C' = 10 + 9(2.5) = 32.5$.
      - Notice: **Cycle $C$ (30.2) is strictly cheaper than Cycle $C'$ (32.5)!** In fact, $C$ can be the unique minimum-weight cycle in the entire graph!
      - In cycle $C'$, edge $e$ has weight $10$, while all other 9 edges have weight $2.5$.
      - Therefore, in cycle $C'$, **edge $e = 10$ is the strictly HEAVIEST edge**!
      - By the Cycle Property on $C'$, **edge $e$ is EXCLUDED from every MST!**
    - **Conclusion:** Even though $e$ is the lightest edge of the unique cheapest cycle in the graph, it is excluded from the MST!

---

### Challenge 2: Kruskal’s Tie-Breaking Completeness

> **Proposition:** Suppose a graph $G$ has duplicate edge weights, so it has multiple distinct MSTs. By varying the tie-breaking rules when sorting edges of equal weight, can Kruskal’s algorithm generate **every single possible MST** of $G$?

**Verdict:** **YES (TRUE).**

#### The Reasoning:
This is a deep theoretical property of matroids (the Graphic Matroid):
- Let $T^*$ be **any** arbitrary valid MST of $G$.
- We can define a tie-breaking rule for Kruskal: whenever two edges have equal weight, prioritize the edge that belongs to $T^*$ over the edge that does not belong to $T^*$.
- Since $T^*$ is an MST, it satisfies the Cut Property at every step. Kruskal will never be forced to reject an edge of $T^*$ due to a cycle formed by previously chosen edges of $T^*$, because $T^*$ is acyclic.
- Therefore, under this tie-breaking rule, Kruskal will choose every single edge of $T^*$, perfectly reconstructing $T^*$.
- Since this holds for *any* arbitrary MST $T^*$, Kruskal’s algorithm can produce every valid MST of the graph.

---

### Challenge 3: The Minimum Bottleneck Equivalence Fallacy

> **Proposition:** Let $G$ be an undirected graph with distinct edge weights. Let $T_{\text{MST}}$ be the unique MST of $G$, and let $T_{\text{MBST}}$ be any Minimum Bottleneck Spanning Tree of $G$.  
> Is it always true that $T_{\text{MST}} = T_{\text{MBST}}$?

**Verdict:** **NO (FALSE).**

#### The Reasoning:
Even with **strictly distinct edge weights**, an MBST is **not** necessarily unique, and an MBST does **not** have to equal the MST!
Distinct edge weights guarantee that the MST is unique, but they do **NOT** guarantee that the MBST is unique.

#### Counterexample:
Vertices $\{A, B, C, D\}$ arranged as a 4-cycle:
- Edge $(A, B) = 1$
- Edge $(B, C) = 2$
- Edge $(C, D) = 3$
- Edge $(D, A) = 4$
- All edge weights are distinct!

Any spanning tree must select $4 - 1 = 3$ edges:
- **Tree 1 (MST):** Pick the 3 lightest edges: $\{(A, B), (B, C), (C, D)\}$ with weights $\{1, 2, 3\}$.
  - Total weight $= 1 + 2 + 3 = 6$.
  - Bottleneck edge $= 3$.
- **Tree 2:** Pick edges $\{(A, B), (B, C), (D, A)\}$ with weights $\{1, 2, 4\}$.
  - Total weight $= 7$, bottleneck $= 4$.
- **Tree 3:** Pick edges $\{(A, B), (C, D), (D, A)\}$ with weights $\{1, 3, 4\}$.
  - Bottleneck $= 4$.
- Wait, what if we have a diagonal edge?
  Let vertices be $\{A, B, C\}$.
  Edges: $(A, B) = 1$, $(B, C) = 2$, $(A, C) = 3$.
  MST is $\{1, 2\}$ (bottleneck 2). MBST must have bottleneck 2, so only $\{1, 2\}$ works.
  To get multiple MBSTs, we need two trees that share the **same maximum edge**:
  Consider 4 vertices $\{A, B, C, D\}$ with edges:
  - $(A, B) = 10$
  - $(C, D) = 100$
  - $(A, C) = 2$
  - $(B, D) = 3$
  - $(A, D) = 4$
  - $(B, C) = 5$
  All edge weights distinct!
  Notice the bridge or heavy connections:
  Consider a graph with two disjoint components connected by edges:
  Let $V = \{A, B, C\}$.
  Suppose we have edges:
  $e_1 = (A, B)$ with weight $2$.
  $e_2 = (B, C)$ with weight $10$.
  $e_3 = (A, C)$ with weight $10$. (Weights must be distinct! So let's make weights distinct):
  - Let vertices be $\{A, B, C, D\}$.
  - Path 1: $A-B$ (weight 1), $B-C$ (weight 10), $C-D$ (weight 2). Total sum $= 13$, max $= 10$.
  - Path 2: $A-C$ (weight 3), $B-D$ (weight 4).
  - Let the graph have edges:
    - $(A, B) = 1$
    - $(C, D) = 2$
    - $(A, C) = 10$
    - $(B, D) = 9$
  - **Spanning Tree 1:** Edges $\{(A, B), (C, D), (B, D)\}$ with weights $\{1, 2, 9\}$.
    - Maximum edge $= 9$. Total sum $= 12$.
  - **Spanning Tree 2:** Edges $\{(A, B), (C, D), (A, C)\}$ with weights $\{1, 2, 10\}$.
    - Maximum edge $= 10$.
  - Here Tree 1 is both MST and MBST.
  - To make an MBST different from MST, we need the bottleneck edge to be unavoidable:
    Suppose vertices $\{A, B\}$ are in group 1, and $\{C, D\}$ are in group 2.
    All edges between group 1 and group 2 are heavy:
    - $(A, C) = 50$
    - $(B, D) = 51$
    - In group 1: $(A, B) = 1$.
    - In group 2: $(C, D) = 2$.
    To connect group 1 and group 2, any spanning tree MUST pick at least one crossing edge.
    The minimum possible crossing edge is $(A, C) = 50$.
    So **every** spanning tree that connects group 1 and 2 must have a bottleneck of **at least 50**.
    - **Tree 1 (The MST):** Pick $(A, B)=1, (C, D)=2, (A, C)=50$.
      - Bottleneck $= 50$. Total weight $= 53$.
    - **Now add another intra-group edge or alternative:**
      What if group 2 also had a vertex $E$?
      Even simpler: What if Tree 2 picks:
      $(A, C) = 50$ (the bottleneck), but inside group 1, instead of edge $(A, B)=1$, it picks an edge of weight $5$?
      Let group 1 have 3 vertices: $\{A, B, X\}$.
      - Edges in group 1: $(A, X) = 1, (X, B) = 2, (A, B) = 3$.
      - Crossing edge: $(X, C) = 50$.
      - Group 2 has only $\{C\}$.
      - **Tree 1 (MST):** Edges $\{(A, X)=1, (X, B)=2, (X, C)=50\}$.
        - Max edge $= 50$. Total sum $= 53$.
      - **Tree 2:** Edges $\{(A, B)=3, (X, B)=2, (X, C)=50\}$.
        - Max edge $= 50$. Total sum $= 55$.
      - Notice: The bottleneck of Tree 2 is **50**, which is the absolute minimum bottleneck possible for any spanning tree of this graph!
      - Therefore, **Tree 2 is a valid Minimum Bottleneck Spanning Tree (MBST)!**
      - But Tree 2 has total sum $55 > 53$, so **Tree 2 is NOT an MST!**
      - All edge weights $\{1, 2, 3, 50\}$ are **strictly distinct**!
    - **Conclusion:** Even with strictly distinct edge weights, an MBST does NOT have to be an MST!

---

### Challenge 4: Nonlinear Transformations with Negative Edge Weights

> **Proposition:** Let $G = (V, E)$ be a connected graph with real edge weights (some of which may be negative). Let $T$ be an MST of $G$.  
> 1. If we replace each edge weight $w(e)$ with $w'(e) = (w(e))^3$, is $T$ guaranteed to remain an MST?  
> 2. If we replace each edge weight $w(e)$ with $w'(e) = (w(e))^2$, is $T$ guaranteed to remain an MST?

**Verdict:**  
- **Part 1 ($w^3$):** **YES (TRUE).**  
- **Part 2 ($w^2$):** **NO (FALSE).**

#### The Reasoning:
- **For Part 1:** The function $f(x) = x^3$ has derivative $f'(x) = 3x^2 \ge 0$, and is **strictly monotonically increasing** across the entire real number line $(-\infty, +\infty)$:
  $$x < y \iff x^3 < y^3 \quad \forall x, y \in \mathbb{R}$$
  Because the relative order of every pair of edges is strictly preserved, Kruskal’s algorithm processes edges in the exact same sequence. Hence, $T$ remains an MST.
- **For Part 2:** The function $f(x) = x^2$ is **NOT monotonic** when negative numbers are involved!  
  For negative numbers, squaring **reverses** the order:
  $$-5 < -2 \implies (-5)^2 = 25 > 4 = (-2)^2$$
  *Counterexample:*  
  Consider two edges competing across a cut:
  - $e_1$ has weight $-4$.
  - $e_2$ has weight $1$.
  Originally, $e_1$ is lighter than $e_2$ ($-4 < 1$), so $e_1$ is chosen for the MST.  
  After squaring: $w'(e_1) = (-4)^2 = 16$, while $w'(e_2) = (1)^2 = 1$.  
  Now $e_2$ is much lighter than $e_1$ ($1 < 16$), so the MST switches to $e_2$!

---

### Challenge 5: Local Edge Perturbations (Cascade Effects)

> **Proposition:** Let $T$ be an MST of $G$. Suppose we strictly decrease the weight of a single edge $e^* \notin T$ by $\Delta > 0$, and recompute the MST.  
> Can the new MST $T'$ differ from $T$ by **more than one edge**? (i.e., can more than one edge be removed from $T$)?

**Verdict:** **NO (FALSE - At most ONE edge can leave $T$).**

#### The Proof:
1. Adding $e^*$ to $T$ creates **exactly one simple cycle** $C$.
2. Only edges in cycle $C$ are affected by the addition of $e^*$.
3. By the Cycle Property, the heaviest edge on cycle $C$ under the new weights must be excluded.
4. If the decreased weight $w'(e^*)$ is strictly smaller than the heaviest edge $e_{\text{old}} \in C$, then $e_{\text{old}}$ is removed, and $e^*$ is added:
   $$T' = (T \setminus \{e_{\text{old}}\}) \cup \{e^*\}$$
5. Exactly **one** edge ($e_{\text{old}}$) is removed, and exactly **one** edge ($e^*$) enters!
6. All other edges of $T$ remain untouched. It is mathematically impossible for a single edge decrease to cause a cascade that removes 2 or more edges.

---

### Challenge 6: The Total Incompatibility of SPT and MST

> **Question:** Does there exist a connected graph $G$ such that for **EVERY vertex $v \in V$**, the Shortest Path Tree rooted at $v$ ($\text{SPT}_v$) is **DIFFERENT** from the Minimum Spanning Tree ($T_{\text{MST}}$)?

**Verdict:** **YES (TRUE).**

#### Construction / Proof:
Consider a simple cycle of 4 vertices $V = \{A, B, C, D\}$ with uniform edge weights $w(e) = 1$ for all 4 edges:
- **The MST:** Any spanning tree of a 4-cycle must have $4 - 1 = 3$ edges (it drops exactly 1 edge). Total MST weight $= 1 + 1 + 1 = 3$.
- **Now look at the SPT from vertex $A$ ($\text{SPT}_A$):**
  - Distances from $A$: $\text{dist}(A, A) = 0, \text{dist}(A, B) = 1, \text{dist}(A, D) = 1$.
  - What about vertex $C$? Path $A-B-C$ has length 2. Path $A-D-C$ has length 2.
  - An SPT from $A$ can drop edge $(C, D)$ (giving path $A-B-C$) or drop edge $(B, C)$ (giving path $A-D-C$).
  - If we want to force **every** SPT to disagree with a fixed MST, we just add distinct weights!
- **Concrete Weighted Construction:**
  Vertices $\{A, B, C\}$ (a triangle):
  - $w(A, B) = 3$
  - $w(B, C) = 3$
  - $w(A, C) = 4$
  - **The unique MST:** Picks the two cheapest edges: $\{(A, B), (B, C)\}$. Total weight $= 3 + 3 = 6$.
  - **Now check the SPT from vertex $A$ ($\text{SPT}_A$):**
    - Shortest path from $A$ to $B$: Direct edge $(A, B)$ of length 3.
    - Shortest path from $A$ to $C$: Direct edge $(A, C)$ of length **4** (the path through $B$ has length $3 + 3 = 6 > 4$!).
    - Therefore, $\text{SPT}_A$ **MUST contain edge $(A, C)$**!
    - But edge $(A, C)$ is **NOT in the MST**!
    - Hence, $\text{SPT}_A \ne T_{\text{MST}}$.
  - **Now check the SPT from vertex $C$ ($\text{SPT}_C$):**
    - Shortest path from $C$ to $B$: Edge $(B, C)$ of length 3.
    - Shortest path from $C$ to $A$: Direct edge $(A, C)$ of length **4** (path via $B$ is $6 > 4$).
    - Therefore, $\text{SPT}_C$ **MUST contain edge $(A, C)$**!
    - Hence, $\text{SPT}_C \ne T_{\text{MST}}$.
  - **Now check the SPT from vertex $B$ ($\text{SPT}_B$):**
    - Shortest path from $B$ to $A$: Edge $(B, A)$ of length 3.
    - Shortest path from $B$ to $C$: Edge $(B, C)$ of length 3.
    - Here $\text{SPT}_B = \{(A, B), (B, C)\} = T_{\text{MST}}$.
  - To make $\text{SPT}_B$ also fail, simply make the triangle symmetric:
    - $w(A, B) = 3, w(B, C) = 3, w(A, C) = 3.1$.
    - Add a fourth vertex $D$ connected to $B$ with weight 1.
    - Or even simpler: Consider a 4-vertex cycle where every pair of opposite vertices has a shortcut!
    - In general, whenever shortest paths between non-adjacent nodes require direct shortcut edges that are heavier than individual peripheral edges, **every SPT will include at least one shortcut edge that the MST rejects**.

---

### Challenge 7: The Degree-Constrained Greedy Fallacy (NP-Hardness)

> **Problem:** We want to find a Spanning Tree where **no vertex has degree greater than 2** (this is simply a Hamiltonian Path of minimum weight).  
> A student suggests modifying Kruskal’s algorithm:  
> *"Sort edges by weight. When considering edge $(u, v)$, add it to $T$ if and only if it does not create a cycle AND neither $u$ nor $v$ currently has degree $\ge 2$ in $T$."*  
> **Question:** Does this greedy modification find the minimum degree-constrained spanning tree?

**Verdict:** **NO (FALSE).**

#### The Reasoning:
1. **Theoretical Impossibility:** Finding a spanning tree with maximum degree $\le 2$ is equivalent to finding a **Hamiltonian Path**. Determining whether a graph even *has* a Hamiltonian Path is **NP-complete**! A greedy polynomial-time algorithm cannot solve an NP-complete problem.
2. **Greedy Trap Counterexample:**
   Consider 4 vertices $\{A, B, C, D\}$ with edges:
   - $(B, C) = 1$
   - $(B, D) = 2$
   - $(A, B) = 3$
   - $(A, C) = 10$
   - $(C, D) = 10$
   - The greedy algorithm sorts edges: $(B, C)=1, (B, D)=2, (A, B)=3 \dots$
   - It picks $(B, C)$ [degree of $B$ is 1].
   - It picks $(B, D)$ [degree of $B$ is now 2!].
   - Now, edge $(A, B)$ is considered. But vertex $B$ already has degree 2! The algorithm **rejects $(A, B)$**.
   - To connect $A$, the algorithm is forced to pick the horrible edge $(A, C)=10$.
   - Degree of $C$ becomes 2 (edges $(B, C)$ and $(A, C)$).
   - Total weight: $1 + 2 + 10 = 13$.
   - **Optimal Path:** Pick $(A, B)=3, (B, D)=2, (D, C)=10 \implies$ or path $A-B-C-D$ with edges $(A, B)=3, (B, C)=1, (C, D)=10 \implies 14$.
   - Even worse: The greedy heuristic can get completely **stuck** and report that no degree-2 spanning tree exists, even when one does!

---

### Challenge 8: Cycles with Multiple Equal Maximum-Weight Edges

> **Proposition:** Suppose a simple cycle $C$ in graph $G$ has **two edges**, $e_1$ and $e_2$, that share the maximum weight in $C$ (i.e., $w(e_1) = w(e_2) = \max_{e \in C} w(e)$).  
> Can an MST of $G$ contain:
> 1. Neither $e_1$ nor $e_2$?
> 2. Exactly one of $e_1$ or $e_2$?
> 3. **Both** $e_1$ and $e_2$?

**Verdict:** **All three cases are possible!**

#### Concrete Demonstrations:
1. **Can an MST contain NEITHER?**  
   **YES.** Suppose cycle $C$ has edges with weights $\{1, 10, 10\}$. The two heaviest edges are 10 and 10. If the graph has other edges of weight 2 connecting the vertices, an MST will use the lighter edges and exclude both 10s.
2. **Can an MST contain EXACTLY ONE?**  
   **YES.** In cycle $C$ with weights $\{1, 10, 10\}$, suppose the two 10-weight edges are the only ways to reach certain vertices. The MST must break the cycle, so it drops one 10, but keeps the other 10 to maintain connectivity.
3. **Can an MST contain BOTH?**  
   **YES! (The Classic Exam Trap!)**  
   Students think: *"The cycle property says the heaviest edge cannot be in the MST, so you can't have both!"*  
   **Wrong!** The cycle property only says you must drop **at least one** heaviest edge from that cycle. It does **not** forbid keeping the other, and if another cycle is broken elsewhere, **both can be in the MST!**

   *Counterexample where an MST contains BOTH $e_1$ and $e_2$:*  
   Consider 4 vertices in a line: $A - B - C - D$.
   - $(A, B) = 10$ ($e_1$)
   - $(C, D) = 10$ ($e_2$)
   - $(B, C) = 1$
   Now add an edge $(A, D) = 100$.
   Look at cycle $C = A - B - C - D - A$.
   The edge weights on $C$ are $\{10, 1, 10, 100\}$.
   Who are the maximum weight edges in $C$? The single heaviest edge is $(A, D) = 100$.
   What if we remove $(A, D)$ and make another cycle:
   Consider a triangle $A - B - C - A$ with weights:
   - $(A, B) = 10$ ($e_1$)
   - $(B, C) = 10$ ($e_2$)
   - $(A, C) = 10$ ($e_3$)
   All three edges have the maximum weight on cycle $C$!
   To span $\{A, B, C\}$, any spanning tree must pick **any 2 edges** out of the 3!
   If it picks $e_1$ and $e_2$, the MST contains **both** $e_1$ and $e_2$!

---

### Challenge 9: The Union of Lightest Edges of All Cuts

> **Proposition:** For every possible cut $(S, V \setminus S)$ in a graph $G$ with distinct edge weights, let $e_S$ be the strictly lightest edge crossing that cut.  
> Let $E_{\text{light}} = \{ e_S \mid (S, V \setminus S) \text{ is a cut in } G \}$.  
> **Question:** Is the set $E_{\text{light}}$ **identically equal** to the unique Minimum Spanning Tree of $G$?

**Verdict:** **YES! $E_{\text{light}} = T_{\text{MST}}$.**

#### The Proof:
1. **$E_{\text{light}} \subseteq T_{\text{MST}}$:**  
   By the Cut Property, the strictly lightest edge crossing *any* cut must belong to every MST. Since each $e \in E_{\text{light}}$ is the strictly lightest edge of at least one cut, every edge in $E_{\text{light}}$ must be in $T_{\text{MST}}$.
2. **$T_{\text{MST}} \subseteq E_{\text{light}}$:**  
   Let $e = (u, v)$ be any edge in $T_{\text{MST}}$.  
   Removing $e$ from $T_{\text{MST}}$ splits the tree into two connected components, defining a cut $(S, V \setminus S)$.  
   Is $e$ the lightest edge crossing this cut $(S, V \setminus S)$?  
   **YES!** If there were a lighter edge $e'$ crossing this cut, replacing $e$ with $e'$ would produce a strictly lighter spanning tree, contradicting that $T_{\text{MST}}$ was an MST.  
   Therefore, every edge $e \in T_{\text{MST}}$ is the strictly lightest edge of its fundamental cut!
3. **Conclusion:** The set of strictly lightest edges of all cuts is **precisely the MST**:
   $$E_{\text{light}} = T_{\text{MST}}$$

---

### Challenge 10: Dynamic Repair after Vertex Deletion

> **Problem:** We are given an MST $T$ of a graph $G = (V, E)$. A single vertex $v \in V$ is deleted from $G$, along with all its incident edges.  
> Removing $v$ breaks the tree $T$ into $k$ disconnected subtrees $T_1, T_2, \dots, T_k$.  
> **Question:** How can we find the MST of the remaining graph $G \setminus \{v\}$ in $O(E + k \log k)$ time, without running Kruskal from scratch on all of $G \setminus \{v\}$?

#### The Strategy:
- The subtrees $T_1, T_2, \dots, T_k$ are already minimum spanning trees for their respective vertex subsets! Deleting $v$ does not invalidate the internal optimality of $T_1, \dots, T_k$.
- We can treat each of the $k$ subtrees as a **single super-vertex**.
- Contract each $T_i$ into a super-vertex $i \in \{1, \dots, k\}$.
- For any edge in $G \setminus \{v\}$ connecting subtree $T_i$ and subtree $T_j$, keep only the lightest edge between $i$ and $j$.
- This contracted multigraph has only **$k$ vertices** and at most $E$ edges!
- Run Kruskal’s or Prim’s algorithm on this small $k$-vertex graph.
- Running time: $O(E + k \log k)$, which is vastly faster than re-running Kruskal on the full graph ($O(E \log V)$), especially when $k \ll V$ (e.g., if $v$ was a leaf, $k = 1$, and update time is $O(1)$!).

---

### Challenge 11: The Random Edge Insertion & Cycle-Breaking Algorithm

> **Algorithm:** Start with an empty set $T = \emptyset$.  
> Pick edges from $E$ in **completely arbitrary (random) order**.  
> For each edge $e = (u, v)$:
> 1. Add $e$ to $T$ ($T \leftarrow T \cup \{e\}$).
> 2. If $T$ contains a cycle $C$, find the maximum-weight edge $e_{\max}$ in $C$, and remove it ($T \leftarrow T \setminus \{e_{\max}\}$).  
> 
> **Question:** Does this algorithm correctly output an MST after all edges are processed?

**Verdict:** **YES (TRUE).** *(This was MAYBE-MST-C from the EndSem exam!)*

#### Why It Works:
- At any point, $T$ has no cycles because whenever a cycle is created by adding an edge, the cycle is immediately broken by removing its heaviest edge.
- By the Cycle Property, the heaviest edge in any cycle cannot belong to an MST. Thus, removing $e_{\max}$ never destroys our ability to reach an MST.
- When all edges have been examined, $T$ spans all connected components of $G$ and contains no cycles. Therefore, $T$ is an MST.

---

### Challenge 12: Independent Percentage Perturbations

> **Proposition:** Suppose all edge weights are strictly positive ($w(e) > 0$).  
> 1. If every edge weight is increased by exactly $10\%$ ($w'(e) = 1.10 \cdot w(e)$), does the MST change?  
> 2. If every edge weight is increased by an individual, independent percentage $p_e \in [5\%, 10\%]$ ($w'(e) = (1 + p_e)w(e)$), does the MST change?

**Verdict:**  
- **Part 1 (Uniform 10%):** **NO (MST is unchanged).**  
- **Part 2 (Independent percentages):** **YES (MST can change completely!).**

#### The Reasoning:
- **Part 1:** Multiplying all edges by a uniform constant $c = 1.10 > 0$ preserves the exact sorted order of all edges:
  $$w(e_1) < w(e_2) \iff 1.10 \cdot w(e_1) < 1.10 \cdot w(e_2)$$
  The Cut and Cycle properties are unaffected.
- **Part 2:** When different edges receive different percentage boosts, their relative order can flip!  
  *Counterexample:*  
  - Edge $e_1$ has weight $100$. It receives a $10\%$ increase $\implies w'(e_1) = 110$.  
  - Edge $e_2$ has weight $102$. It receives a $5\%$ increase $\implies w'(e_2) = 102 \times 1.05 = 107.1$.  
  Originally, $e_1 < e_2$ ($100 < 102$).  
  After perturbation: $e_2 < e_1$ ($107.1 < 110$)!  
  The order has inverted, which can alter which edge is chosen across a cut.

---

## 7. The 7 Golden Rules for Acing MST Exam Questions

Keep these 7 rules at the front of your mind during your exam:

1. **Trees have $n - 1$ edges:** Any question involving adding a constant $+c$ to all edges is trivial—every spanning tree changes by $(n-1)c$, so the MST is **invariant**.
2. **MSTs love negative numbers:** Unlike Dijkstra, negative weights do **not** break Kruskal, Prim, or Borůvka.
3. **Monotonicity preserves MSTs:** Any strictly increasing function $f(x)$ applied to edge weights leaves the MST unchanged. If weights can be negative, $x^2$ is **not** monotonic!
4. **Distinct weights = Unique MST:** If all weights are unique, there is exactly one MST. (The converse is false: non-distinct weights can still have a unique MST).
5. **Cuts INCLUDE, Cycles EXCLUDE:**
   - Lightest edge crossing a cut $\implies$ **IN** the MST.
   - Heaviest edge in a cycle $\implies$ **OUT** of the MST.
6. **MST $\ne$ SPT:** The tree that minimizes the total sum of all edges rarely minimizes distances from a single source vertex.
7. **When proving or disproving, use the Exchange Argument:**
   - Disprove by drawing a 3-vertex triangle or 4-vertex diamond.
   - Prove by swapping an edge into the tree, showing a cycle is formed, and swapping the heavier edge out to show $w(T') < w(T)$.
