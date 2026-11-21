# Data Structures and Algorithms for Problem Solving (CS1.304)
## End-Semester Examination Solutions (29 Nov 2025)
**Instructors:** Kshitij Gajjar, Lini Thomas  
**Total Marks:** 70 `[8, 8, 8, 12, 5, 17, 12]`  
**Source Document:** [don't_share_to_TAs_DSAPS.pdf](file:///home/krshnsaboo/IIITH/SEMESTER%201/DSAPS/DSAPS_EndTerm/don't_share_to_TAs_DSAPS.pdf) (Pages 5–7)

---

## Table of Contents
1. [Puzzle 1: Johnson's Algorithm Modifications [8 Marks]](#puzzle-1-johnsons-algorithm-modifications-8-marks)
   - [Case 1: Running Bellman-Ford from an Existing Vertex A](#case-1-running-bellman-ford-from-an-existing-vertex-a)
   - [Case 2: Adding 0-Weight Edges from an Existing Vertex A](#case-2-adding-0-weight-edges-from-an-existing-vertex-a)
2. [Puzzle 2: Minimum Spanning Tree Algorithms [8 Marks]](#puzzle-2-minimum-spanning-tree-algorithms-8-marks)
   - [MAYBE-MST-A (Reverse-Delete Algorithm)](#maybe-mst-a-reverse-delete-algorithm)
   - [MAYBE-MST-C (Cycle-Breaking Algorithm)](#maybe-mst-c-cycle-breaking-algorithm)
3. [Puzzle 3: Modifying Dijkstra for Longest Simple Paths [8 Marks]](#puzzle-3-modifying-dijkstra-for-longest-simple-paths-8-marks)
   - [Part (a): Acyclic Undirected Graph (Forest/Tree)](#part-a-acyclic-undirected-graph-foresttree)
   - [Part (b): Graphs with Cycles](#part-b-graphs-with-cycles)
4. [Puzzle 4: Properties of MSTs and Shortest Paths [12 Marks]](#puzzle-4-properties-of-msts-and-shortest-paths-12-marks)
   - [Part (a): Cycle Properties with Distinct Edge Weights](#part-a-cycle-properties-with-distinct-edge-weights)
   - [Part (b): Uniform Weight Shift (+1 to all edges)](#part-b-uniform-weight-shift-1-to-all-edges)
5. [Puzzle 5: Friendship Queries and Equivalence Classes [5 Marks]](#puzzle-5-friendship-queries-and-equivalence-classes-5-marks)
   - [Optimal Data Structure: Disjoint Set Union (DSU)](#optimal-data-structure-disjoint-set-union-dsu)
   - [Step-by-Step Construction & Query Processing](#step-by-step-construction--query-processing)
   - [Complexity Analysis](#complexity-analysis)
6. [Puzzle 6: Gale-Shapley Stable Marriage Analysis [17 Marks]](#puzzle-6-gale-shapley-stable-marriage-analysis-17-marks)
   - [Part (a): Preference Construction for $\ge \frac{n(n+1)}{2}$ Proposals](#part-a-preference-construction-for-ge-fracnn12-proposals)
   - [Part (b): Stability Verification & Finding Multiple Stable Matchings](#part-b-stability-verification--finding-multiple-stable-matchings)
   - [Part (c): Efficient Algorithm for Uniqueness of Stable Marriage](#part-c-efficient-algorithm-for-uniqueness-of-stable-marriage)
7. [Puzzle 7: Grid Navigation Puzzle & General Pathfinding [12 Marks]](#puzzle-7-grid-navigation-puzzle--general-pathfinding-12-marks)
   - [Part (a): Concrete Solution to the $6 \times 6$ Grid Board](#part-a-concrete-solution-to-the-6-times-6-grid-board)
   - [Part (b): General Algorithm & Pseudocode for $n \times n$ Grid](#part-b-general-algorithm--pseudocode-for-n-times-n-grid)

---

## Puzzle 1: Johnson's Algorithm Modifications [8 Marks]

### Problem Statement
Consider Johnson’s algorithm to compute all-pairs shortest paths in a given directed, weighted, connected graph. Recall that Johnson’s algorithm creates a new vertex named $\text{"source"}$, and connects this source vertex to every other vertex of the original input graph with an edge weight of $0$.

For each case below, discuss whether Johnson’s algorithm (with the suggested modification in Case 1 and in Case 2) will still be able to produce correct results. Provide necessary explanations to illustrate why or why not.

- **Case 1 [4 Marks]:** Consider making the modification that we select one of the existing vertices (say $A$) in the input graph and run the Bellman-Ford algorithm with vertex $A$ as the source to find the shortest distance from $A$ to all vertices instead of introducing a new vertex as source. The value of $h(x)$ will now be the shortest distance from vertex $A$ to the vertex $x$ in the input graph.
- **Case 2 [4 Marks]:** Professor Ben proposes a further modification. He suggests that instead of introducing a new vertex, we select one of the existing vertices (say $A$) in the input graph and introduce a $0$-weight edge to all vertices that the vertex $A$ is not connected to in the original input graph. Then run the Bellman-Ford algorithm with vertex $A$ as the source. The value of $h(x)$ will now be the shortest distance from vertex $A$ to the vertex $x$.

---

### Case 1: Running Bellman-Ford from an Existing Vertex A
**Verdict:** **INCORRECT (Will NOT produce correct results).**

#### Detailed Explanation:
Johnson's algorithm uses vertex potentials $h: V \to \mathbb{R}$ to reweight edge weights via the formula:
$$\hat{w}(u, v) = w(u, v) + h(u) - h(v)$$
For Dijkstra's algorithm to work correctly on the reweighted graph, the reweighted edge weights must be strictly non-negative:
$$\hat{w}(u, v) \ge 0 \iff h(v) \le h(u) + w(u, v) \quad \forall (u, v) \in E$$
This inequality is guaranteed by the triangle inequality of shortest-path distances **only if** both $u$ and $v$ are reachable from the source vertex.

Using an existing vertex $A \in V$ as the source fails for the following fundamental reasons:

1. **Unreachability and Incomplete Potentials:**
   In a directed graph, vertex $A$ is not guaranteed to reach all other vertices $x \in V$. If a vertex $x$ is unreachable from $A$, its distance from $A$ is $\delta(A, x) = +\infty$.
   - If $h(x) = +\infty$, the reweighted formula $\hat{w}(u, v) = w(u, v) + h(u) - h(v)$ yields $\infty - \infty$ (mathematically undefined) for edges between unreachable vertices.
   - If one attempts to set $h(x) = 0$ (or any constant) for unreachable vertices, consider an edge $(u, v)$ where neither $u$ nor $v$ is reachable from $A$, and $w(u, v) < 0$. Then $h(u) = h(v) = 0$, leading to $\hat{w}(u, v) = w(u, v) < 0$. Dijkstra's algorithm cannot handle negative edges and will fail.

2. **Failure to Detect Negative Cycles:**
   If the graph contains a negative-weight cycle that is not reachable from vertex $A$, Bellman-Ford run from $A$ will never reach that cycle and will **fail to detect it**. Johnson's algorithm will erroneously report that the graph has no negative cycles, and subsequent executions of Dijkstra will produce incorrect results or loop indefinitely.

#### Minimal Counterexample:
Consider a directed graph with $V = \{A, B, C\}$:
- $E = \{(B, C)\}$ with $w(B, C) = -5$.
- Vertex $A$ has no outgoing edges.
- Running Bellman-Ford from $A$: $h(A) = 0$, $h(B) = \infty$, $h(C) = \infty$.
- The negative edge $(B, C)$ is not eliminated or properly reweighted. Thus, all-pairs shortest paths cannot be computed.

---

### Case 2: Adding 0-Weight Edges from an Existing Vertex A
**Verdict:** **INCORRECT (Will NOT produce correct results).**

#### Detailed Explanation:
In standard Johnson's algorithm, the source $s$ is an **external** vertex ($s \notin V$). As a result, $s$ has $\text{in-degree}(s) = 0$, meaning **no edges enter $s$**. Therefore, adding directed edges out of $s$ cannot create any new cycles in the graph.

In Professor Ben's proposal, vertex $A \in V$ is an **internal** vertex of the graph. It may already possess incoming edges from other vertices in $V$. Adding $0$-weight edges from $A$ to other vertices creates new directed cycles that involve $A$.

1. **Creation of False Negative Cycles:**
   Suppose that in the original graph $G$, there is a path from some vertex $X$ to $A$ with negative total weight (e.g., $w(X \rightsquigarrow A) = -2$). In $G$, this does not create a negative cycle because there is no path from $A$ back to $X$ (the graph may be a Directed Acyclic Graph, DAG).
   Under Professor Ben's modification:
   - Since $A$ is not connected to $X$, a directed edge $(A, X)$ with weight $0$ is added.
   - Now, a new cycle is formed: $A \to X \rightsquigarrow A$ with total weight $0 + (-2) = -2 < 0$.
   - When Bellman-Ford is executed from $A$, it will **detect this newly created negative cycle and abort**, reporting that the graph contains a negative-weight cycle—even though the original graph was completely valid and had **no negative cycles**!

#### Minimal Counterexample:
Consider a graph $G = (V, E)$ with $V = \{A, B\}$:
- Single original edge: $(B, A)$ with weight $w(B, A) = -1$.
- This graph is a DAG; it contains **no cycles** whatsoever. The true shortest paths are $\delta(B, A) = -1$, $\delta(A, B) = \infty$, $\delta(A, A) = 0$, $\delta(B, B) = 0$.
- Under Professor Ben's proposal, since $A$ has no edge to $B$, a directed edge $(A, B)$ with weight $0$ is added.
- The modified graph now contains the cycle $A \to B \to A$ with weight:
  $$w(A, B) + w(B, A) = 0 + (-1) = -1 < 0$$
- Bellman-Ford executed from $A$ detects this negative cycle and terminates with an error, failing to compute the all-pairs shortest paths.

---

## Puzzle 2: Minimum Spanning Tree Algorithms [8 Marks]

### Problem Statement
For each algorithm below (`MAYBE-MST-A` and `MAYBE-MST-C`), explain whether the algorithm correctly computes the minimum spanning tree of a graph $G$ with edge weight function $w$.

```text
MAYBE-MST-A(G, w) {
1   sort the edges into nonincreasing order of edge weights w;
2   T <- E;
3   for each edge e, taken in nonincreasing order by weight {
4       do if T - {e} is a connected graph
5           then T <- T - {e};
6   }
7   return T;
8 }
```

```text
MAYBE-MST-C(G, w) {
1   T <- \emptyset;
3   for each edge e, taken in arbitrary order by weight {
4       do T <- T \cup {e};
5           if T has a cycle c {
6               Then let e' be a maximum-weight edge on c;
7               T <- T - {e'};
8           }
9   }
10  return T;
11 }
```

---

### MAYBE-MST-A (Reverse-Delete Algorithm)
**Verdict:** **CORRECT (Computes an MST).**

#### Proof of Correctness:
Algorithm `MAYBE-MST-A` is the well-known **Reverse-Delete Algorithm**. Its correctness rests on two fundamental graph properties:

1. **$T$ is a Spanning Tree:**
   - **Connectedness:** $T$ starts as $E$, which connects all vertices of $G$ (assuming $G$ is connected). An edge $e$ is removed at line 5 **if and only if** $T \setminus \{e\}$ remains connected. Hence, connectivity is an invariant throughout execution, and the final $T$ is connected.
   - **Acyclicity:** Suppose for contradiction that upon termination, $T$ contains a cycle $C$. Let $e_{\max} \in C$ be the maximum-weight edge in $C$. When $e_{\max}$ was considered in the loop, all other edges of $C$ were present in $T$ (since any edge in $C$ has weight $\le w(e_{\max})$ and thus had not been processed yet). Therefore, removing $e_{\max}$ would not have disconnected $T$, because the remaining path $C \setminus \{e_{\max}\}$ still connects the endpoints of $e_{\max}$. Hence, $e_{\max}$ must have been removed at line 5. This contradicts the assumption that $C \subseteq T$.
   - Since $T$ is connected and acyclic, $T$ is a spanning tree.

2. **Optimality via the Cycle Property:**
   - **The Cycle Property:** In any cycle $C$ in a graph $G$, the edge with the maximum weight does not belong to any minimum spanning tree (assuming distinct weights; with duplicate weights, there exists at least one MST that does not contain it).
   - In `MAYBE-MST-A`, an edge $e$ is removed if and only if $T \setminus \{e\}$ is connected, which implies that $e$ belongs to some cycle $C$ in the current graph $T$.
   - Because edges are processed in **non-increasing order of weight**, all edges currently in $T$ have weight $\le w(e)$. Thus, $e$ is guaranteed to be a maximum-weight edge of cycle $C$.
   - By the Cycle Property, removing $e$ never eliminates all minimum spanning trees. Therefore, the remaining spanning tree $T$ is a Minimum Spanning Tree.

---

### MAYBE-MST-C (Cycle-Breaking Algorithm)
**Verdict:** **CORRECT (Computes an MST).**

#### Proof of Correctness:
Algorithm `MAYBE-MST-C` is an online cycle-elimination algorithm.

1. **Acyclicity Invariant:**
   - Initially, $T = \emptyset$, which is acyclic.
   - In each iteration, exactly one edge $e$ is added to $T$.
   - Since $T$ was previously a forest (acyclic), adding a single edge $e$ creates **at most one simple cycle** $c$.
   - Lines 5–7 check if a cycle $c$ exists. If so, a maximum-weight edge $e'$ on $c$ is removed.
   - Removing an edge from the unique simple cycle $c$ restores acyclicity. Therefore, at the end of every iteration, $T$ remains a forest.

2. **Spanning Property:**
   - We claim that after processing any subset of edges $E' \subseteq E$, the connected components of $(V, T)$ are identical to the connected components of $(V, E')$.
   - When edge $e = (u, v)$ is added:
     - If $u$ and $v$ were in different components of $T$, no cycle is created, and $e$ merges the two components.
     - If $u$ and $v$ were already in the same component, a cycle $c$ is formed. The algorithm removes the maximum-weight edge $e'$ from $c$. Because $c \setminus \{e'\}$ is a path connecting the endpoints of $e'$, removing $e'$ does not disconnect any vertices that were previously connected.
   - Since $G$ is connected, once all edges in $E$ are processed, $T$ connects all vertices in $G$. Being connected and acyclic, $T$ is a spanning tree.

3. **Optimality via the Cycle Property:**
   - Every edge $e'$ removed by the algorithm is a maximum-weight edge of a cycle $c$ in the subgraph $T \cup \{e\}$.
   - By the **Cycle Property of MSTs**, a maximum-weight edge on any cycle cannot be part of a minimum spanning tree (or there exists an MST that excludes it).
   - Hence, an optimal spanning forest is maintained after each insertion.
   - When all edges have been processed, the resulting spanning tree $T$ has minimum possible weight.

---

## Puzzle 3: Modifying Dijkstra for Longest Simple Paths [8 Marks]

### Problem Statement
**(a) [4 Marks]:** If we were to make the following modifications (Step 1 and Step 2) to Dijkstra’s algorithm, then explain whether it can be used to find the longest simple path (without revisiting a vertex) for an input acyclic undirected graph.
- **Step 1:** Initialize all distances as minus infinity instead of plus infinity.
- **Step 2:** Modify the relaxation condition in the algorithm to update the distance of a vertex $v$ adjacent to the current vertex $u$, if and only if:
  $$\text{dist}[u] + \text{weight}[u, v] > \text{dist}[v]$$

**(b) [4 Marks]:** Will these modifications work if the graph is not acyclic? Why or why not?

---

### Part (a): Acyclic Undirected Graph (Forest/Tree)
**Verdict:** **NO (Cannot be used to find the longest simple path).**

#### Explanations:
1. **Single-Source vs. All-Pairs Nature of Longest Path:**
   - "The longest simple path in an input graph" is an all-pairs problem (finding the diameter of the tree, i.e., the pair of vertices $(x, y)$ that maximizes the distance between them).
   - Dijkstra’s algorithm is fundamentally a **single-source** shortest-path algorithm. Running it from a single arbitrary vertex $s$ only finds paths originating at $s$. The globally longest simple path may lie entirely within a subtree not passing through $s$. A single run of Dijkstra cannot identify the true start vertex of the longest path.

2. **Dijkstra's Extraction Mechanism (`Extract-Min` vs. `Extract-Max`):**
   - The question explicitly specifies **only** Step 1 and Step 2. It does **not** change the priority queue extraction from `Extract-Min` to `Extract-Max`.
   - If `Extract-Min` is retained, the priority queue repeatedly extracts vertices with distance $-\infty$ before any reachable path is traversed, completely breaking the algorithm.
   - Even if modified to `Extract-Max`, Dijkstra’s greedy principle relies on the property that extending a path cannot decrease its cost. For longest paths with arbitrary or negative weights, a path that starts with a smaller initial weight might later have very large weights; greedily finalizing a vertex prevents discovering such paths.

3. **Undirected Edges and Revisiting Vertices:**
   - In an undirected graph, every undirected edge $\{u, v\}$ corresponds to two directed edges $(u, v)$ and $(v, u)$.
   - When relaxing neighbors from $u$, edge $(u, v)$ updates $v$. When $v$ is subsequently explored, without explicit tracking of the parent node, it can attempt to relax back to $u$, leading to cyclic ping-pong updates unless constrained.

---

### Part (b): Graphs with Cycles
**Verdict:** **NO (Will NOT work for graphs with cycles).**

#### Explanations:
1. **NP-Hardness of the Longest Simple Path Problem:**
   - Finding the longest simple path in a general graph containing cycles is a classic **NP-hard** problem (reducible from the Hamiltonian Path problem by assigning unit weights to all edges: a Hamiltonian path of length $|V|-1$ exists if and only if the longest simple path has length $|V|-1$).
   - Since Dijkstra’s algorithm runs in polynomial time ($O(|V|^2)$ or $O(|E| + |V|\log |V|)$), it cannot solve an NP-hard problem unless $\text{P} = \text{NP}$.

2. **Failure of the Greedy Choice Property:**
   - In standard Dijkstra, once a vertex $u$ is extracted from the priority queue and marked visited, its distance is finalized and never updated again.
   - For longest paths, a greedy algorithm that commits to a vertex with the currently highest distance can permanently miss the true longest simple path that reaches the same vertex via a path that had smaller intermediate weights but much larger subsequent weights.

#### Concrete Counterexample:
Consider a directed graph with vertices $V = \{S, A, B\}$:
- Edges:
  - $(S, A)$ with weight $2$
  - $(S, B)$ with weight $1$
  - $(B, A)$ with weight $10$
- We want the longest simple path from $S$ to $A$.
- The true longest simple path is $S \to B \to A$ with length $1 + 10 = 11$.
- **Execution of Modified Dijkstra (using Extract-Max):**
  1. Distances initialized: $\text{dist}[S] = 0$, $\text{dist}[A] = -\infty$, $\text{dist}[B] = -\infty$.
  2. Relax outgoing edges from $S$:
     - $\text{dist}[A] = \max(-\infty, 0 + 2) = 2$
     - $\text{dist}[B] = \max(-\infty, 0 + 1) = 1$
  3. Extract-Max extracts vertex $A$ because $\text{dist}[A] = 2 > \text{dist}[B] = 1$.
  4. Vertex $A$ is marked finalized/visited with $\text{dist}[A] = 2$.
  5. Next, vertex $B$ is extracted with $\text{dist}[B] = 1$. It attempts to relax neighbor $A$:
     $$\text{dist}[B] + w(B, A) = 1 + 10 = 11 > \text{dist}[A]$$
     Because $A$ has already been finalized and visited, Dijkstra cannot reopen $A$.
  6. The algorithm outputs $\text{dist}[A] = 2$, failing to find the true longest path of length $11$.

---

## Puzzle 4: Properties of MSTs and Shortest Paths [12 Marks]

### Problem Statement
**(a) [6 Marks]:** Suppose $G = (V, E)$ is an undirected graph in which each edge has a distinct weight, and $e \in E$ is a particular edge of $G$. Which of the following statements about the minimum spanning trees (MSTs) of $G$ are TRUE and which are FALSE? Explain your answer.
1. If $e$ is the lightest edge of some cycle in $G$, then every MST of $G$ includes $e$.
2. If $e$ is the heaviest edge of some cycle in $G$, then every MST of $G$ excludes $e$.

**(b) [6 Marks]:** Given an undirected, weighted graph $G = (V, E)$ with non-negative edge weights, we can compute a minimum cost spanning tree $T = (V, E_0)$ of $G$. We can also compute, for a given source vertex $s \in V$, the shortest paths from $s$ to every other vertex in $V$. We now increase the weight of every edge in the graph by $1$.
Are the following TRUE or FALSE, regardless of the structure of $G$? Give a mathematically sound argument if you claim the statement is TRUE, or a counterexample if you claim the statement is FALSE.
1. $T$ is still a minimum cost spanning tree of $G$.
2. All the shortest paths from $s$ to all the other vertices are unchanged.

---

### Part (a): Cycle Properties with Distinct Edge Weights

#### 1. "If $e$ is the lightest edge of some cycle in $G$, then every MST of $G$ includes $e$."
**Verdict:** **FALSE.**

##### Counterexample:
Consider a graph with 4 vertices $V = \{A, B, C, D\}$ and distinct edge weights:
- Cycle $C_1$: $A - B - C - A$ with edge weights:
  - $w(A, B) = 10$
  - $w(B, C) = 12$
  - $w(C, A) = 14$
- Here, $e = (A, B)$ is the **lightest edge in cycle $C_1$** ($10 < 12 < 14$).
- Now add vertex $D$ connected to $A$ and $B$:
  - $w(A, D) = 1$
  - $w(D, B) = 2$
- This introduces cycle $C_2$: $A - D - B - A$ with edge weights $1, 2, 10$.
- In cycle $C_2$, the edge $e = (A, B)$ is the **heaviest edge** ($10 > 2 > 1$).
- The unique MST of this 4-vertex graph requires 3 edges:
  $$\text{MST} = \{(A, D), (D, B), (B, C)\} \quad \text{with total weight } 1 + 2 + 12 = 15$$
- Edge $e = (A, B)$ is **excluded** from the MST.
- Therefore, being the lightest edge of *some* cycle does not guarantee inclusion in the MST.

---

#### 2. "If $e$ is the heaviest edge of some cycle in $G$, then every MST of $G$ excludes $e$."
**Verdict:** **TRUE.**

##### Proof (The Cycle Property of MSTs):
Because all edge weights are distinct, the graph has a unique MST, denoted $T$.

1. Assume for contradiction that $e = (u, v)$ is the strictly heaviest edge in some simple cycle $C$, but $e \in T$.
2. If we remove $e$ from $T$, the tree $T$ splits into exactly two connected components, $T_1$ and $T_2$. This partition defines a cut $(S, V \setminus S)$ such that $u \in S$ and $v \in V \setminus S$.
3. The remaining edges in the cycle $C$, namely $C \setminus \{e\}$, form a simple path connecting $u \in S$ to $v \in V \setminus S$.
4. By the intermediate value property of cuts, this path must cross the cut $(S, V \setminus S)$ at least once. Therefore, there exists another edge $e' \in C$ ($e' \ne e$) that crosses the cut $(S, V \setminus S)$.
5. Since $e$ is the strictly heaviest edge in cycle $C$, we have:
   $$w(e') < w(e)$$
6. Construct a new spanning tree $T' = (T \setminus \{e\}) \cup \{e'\}$.
   - Since $e'$ connects the two disconnected components $T_1$ and $T_2$, $T'$ is connected and contains $|V|-1$ edges; thus, $T'$ is a valid spanning tree.
   - The total weight of $T'$ is:
     $$w(T') = w(T) - w(e) + w(e') < w(T)$$
7. This strictly smaller weight contradicts the assumption that $T$ is a minimum spanning tree.
8. Hence, $e$ cannot belong to any MST; every MST excludes $e$.

---

### Part (b): Uniform Weight Shift (+1 to all edges)

#### 1. "$T$ is still a minimum cost spanning tree of $G$."
**Verdict:** **TRUE.**

##### Mathematical Proof:
Let $n = |V|$ be the number of vertices in $G$.
Every spanning tree $T'$ of $G$ contains **exactly $n - 1$ edges**.

When the weight of every edge $e \in E$ is increased by $1$, the new weight function is $w'(e) = w(e) + 1$.
The cost of any arbitrary spanning tree $T'$ under $w'$ is:
$$w'(T') = \sum_{e \in T'} w'(e) = \sum_{e \in T'} (w(e) + 1) = \sum_{e \in T'} w(e) + \sum_{e \in T'} 1 = w(T') + (n - 1)$$

Notice that $(n - 1)$ is an **identical additive constant** for every spanning tree of $G$.
Therefore, for any two spanning trees $T_1$ and $T_2$:
$$w'(T_1) \le w'(T_2) \iff w(T_1) + (n - 1) \le w(T_2) + (n - 1) \iff w(T_1) \le w(T_2)$$
The relative order of the weights of all spanning trees remains completely invariant.
Since $T$ minimized the original weight $w(T)$ among all spanning trees, $T$ also minimizes the new weight $w'(T)$.
Hence, $T$ remains a minimum cost spanning tree of $G$.

---

#### 2. "All the shortest paths from $s$ to all the other vertices are unchanged."
**Verdict:** **FALSE.**

##### Counterexample:
Unlike spanning trees, different paths between the same pair of vertices can contain **different numbers of edges**.
If path $P$ contains $k$ edges, its new cost increases by $k$:
$$w'(P) = w(P) + k$$
Paths with more edges are penalized more heavily than paths with fewer edges.

Consider the following graph with source $s$ and destination $t$:
- **Path 1 (1 edge):** Direct edge $(s, t)$ with weight $w(s, t) = 4$.
- **Path 2 (3 edges):** Path $s \to u \to v \to t$ with edge weights:
  - $w(s, u) = 1$
  - $w(u, v) = 1$
  - $w(v, t) = 1$
  - Total weight of Path 2 = $1 + 1 + 1 = 3$.

**Initial State:**
- $\text{Cost}(\text{Path 1}) = 4$
- $\text{Cost}(\text{Path 2}) = 3$
- The unique shortest path from $s$ to $t$ is **Path 2** ($3 < 4$).

**After Increasing Every Edge Weight by 1:**
- New weight of $(s, t) = 4 + 1 = 5$ $\implies \text{Cost}'(\text{Path 1}) = 5$.
- New weights on Path 2: $w'(s, u) = 2$, $w'(u, v) = 2$, $w'(v, t) = 2$ $\implies \text{Cost}'(\text{Path 2}) = 2 + 2 + 2 = 6$.
- Now, $\text{Cost}'(\text{Path 1}) = 5 < 6 = \text{Cost}'(\text{Path 2})$.
- The unique shortest path has **switched from Path 2 to Path 1**!
Therefore, shortest paths do not necessarily remain unchanged.

---

## Puzzle 5: Friendship Queries and Equivalence Classes [5 Marks]

### Problem Statement
We are given the data of $n$ individuals, and a set of friendships for certain pairs of individuals. For example, say $n = 10$, and $a, b, c, d, e, f, g, h, i, j$ are the individuals. Their friendships are as follows:
$$a \leftrightarrow b, \quad b \leftrightarrow d, \quad c \leftrightarrow f, \quad c \leftrightarrow i, \quad j \leftrightarrow e, \quad g \leftrightarrow j$$

An individual is a friend of another individual if they are either directly friends, or if they are mutual friends via a transitive relation. In the example above, $a$ is a friend of $d$, since $a$ is friends with $b$, and $b$ is friends with $d$.
How would you store the data above so that you can quickly answer queries whether some individual $x$ is friends with an individual $y$?

---

### Optimal Data Structure: Disjoint Set Union (DSU)
The friendship relation possesses the following properties:
1. **Reflexive:** Every individual is friends with themselves.
2. **Symmetric:** $x \leftrightarrow y \implies y \leftrightarrow x$.
3. **Transitive:** If $x$ is friends with $y$ and $y$ is friends with $z$, then $x$ is friends with $z$.

Therefore, the friendship relation is an **equivalence relation**, and the set of individuals is partitioned into disjoint equivalence classes (connected components).

The optimal data structure to represent this is **Disjoint Set Union (DSU) / Union-Find** with:
1. **Union by Rank (or Size):** Keeps trees balanced by attaching the shallower tree under the root of the deeper tree.
2. **Path Compression:** Flattens the tree during `Find` operations by pointing all examined nodes directly to the root.

---

### Step-by-Step Construction & Query Processing

#### Core Operations:
1. `MakeSet(x)`: Initializes `parent[x] = x` and `rank[x] = 0` for each of the $n$ individuals.
2. `Find(x)`: Recursively follows parent pointers to find the representative root of $x$, applying path compression along the way:
   ```text
   Find(x):
       if parent[x] != x:
           parent[x] = Find(parent[x])
       return parent[x]
   ```
3. `Union(x, y)`: Merges the sets containing $x$ and $y$:
   ```text
   Union(x, y):
       root_x = Find(x)
       root_y = Find(y)
       if root_x != root_y:
           if rank[root_x] < rank[root_y]:
               parent[root_x] = root_y
           else if rank[root_x] > rank[root_y]:
               parent[root_y] = root_x
           else:
               parent[root_y] = root_x
               rank[root_x] = rank[root_x] + 1
   ```
4. `IsFriend(x, y)`:
   ```text
   IsFriend(x, y):
       return Find(x) == Find(y)
   ```

#### Application to the Given Example:
- **Given friendships:** $(a, b), (b, d), (c, f), (c, i), (j, e), (g, j)$
- Performing `Union` operations merges the elements into 4 disjoint equivalence classes:
  1. Component 1: $\{a, b, d\}$
  2. Component 2: $\{c, f, i\}$
  3. Component 3: $\{e, g, j\}$
  4. Component 4: $\{h\}$ (unconnected)

#### Answering Queries:
- `IsFriend(a, d)`: $\text{Find}(a) == \text{Find}(d)$ $\implies$ **True** (since both belong to $\{a, b, d\}$).
- `IsFriend(e, g)`: $\text{Find}(e) == \text{Find}(g)$ $\implies$ **True** (since both belong to $\{e, g, j\}$).
- `IsFriend(a, c)`: $\text{Find}(a) \ne \text{Find}(c)$ $\implies$ **False** (different sets).
- `IsFriend(h, b)`: $\text{Find}(h) \ne \text{Find}(b)$ $\implies$ **False**.

---

### Complexity Analysis
- **Space Complexity:** $O(n)$ to store the `parent` and `rank` arrays.
- **Preprocessing / Construction Time:** $O(m \cdot \alpha(n))$, where $m$ is the number of friendship pairs and $\alpha$ is the inverse Ackermann function ($\alpha(n) \le 4$ for all practical inputs).
- **Query Time:** $O(\alpha(n)) \approx O(1)$ per query.

> **Alternative Static Graph Approach:**  
> If the friendship network is static (no future friendships added), one can run **Breadth-First Search (BFS) / Depth-First Search (DFS)** once in $O(n + m)$ time to assign each connected component an integer `component_id`. Then `IsFriend(x, y)` simply evaluates `component_id[x] == component_id[y]` in strictly **$O(1)$ worst-case time**.  
> DSU is advantageous as it dynamically supports new friendship additions in near-constant time.

---

## Puzzle 6: Gale-Shapley Stable Marriage Analysis [17 Marks]

### Problem Statement
**(a) [6 Marks]:** Suppose that there are $n$ boys and $n$ girls. Your task is to design each boy’s preference list over the $n$ girls, and also each girl’s preference list over the $n$ boys, such that the Gale-Shapley algorithm with boys proposing to girls will make at least $n(n+1)/2$ proposals before reaching a stable marriage. Explain your solution.  
*[Hint: Try to devise the preference lists in such a manner that the first girl receives a proposal from only 1 boy, the second girl receives 2 proposals, the third girl receives 3 proposals, and so on, the last girl (that is, the $n^{\text{th}}$ girl) receives proposals from all the $n$ boys. Then, sum up the total number of proposals.]*

**(b) [5 Marks]:** Suppose that there are 3 boys (labelled $1, 2, 3$) and 3 girls (labelled $A, B, C$). Here are their preference lists:
- Girl $A$: $2, 1, 3$
- Girl $B$: $3, 2, 1$
- Girl $C$: $1, 3, 2$
- Boy $1$: $B, A, C$
- Boy $2$: $C, B, A$
- Boy $3$: $A, C, B$

Suppose girl $A$ marries boy $1$, girl $B$ marries boy $2$, and girl $C$ marries boy $3$. Does this set of three marriages constitute a stable marriage? Why or why not? If it is not a stable marriage, then find a stable marriage. If it is a stable marriage, then find a new (different) stable marriage. Explain why your set of marriages is stable.

**(c) [6 Marks]:** The Gale-Shapley algorithm outputs only one of possibly many stable marriages. In this puzzle, we will see how to efficiently check whether a given instance of girls and boys has multiple stable marriages.  
An interesting property of the Gale-Shapley algorithm with boys proposing to girls is that it guarantees that every boy gets the best marriage partner that he can possibly get in a stable marriage. Further, it also guarantees that every girl gets the worst marriage partner that she can possibly get in a stable marriage.  
Using this property, describe and analyse an efficient algorithm to determine whether a set of $n$ boys (given each boy’s preference list over $n$ girls) and $n$ girls (given each girl’s preference list over $n$ boys) has exactly one stable marriage. (Assume that the Gale-Shapley algorithm is already provided to you, and you may use it as a function/sub-routine in your code/pseudo-code.)

---

### Part (a): Preference Construction for $\ge \frac{n(n+1)}{2}$ Proposals

#### Construction Design:
Let the boys be $b_1, b_2, \dots, b_n$ and the girls be $g_1, g_2, \dots, g_n$.

1. **Boys' Preference Lists:**  
   Every boy shares the **exact same** preference ranking over the girls, preferring higher-indexed girls:
   $$\forall i \in \{1, \dots, n\}: \quad g_n > g_{n-1} > g_{n-2} > \dots > g_2 > g_1$$

2. **Girls' Preference Lists:**  
   Every girl prefers boys with higher indices:
   $$\forall j \in \{1, \dots, n\}: \quad b_n > b_{n-1} > b_{n-2} > \dots > b_2 > b_1$$

#### Execution Trace of Gale-Shapley:
Suppose boys propose in increasing order of their indices $b_1, b_2, \dots, b_n$:

- **Round 1:** Boy $b_1$ proposes to his top choice $g_n$. $g_n$ is free and tentatively accepts $b_1$.  
  *(Proposals made to $g_n$: 1)*
- **Round 2:** Boy $b_2$ proposes to $g_n$. Since $g_n$ prefers $b_2 > b_1$, $g_n$ rejects $b_1$ and accepts $b_2$.  
  $b_1$ is now free and proposes to his second choice $g_{n-1}$. $g_{n-1}$ tentatively accepts $b_1$.  
  *(Proposals made to $g_n$: 2; to $g_{n-1}$: 1)*
- **Round 3:** Boy $b_3$ proposes to $g_n$. $g_n$ prefers $b_3 > b_2$, rejecting $b_2$.  
  $b_2$ proposes to $g_{n-1}$. $g_{n-1}$ prefers $b_2 > b_1$, rejecting $b_1$.  
  $b_1$ proposes to his third choice $g_{n-2}$. $g_{n-2}$ tentatively accepts $b_1$.  
  *(Proposals made to $g_n$: 3; to $g_{n-1}$: 2; to $g_{n-2}$: 1)*
- **General Round $k$ ($1 \le k \le n$):**  
  When boy $b_k$ begins proposing:
  - $b_k$ proposes to $g_n$, displacing $b_{k-1}$.
  - $b_{k-1}$ proposes to $g_{n-1}$, displacing $b_{k-2}$.
  - This cascade ripples down until $b_1$ proposes to $g_{n-k+1}$.
  - Exactly $k$ new proposals are triggered during this round.

#### Proposal Count:
- Girl $g_n$ receives proposals from all $n$ boys: $b_1, b_2, \dots, b_n \implies n$ proposals.
- Girl $g_{n-1}$ receives proposals from $n-1$ boys: $b_1, b_2, \dots, b_{n-1} \implies n-1$ proposals.
- $\dots$
- Girl $g_k$ receives proposals from $k$ boys: $b_1, \dots, b_k \implies k$ proposals.
- $\dots$
- Girl $g_1$ receives a proposal from only $b_1 \implies 1$ proposal.

The total number of proposals before termination is:
$$\sum_{k=1}^n k = 1 + 2 + 3 + \dots + n = \frac{n(n+1)}{2}$$
This matches the exact bound requested.

---

### Part (b): Stability Verification & Finding Multiple Stable Matchings

#### 1. Stability Verification of Matching $M_1$:
Proposed matching:
$$M_1 = \{(1, A), (2, B), (3, C)\}$$

A matching is stable if and only if there exists **no blocking pair** $(b, g)$ such that boy $b$ prefers $g$ to his current partner, **and** girl $g$ prefers $b$ to her current partner.

Let us test every boy and the girls he strictly prefers over his wife:
- **Boy 1** is married to $A$.
  - Boy 1's preferences: $B > A > C$.
  - Boy 1 strictly prefers girl $B$ to girl $A$.
  - Does girl $B$ prefer Boy 1 to her husband (Boy 2)?
    - Girl $B$'s preferences: $3 > 2 > 1$.
    - Girl $B$ prefers Boy 2 over Boy 1 ($2 > 1$).
  - Hence, $(1, B)$ is **not** a blocking pair.
- **Boy 2** is married to $B$.
  - Boy 2's preferences: $C > B > A$.
  - Boy 2 strictly prefers girl $C$ to girl $B$.
  - Does girl $C$ prefer Boy 2 to her husband (Boy 3)?
    - Girl $C$'s preferences: $1 > 3 > 2$.
    - Girl $C$ prefers Boy 3 over Boy 2 ($3 > 2$).
  - Hence, $(2, C)$ is **not** a blocking pair.
- **Boy 3** is married to $C$.
  - Boy 3's preferences: $A > C > B$.
  - Boy 3 strictly prefers girl $A$ to girl $C$.
  - Does girl $A$ prefer Boy 3 to her husband (Boy 1)?
    - Girl $A$'s preferences: $2 > 1 > 3$.
    - Girl $A$ prefers Boy 1 over Boy 3 ($1 > 3$).
  - Hence, $(3, A)$ is **not** a blocking pair.

No blocking pairs exist.  
**Conclusion:** $M_1 = \{(1, A), (2, B), (3, C)\}$ **IS A STABLE MARRIAGE.**

---

#### 2. Finding a New (Different) Stable Marriage:
Let us run the **Gale-Shapley algorithm with boys proposing**:
- Boy 1's top choice is $B$. Boy 1 proposes to $B$.
- Boy 2's top choice is $C$. Boy 2 proposes to $C$.
- Boy 3's top choice is $A$. Boy 3 proposes to $A$.

Each girl receives a proposal from a distinct boy on the very first round:
- Girl $A$ receives a proposal from Boy 3.
- Girl $B$ receives a proposal from Boy 1.
- Girl $C$ receives a proposal from Boy 2.

All girls accept immediately. The resulting matching is:
$$M_2 = \{(1, B), (2, C), (3, A)\}$$

##### Proof of Stability for $M_2$:
- In $M_2$, **every boy is matched with his first-choice girl**:
  - Boy 1 gets $B$ (rank 1).
  - Boy 2 gets $C$ (rank 1).
  - Boy 3 gets $A$ (rank 1).
- Because every boy has his most preferred partner, no boy would ever prefer to deviate to any other girl.
- Therefore, no blocking pair can contain any boy, making blocking pairs impossible.
- $M_2$ is strictly stable, and clearly $M_2 \ne M_1$.

*(Note: There also exists a third stable matching $M_3 = \{(1, C), (2, A), (3, B)\}$, which is the girl-optimal matching obtained when girls propose).*

---

### Part (c): Efficient Algorithm for Uniqueness of Stable Marriage

#### Theoretical Foundation:
From the fundamental theorems of stable matching (Gale and Shapley, 1962; McVitie and Wilson, 1971):
1. **Boy-Proposing Gale-Shapley:** Guarantees a **Boy-Optimal** stable matching $M_B$, where every boy receives his best possible partner across all valid stable matchings, and simultaneously every girl receives her worst possible partner (**Girl-Pessimal**).
2. **Girl-Proposing Gale-Shapley:** Guarantees a **Girl-Optimal** stable matching $M_G$, where every girl receives her best possible partner across all valid stable matchings, and every boy receives his worst possible partner (**Boy-Pessimal**).

#### Core Theorem:
An instance of the Stable Marriage Problem has **exactly one stable marriage** if and only if the boy-optimal matching is identical to the girl-optimal matching:
$$M_B = M_G$$

**Proof:**
- $(\implies)$ If there is only one stable matching $M^*$, the set of all stable matchings is the singleton $\{M^*\}$. Thus, the best and worst possible partners for each individual must coincide with their assigned partner in $M^*$. Hence $M_B = M_G = M^*$.
- $(\impliedby)$ Suppose $M_B = M_G = M^*$. For every boy $b$, his best possible stable partner is $M_B(b)$ and his worst possible stable partner is $M_G(b)$. Since $M_B(b) = M_G(b)$, all stable matchings must assign boy $b$ to the exact same partner $M^*(b)$. Since this holds for all $b$, $M^*$ is the unique stable matching.

---

#### Algorithm Description & Pseudocode:

```text
ALGORITHM CheckUniqueStableMarriage(BoysPrefs, GirlsPrefs, n):
    // Step 1: Run Gale-Shapley with boys proposing
    M_B = Gale_Shapley(proposers = Boys, receivers = Girls, BoysPrefs, GirlsPrefs)

    // Step 2: Run Gale-Shapley with girls proposing (roles reversed)
    M_G = Gale_Shapley(proposers = Girls, receivers = Boys, GirlsPrefs, BoysPrefs)

    // Step 3: Compare both matchings
    for each boy b from 1 to n:
        if M_B[b] != M_G[b]:
            return FALSE  // Multiple stable marriages exist

    return TRUE  // Exactly one unique stable marriage exists
```

#### Complexity Analysis:
- **Time Complexity:**
  - Standard Gale-Shapley with $n$ men and $n$ women runs in $O(n^2)$ time.
  - Executing it twice takes $2 \times O(n^2) = O(n^2)$ time.
  - Comparing the arrays $M_B$ and $M_G$ takes $O(n)$ time.
  - **Total Time Complexity:** $O(n^2)$.  
    Since the input consists of $2n$ preference lists of length $n$ (input size $\Theta(n^2)$), this algorithm runs in **optimal linear time relative to the input size**.
- **Space Complexity:** $O(n^2)$ to store preferences and rankings, and $O(n)$ for the matching arrays.

---

## Puzzle 7: Grid Navigation Puzzle & General Pathfinding [12 Marks]

### Problem Statement
**(a) [5 Marks]:** You are given the following $6 \times 6$ grid board with 36 cells. Your task is to start at the cell on the top left (labelled 5) and reach the cell on the bottom right, labelled "END", via a sequence of moves.
The number in each cell indicates how many steps you can move horizontally or vertically from that cell.
For example, if you are at the cell in the $4^{\text{th}}$ row from the top and the $5^{\text{th}}$ column from the left (that is, the cell labelled 2), then you have the following options:
1. Move to the cell 2 steps horizontally to its left (to the cell labelled 1).
2. Move to the cell 2 steps vertically below it (to the cell labelled 3).
3. Move to the cell 2 steps vertically above it (to the cell labelled 2).
Note that you cannot move 2 steps horizontally to its right, since that would take you outside the grid board.

```text
5   3   3   2   4   4
3   3   4   4   2   4
1   4   1   2   4   2
3   4   1   3   2   3
4   3   2   4   4   4
2   3   5   2   3  END
```

Starting at the top left cell of the grid board (labelled 5), is it possible to make a series of moves so that you end up at the bottom right of the grid board (labelled "END")? If it is impossible, explain why. If it is possible, demonstrate a sequence of moves. *(Do not explain your solution using arrows on a grid board, as that will make it untidy.)*

**(b) [7 Marks]:** Suppose you are given an $n \times n$ board (in the form of a 2-dimensional array $A$ of size $n^2$), all of whose entries are positive integers (except the last entry, which you can assume is "END"). You need to compute a route from the top left cell (that is, $A[0][0]$) to the bottom right cell labelled "END" (that is, $A[n-1][n-1]$), if such a route indeed exists.
Design an algorithm that outputs such a route if it exists, and outputs `"IMPOSSIBLE"` otherwise. Your solution will be graded on the correctness of its code/pseudo-code, the analysis of its running time, and the clarity of its explanation.

---

### Part (a): Concrete Solution to the $6 \times 6$ Grid Board
**Verdict:** **YES, it is possible to reach the "END" cell.**

Using 1-indexed coordinates `(row, column)`, where `(1, 1)` is the top-left cell and `(6, 6)` is the bottom-right `"END"` cell, the exact sequence of 13 moves is detailed below:

| Step | Current Position | Value | Direction Taken | Step Length | Destination Cell |
|:---:|:---:|:---:|:---:|:---:|:---:|
| **Start** | **(1, 1)** | 5 | Move RIGHT | 5 | **(1, 6)** |
| 1 | **(1, 6)** | 4 | Move DOWN | 4 | **(5, 6)** |
| 2 | **(5, 6)** | 4 | Move LEFT | 4 | **(5, 2)** |
| 3 | **(5, 2)** | 3 | Move UP | 3 | **(2, 2)** |
| 4 | **(2, 2)** | 3 | Move RIGHT | 3 | **(2, 5)** |
| 5 | **(2, 5)** | 2 | Move DOWN | 2 | **(4, 5)** |
| 6 | **(4, 5)** | 2 | Move DOWN | 2 | **(6, 5)** |
| 7 | **(6, 5)** | 3 | Move UP | 3 | **(3, 5)** |
| 8 | **(3, 5)** | 4 | Move LEFT | 4 | **(3, 1)** |
| 9 | **(3, 1)** | 1 | Move UP | 1 | **(2, 1)** |
| 10 | **(2, 1)** | 3 | Move RIGHT | 3 | **(2, 4)** |
| 11 | **(2, 4)** | 4 | Move DOWN | 4 | **(6, 4)** |
| 12 | **(6, 4)** | 2 | Move RIGHT | 2 | **(6, 6)** |
| **Finish** | **(6, 6)** | **END** | — | — | **Goal Reached!** |

#### Step-by-Step Move Verification:
1. `(1, 1)` has value $5 \implies$ moving right 5 columns lands on `(1, 6)`.
2. `(1, 6)` has value $4 \implies$ moving down 4 rows lands on `(5, 6)`.
3. `(5, 6)` has value $4 \implies$ moving left 4 columns lands on `(5, 2)`.
4. `(5, 2)` has value $3 \implies$ moving up 3 rows lands on `(2, 2)`.
5. `(2, 2)` has value $3 \implies$ moving right 3 columns lands on `(2, 5)`.
6. `(2, 5)` has value $2 \implies$ moving down 2 rows lands on `(4, 5)`.
7. `(4, 5)` has value $2 \implies$ moving down 2 rows lands on `(6, 5)`.
8. `(6, 5)` has value $3 \implies$ moving up 3 rows lands on `(3, 5)`.
9. `(3, 5)` has value $4 \implies$ moving left 4 columns lands on `(3, 1)`.
10. `(3, 1)` has value $1 \implies$ moving up 1 row lands on `(2, 1)`.
11. `(2, 1)` has value $3 \implies$ moving right 3 columns lands on `(2, 4)`.
12. `(2, 4)` has value $4 \implies$ moving down 4 rows lands on `(6, 4)`.
13. `(6, 4)` has value $2 \implies$ moving right 2 columns lands on `(6, 6) = END`.

---

### Part (b): General Algorithm & Pseudocode for $n \times n$ Grid

#### Modeling as a Graph Problem:
The problem can be modeled as finding a path in a directed unweighted graph $G = (V, E)$:
- **Vertices $V$:** Each cell of the $n \times n$ grid is a vertex $(r, c)$, giving $|V| = n^2$.
- **Edges $E$:** For each cell $(r, c)$ with value $k = A[r][c]$, directed edges go to adjacent cells at distance $k$ that lie within board bounds:
  $$\{(r - k, c), (r + k, c), (r, c - k), (r, c + k)\} \cap \{0, \dots, n-1\}^2$$
  Since each cell has at most 4 valid outgoing moves, $|E| \le 4n^2$.

Because all move steps have uniform cost, **Breadth-First Search (BFS)** is the ideal algorithm:
1. It is guaranteed to find a path if one exists.
2. It naturally computes the path with the **minimum number of moves**.
3. It prevents infinite loops caused by cycles by maintaining a `visited` table.

---

#### Algorithm Pseudocode:

```text
ALGORITHM FindGridRoute(A, n):
    // A: 2D array of size n x n
    // Target is cell (n-1, n-1)

    // Data structures for BFS
    create a 2D boolean array visited[n][n] initialized to FALSE
    create a 2D array parent[n][n] initialized to NULL
    create a FIFO queue Q

    // Initialize BFS from top-left cell (0, 0)
    visited[0][0] = TRUE
    enqueue(Q, (0, 0))

    target_reached = FALSE

    while Q is not empty:
        (r, c) = dequeue(Q)

        // Check if destination is reached
        if r == n - 1 and c == n - 1:
            target_reached = TRUE
            break

        k = A[r][c]  // Step length from current cell

        // Define potential moves: UP, DOWN, LEFT, RIGHT
        candidate_moves = [ (r - k, c), (r + k, c), (r, c - k), (r, c + k) ]

        for each (nr, nc) in candidate_moves:
            // Check boundary conditions and unvisited status
            if 0 <= nr < n and 0 <= nc < n:
                if not visited[nr][nc]:
                    visited[nr][nc] = TRUE
                    parent[nr][nc] = (r, c)
                    enqueue(Q, (nr, nc))

    // Reconstruct and output the route
    if not target_reached:
        output "IMPOSSIBLE"
        return

    // Backtrack from (n-1, n-1) to (0, 0) using parent pointers
    route = empty list
    curr = (n - 1, n - 1)
    while curr is not NULL:
        route.append(curr)
        curr = parent[curr.r][curr.c]

    reverse(route)

    // Print the sequence of cells
    for step from 0 to length(route) - 1:
        (r, c) = route[step]
        output "Step " + step + ": (" + r + ", " + c + ") [value = " + A[r][c] + "]"
    
    return route
```

---

#### Complexity and Performance Analysis:

1. **Time Complexity:**
   - **Graph Traversal:** The graph has $|V| = n^2$ vertices and at most $|E| \le 4n^2$ directed edges.
   - BFS visits each vertex at most once and scans its outgoing edges at most once.
   - Enqueue and dequeue operations take $O(1)$ time each.
   - Backtracking and reversing the shortest path takes $O(\text{path length}) \le O(n^2)$ time.
   - **Total Time Complexity:**
     $$O(|V| + |E|) = O(n^2 + 4n^2) = O(n^2)$$
     Since the input grid contains $n^2$ numbers, this algorithm runs in **optimal linear time relative to the size of the input**, $\Theta(n^2)$.

2. **Space Complexity:**
   - The `visited` matrix requires $n \times n = O(n^2)$ booleans.
   - The `parent` matrix requires $n \times n = O(n^2)$ coordinate pairs.
   - The BFS queue holds at most $O(n^2)$ elements at any point.
   - **Total Space Complexity:** $O(n^2)$ auxiliary space.
