# Ultimate Last-Day Exam Revision Master Guide
## Advanced Data Structures & Algorithms: Graphs, Trees, Shortest Paths & MSTs
### Specially Designed for High-Difficulty Exam Problems, Proofs, Fallacies & Counterexamples

---

# Table of Contents
1. [Exam Architecture: The 7 Core Question Archetypes](#1-exam-architecture-the-7-core-question-archetypes)
2. [Breadth-First Search (BFS): Invariants, Fallacies & Diameter](#2-breadth-first-search-bfs-invariants-fallacies--diameter)
3. [Depth-First Search (DFS): Edge Classification, Leaf Theorem & Timestamp Rules](#3-depth-first-search-dfs-edge-classification-leaf-theorem--timestamp-rules)
4. [Topological Sort: Order Invariants, Edge Additions & Multinomial Counting](#4-topological-sort-order-invariants-edge-additions--multinomial-counting)
5. [Strongly Connected Components (SCC): Condensation DAGs, Transpose Duality & Kosaraju](#5-strongly-connected-components-scc-condensation-dags-transpose-duality--kosaraju)
6. [Single-Source Shortest Paths I: Bellman-Ford, Relaxation Order & Tree Divergence](#6-single-source-shortest-paths-i-bellman-ford-relaxation-order--tree-divergence)
7. [Single-Source Shortest Paths II: Dijkstra, Negative Edge Traces & Dial's Algorithm](#7-single-source-shortest-paths-ii-dijkstra-negative-edge-traces--dials-algorithm)
8. [All-Pairs Shortest Paths (APSP): Floyd-Warshall Loop Invariance, In-Place Proof & Johnson's](#8-all-pairs-shortest-paths-apsp-floyd-warshall-loop-invariance-in-place-proof--johnsons)
9. [Binary Trees, BSTs & Segment Trees: Traversals, Successor Splicing & Complexity](#9-binary-trees-bsts--segment-trees-traversals-successor-splicing--complexity)
10. [Minimum Spanning Trees (MST): Cut/Cycle Properties, Reverse-Delete & Optimization](#10-minimum-spanning-trees-mst-cutcycle-properties-reverse-delete--optimization)
11. [Master Algorithmic Complexity & Property Matrix](#11-master-algorithmic-complexity--property-matrix)
12. [High-Yield "Trap & Counterexample" Rapid-Fire Revision Bank](#12-high-yield-trap--counterexample-rapid-fire-revision-bank)

---

# 1. Exam Architecture: The 7 Core Question Archetypes

Based on extensive analysis of university exam papers (Midsems, Endsems, Slip Tests, Class Tests), graph and tree exam questions test conceptual depth through **seven recurring archetypes**:

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                        7 CORE EXAM QUESTION ARCHETYPES                                 │
├────────────────────────────────────────────────────────────────────────────────────────┤
│ 1. Minimal Counterexample Discovery: Prove why a naive heuristic or variation fails.   │
│ 2. Algorithmic Execution Traces with Negative Edges: Trace exact outputs vs. true values│
│ 3. Invariant & Structural Proofs: Prove topological facts (Leaf Theorem, Condensation) │
│ 4. "MAYBE-ALG" Variations: State whether a modified algorithm is correct or false.     │
│ 5. Extremal Optimization & Constraint Equations: Bound sums of edges (e.g. K4 MST sum).│
│ 6. Combinatorial Counting on DAGs: Multinomial path interleavings of topological sorts.│
│ 7. Transpose & Duality Theorems: Dual properties of G and G^T under condensation.     │
└────────────────────────────────────────────────────────────────────────────────────────┘
```

---

# 2. Breadth-First Search (BFS): Invariants, Fallacies & Diameter

## Core Mechanics & 3-Coloring Invariant
- **WHITE:** Undiscovered ($d = \infty, \pi = \text{NIL}$).
- **GREY:** Frontier queue ($Q$). Discovered, but adjacency list not fully scanned.
- **BLACK:** Dequeued and fully explored.
- **The Non-Negotiable Invariant:** A **BLACK** vertex can **NEVER** have an edge to a **WHITE** vertex. By the time $u$ turns black, all its neighbors have been colored grey or black.

## Queue Monotonicity Lemma
At any point during execution, if queue contains $Q = \langle v_1, v_2, \dots, v_r \rangle$:
$$d[v_1] \le d[v_2] \le \dots \le d[v_r] \quad \text{and} \quad d[v_r] \le d[v_1] + 1$$
The queue contains vertices from at most **two consecutive levels**: $\{k, k + 1\}$.

## Undirected Level-Difference Lemma
For every edge $(u, v) \in E$ in an undirected graph:
$$|d[u] - d[v]| \le 1$$
- **Cross Edges** exist only within the **same level** ($d[u] = d[v]$) or between **adjacent levels** ($|d[u] - d[v]| = 1$).
- **No Back Edges** and **No Forward Edges** can ever exist in undirected BFS!

## Directed Edge Classification
- **Tree Edges:** $v$ is WHITE when $(u, v)$ is scanned.
- **Back Edges:** Ancestor in BFS tree ($d[v] \le d[u]$).
- **Cross Edges:** Neither ancestor nor descendant.
- **Forward Edges:** **CANNOT EXIST!** If $u \to v$ existed with $d[v] \ge d[u] + 2$, scanning $u$ would have discovered $v$ at $d[u] + 1$, preventing distance $\ge d[u] + 2$.

---

## The Graph Diameter Fallacy (ClassPractice Q5c)
> **Fallacy:** The depth of a BFS tree on an undirected graph from an arbitrary vertex $v$ equals the graph diameter.
> **Truth:** **FALSE.** Depth from $v$ is only the **eccentricity** $\epsilon(v) = \max_u \delta(v, u)$.

$$\text{Radius}(G) \le \text{depth}(v) \le \text{Diameter}(G) \le 2 \cdot \text{depth}(v)$$
- **Minimal Counterexample:** Line graph $P_5$: $v_1 - v_2 - v_3 - v_4 - v_5$.
  - True diameter $= \delta(v_1, v_5) = 4$.
  - Start BFS at center $v_3$: BFS depth is **2** (underestimates diameter by $2\times$!).

### Why Double-BFS (2-BFS) Works on Trees vs. Fails on General Graphs:
1. **On Trees:** Double-BFS (BFS from arbitrary $u \to$ farthest $v$; BFS from $v \to$ farthest $w$) is **provably correct** because trees are acyclic with unique simple paths.
2. **On General Graphs with Cycles:** **FAILS.**
   - **Minimal 6-Vertex Counterexample:** 4-cycle $C_4$ ($A - B - C - D - A$) with tails $x - A$ and $y - C$.
   - True diameter: $\delta(x, y) = 4$ ($x - A - B - C - y$).
   - Start BFS 1 at $B$: farthest nodes at distance 2 are $\{x, y, D\}$. If tie-breaking picks $D$:
   - BFS 2 starts at $D$: maximum distance from $D$ is **2**!
   - 2-BFS outputs diameter = 2 (discrepancy: true diameter is 4!).

---

## High-Performance BFS Variations
1. **0-1 BFS (Double-Ended Queue):** Edge weights $\in \{0, 1\}$.
   - Weight 0: push to **front** of deque ($d[v] = d[u]$).
   - Weight 1: push to **back** of deque ($d[v] = d[u] + 1$).
   - Time: Strictly linear $\mathbf{O(V + E)}$.
2. **Universal Sink in $O(V)$ on Adjacency Matrix:**
   - Universal sink $u^*$: in-degree $= V - 1$, out-degree $= 0$.
   - Pointers $i = 1, j = 1$. If $A[i, j] == 1$, $i \leftarrow i + 1$ (eliminates $i$); else $j \leftarrow j + 1$ (eliminates $j$).
   - Verification phase takes $2V$ checks. Total time: $\mathbf{\Theta(V)}$ despite $V^2$ matrix size!

---

# 3. Depth-First Search (DFS): Edge Classification, Leaf Theorem & Timestamp Rules

## Edge Classification via Vertex Color on Discovery
When exploring edge $(u, v)$:
- **WHITE:** $\implies$ **Tree Edge** ($v$ becomes child of $u$).
- **GREY:** $\implies$ **Back Edge** ($v$ is an ancestor of $u$; indicates a **CYCLE**).
- **BLACK:**
  - If $d[u] < d[v]$: $\implies$ **Forward Edge** ($v$ is descendant of $u$).
  - If $d[u] > d[v]$: $\implies$ **Cross Edge** ($v$ is in an already-explored subtree/component).

> [!IMPORTANT]
> **Undirected DFS Theorem:** In any undirected DFS, **every non-tree edge is a BACK edge**. Forward and Cross edges **NEVER** exist!

---

## The DFS Shortest Path Failure (Slip Test Q1 Deep-Dive)
> **Question:** Can DFS be used to find single-source shortest paths on unweighted graphs?
> **Answer: NO.**

```
           (s) ────── 1 ──────► (b)
            │                    ▲
           1│                    │1
            ▼                    │
           (a) ──────────────────┘
```
1. **Minimal 3-Vertex Counterexample:** Edges $(s, a)$, $(a, b)$, and $(s, b)$.
   - True shortest distance: $\delta(s, b) = 1$.
   - DFS explores $(s, a)$ first, then $(a, b)$. It marks $b$ visited with $dist[b] = 2$.
   - Edge $(s, b)$ is inspected as a cross/forward edge and ignored because $b$ is already visited.
   - Output: $dist[b] = 2 \neq 1$.
2. **What if we allow updating visited vertices whenever a shorter path is found?**
   - **On DAGs / Graphs without negative cycles:** It eventually converges, but running time explodes to **$O(2^V)$ exponential time** (degenerate diamond lattice graph forces full path enumeration).
   - **On Graphs with Negative Cycles:** It enters an **INFINITE LOOP**!

---

## The DFS Leaf Theorem (ClassPractice Q7)
> **Theorem:** In an undirected graph $G$, let $T$ be a DFS tree. Let $u$ be visited, and let $v$ be the first unvisited vertex visited after $u$. If $(u, v) \notin E$, then **$u$ must be a LEAF in $T$**.

*Proof:*
- When DFS visits $u$, it inspects all incident edges $(u, x)$.
- If $u$ has any unvisited neighbor $w$, DFS immediately traverses $(u, w)$, making $w$ the very next visited vertex, with $(u, w) \in E$.
- If the next visited vertex $v$ has $(u, v) \notin E$, $u$ had **zero unvisited neighbors** when visited.
- Therefore, DFS cannot branch downward from $u$; it backtracks immediately.
- A vertex from which DFS immediately backtracks has 0 children in $T$, proving $u$ is a **leaf**. $\blacksquare$

---

## Timestamp Inequalities & Parenthesis Structure (ClassPractice Q9)
For any two vertices $u$ and $v$ in a DFS forest, their intervals $[d[u], f[u]]$ and $[d[v], f[v]]$ are either **completely disjoint** or **one is strictly nested within the other**:
1. **$v$ is a descendant of $u$:** $d[u] < d[v] < f[v] < f[u]$.
2. **$u$ and $v$ are in disjoint subtrees:** $d[u] < f[u] < d[v] < f[v]$ (or vice versa).
3. **If $d[u] < d[v]$ and $(u, v) \in E$ (directed):** $v$ must finish before $u$ ($f[v] < f[u]$).
4. **If there is a path from $u$ to $v$ and $d[u] < d[v]$:** $f[v] < f[u]$ always holds (White Path Theorem).

---

# 4. Topological Sort: Order Invariants, Edge Additions & Multinomial Counting

## Core Properties of Topological Sorting
- Defined **ONLY** for Directed Acyclic Graphs (DAGs).
- Linear ordering such that for every directed edge $u \to v$, $\text{pos}(u) < \text{pos}(v)$.
- A directed graph has a topological sort $\iff$ it has **NO directed cycles** (no back edges in DFS).

---

## The Append-on-Finish Failure (Slip Test Q3a)
> **Fallacy:** In DFS, appending each vertex to the output list when it finishes (`list.append(u)`) produces a topological sort.
> **Truth: FALSE. It produces the exact REVERSE topological sort!**
- **Minimal 2-Vertex Counterexample:** $u \to v$.
  - DFS visits $u$, then $v$.
  - $v$ has no outgoing edges, so $v$ finishes first: `list.append(v)`.
  - DFS backtracks to $u$, which finishes second: `list.append(u)`.
  - Output list: $\langle v, u \rangle$.
  - Edge points from $u \to v$, but output has $v$ before $u$!
- **Rule:** If $|E| \ge 1$, appending on finish **NEVER** yields a valid topological sort! One must prepend (`list.insert(0, u)`) or reverse the finished list.

---

## Acyclic Edge Addition Theorems (Slip Test Q3b & Q3c)
Let $\langle v_1, v_2, \dots, v_n \rangle$ be a topological sort of DAG $G$.
1. **Forward Edge Addition:** Adding directed edge $v_i \to v_j$ where $i < j$ can **NEVER create a cycle**.
   - *Proof:* The existing topological ordering remains valid for the augmented graph ($i < j$). Since a valid topological order exists, the augmented graph is strictly acyclic.
2. **Adding an Edge from Last to First Vertex ($v_n \to v_1$):**
   - **Does NOT necessarily create a cycle!**
   - *Counterexample:* Disconnected graph with edges $A \to B$ and $C \to D$.
     - Valid topological sort: $\langle A, C, B, D \rangle$. Here $v_1 = A, v_n = D$.
     - Add edge $D \to A$: Graph becomes $C \to D \to A \to B$, which is a single directed path—**completely acyclic**!
   - *Cycle Condition:* Edge $v_n \to v_1$ creates a cycle if and only if there was already a directed path from $v_1 \leadsto v_n$ in the original graph.

---

## Counting Valid Topological Sorts via Multinomial Path Interleaving (ClassPractice Q14)
When a DAG consists of bottleneck nodes and parallel paths:
```
           (B) ──► (C) ──► (D)
          /                   \
        (A) ──────► (F) ──────► (E) ───► ... ───► (N)
          \                   /
           (G) ──────────────┘
```
1. **Bottleneck Decomposition:** If every path from $A$ to $N$ must pass through intermediate bottleneck node $E$, the problem factorizes:
   $$\text{Total Orderings} = (\text{Orderings from } A \to E) \times (\text{Orderings from } E \to N)$$
2. **Multinomial Interleaving Formula:** If stage 1 consists of $k$ independent parallel paths of lengths $l_1, l_2, \dots, l_k$ with $\sum l_i = M$ interior vertices:
   $$\text{Ways} = \binom{M}{l_1, l_2, \dots, l_k} = \frac{M!}{l_1! \cdot l_2! \dots l_k!}$$
- **ClassPractice Q14 Exact Solution:**
  - Stage 1 ($A \to E$): 3 paths with 3, 2, 1 vertices ($M = 6$): $\frac{6!}{3! \cdot 2! \cdot 1!} = \mathbf{60}$.
  - Stage 2 ($E \to N$): 3 paths with 2, 1, 2 vertices ($M = 5$): $\frac{5!}{2! \cdot 1! \cdot 2!} = \mathbf{30}$.
  - **Total Valid Topological Sorts:** $60 \times 30 = \mathbf{1800}$.

---

# 5. Strongly Connected Components (SCC): Condensation DAGs, Transpose Duality & Kosaraju

## Fundamental Definitions & Properties
- An SCC is a maximal set of vertices $C \subseteq V$ such that every pair $u, v \in C$ has $u \leadsto v$ and $v \leadsto u$.
- **The Condensation Graph $G^{\text{SCC}}$ is ALWAYS a DAG.** If $G^{\text{SCC}}$ had a cycle of components $C_1 \to C_2 \to \dots \to C_1$, all vertices in those components would mutually reach each other, collapsing into a single SCC by maximality.

---

## Transpose Duality Theorems (Slip Test 6 Q2 & Q3)
Let $G^T$ be the transpose graph (all edges reversed), and let $G^{\text{SCC}}$ be the condensation DAG.
1. **Theorem 1:** $(G^{\text{SCC}})^T = (G^T)^{\text{SCC}}$ is **TRUE**.
   - The SCCs of $G$ and $G^T$ are identical vertex partitions ($u \leftrightarrow v$ in $G \iff u \leftrightarrow v$ in $G^T$).
   - A component edge $C \to C'$ in $G^{\text{SCC}}$ corresponds to edges $u \to v$ with $u \in C, v \in C'$. In $G^T$, edges reverse ($v \to u$), creating component edge $C' \to C$. Reversing condensation edges equals condensing reversed edges!
2. **Theorem 2:** $((G^T)^{\text{SCC}})^T = G^{\text{SCC}}$ is **TRUE**.
   - Applying transpose involution: $(A^T)^T = A$.

---

## 2-DFS Reachability Intersection (Slip Test Q2)
> **Problem:** Does $R_G(v) \cap R_{G^T}(v)$ correctly identify $\text{SCC}(v)$?
> **Answer: YES.**

- $R_G(v) = \{u \in V : v \leadsto_G u\}$ (nodes reachable from $v$).
- $R_{G^T}(v) = \{u \in V : v \leadsto_{G^T} u\} = \{u \in V : u \leadsto_G v\}$ (nodes that can reach $v$).
- Their intersection $R_G(v) \cap R_{G^T}(v)$ is precisely the set of nodes with mutual reachability $\iff \text{SCC}(v)$.
- **Time Complexity:** Two DFS traversals take $O(V + E)$ to find **one** component.
- **Why Naive 2-DFS is Suboptimal for the Whole Graph:** Finding all $k$ SCCs takes $O(k(V + E)) = \mathbf{O(V^2 + VE)}$, whereas Kosaraju finds all SCCs in a single unified $\mathbf{O(V + E)}$ pass!
- **Starting Vertex Sensitivity:** If DFS 2 starts at $w \neq v$, $R_G(v) \cap R_{G^T}(w)$ finds nodes reachable from $v$ that can reach $w$, which is **NOT an SCC** (can be empty or cross components).

---

## Why Finish Times are Mandatory in Kosaraju
In Kosaraju's algorithm:
1. Run DFS on $G$, recording vertex finish times $f[u]$.
2. Compute $G^T$.
3. Run DFS on $G^T$, processing vertices in **decreasing order of $f[u]$**.

> [!IMPORTANT]
> **Component Finish Time Theorem:** For any edge $C \to C'$ in $G^{\text{SCC}}$, the maximum finish time in $C$ is strictly greater than the maximum finish time in $C'$:
> $$f(C) > f(C')$$
> Therefore, the vertex with the **globally maximum finish time** belongs to a **SOURCE component in $G$**.
> In $G^T$, all component edges reverse, turning that source component into a **SINK component in $G^T$**!
> Running DFS on $G^T$ from a sink component guarantees **zero leakage into other components**.

### Why Discovery Times FAIL in Kosaraju:
- Discovery times $d[u]$ do not satisfy $d(C) > d(C')$.
- **Counterexample:** $C \to C'$. DFS can start in $C'$, discover all nodes in $C'$, finish $C'$, and then explore $C$. Then $d(C') < d(C)$, meaning the minimum discovery time belongs to the sink component. Using discovery times leaks across components, corrupting decomposition!

---

# 6. Single-Source Shortest Paths I: Bellman-Ford, Relaxation Order & Tree Divergence

## Fundamental Relaxation Properties
- **Upper-Bound Property:** $d[v] \ge \delta(s, v)$ always. Once $d[v] = \delta(s, v)$, it never increases.
- **Convergence Property:** If $s \leadsto u \to v$ is a shortest path, and $d[u] = \delta(s, u)$ prior to relaxing $(u, v)$, then $d[v] = \delta(s, v)$ immediately after relaxing $(u, v)$.
- **Path-Relaxation Property:** If $p = \langle v_0, v_1, \dots, v_k \rangle$ is a shortest path, and edges are relaxed in any sequence that contains subsequence $\langle (v_0, v_1), (v_1, v_2), \dots, (v_{k-1}, v_k) \rangle$, then $d[v_k] = \delta(s, v_k)$.

---

## Edge Relaxation Order Invariance (ClassPractice Q15a)
> **Theorem:** Changing the order in which edges are relaxed within each pass of the Bellman-Ford algorithm **NEVER affects correctness**.

- Inductive proof: Iteration $k$ guarantees correct distances for all shortest paths containing $\le k$ edges, regardless of edge list order.
- **The Lucky Permutation:** If edges happen to be relaxed in exact topological / path order, Bellman-Ford computes all shortest paths in **a single pass**! (Remaining $V - 2$ passes make 0 updates).
- Total running time remains $O(VE)$ worst-case.

---

## Bellman-Ford vs. Dijkstra Shortest-Path Tree Divergence (ClassPractice Q5a)
> **Theorem:** On a graph with positive edge weights, Bellman-Ford and Dijkstra **always compute the same shortest-path weights $\delta(s, v)$**, but can produce **DIFFERENT shortest-path trees $\pi$**.

```
           (s) ────── 1 ──────► (a)
            │                    │
           2│                    │1
            ▼                    ▼
           (b) ◄─────────────────┘
```
- Shortest paths to $b$: Path 1 ($s \to b$, cost 2) and Path 2 ($s \to a \to b$, cost $1 + 1 = 2$).
- $\delta(s, b) = 2$ is unique.
- **Dijkstra:** Relaxes $(s, a)$ and $(s, b)$ first. Sets $\pi[b] = s$. When $a$ is extracted, candidate distance $1 + 1 = 2 \ngtr 2$, leaving $\pi[b] = s$.
- **Bellman-Ford:** If edge list relaxes $(a, b)$ after $(s, b)$, it updates $\pi[b] = a$.
- Edge sets $E(T_{\text{Dijkstra}}) \neq E(T_{\text{BF}})$. Both trees are valid, but structurally distinct!

---

# 7. Single-Source Shortest Paths II: Dijkstra, Negative Edge Traces & Dial's Algorithm

## Dijkstra Execution Trace on Graph with Negative Edges (ClassPractice Q13)
Graph with vertices $\{A, B, C, D, E, F, G\}$ and source $A$:
- Edges: $(A, B)=2, (A, C)=7, (A, E)=12, (B, D)=2, (C, B)=3, (C, D)=-1, (C, E)=2, (D, F)=2, (E, A)=-4, (E, G)=-7, (F, G)=2, (G, D)=1$.

### Step-by-Step Priority Queue Trace:
1. **Extract $A$ ($d=0$):** Relax $B (d=2), C (d=7), E (d=12)$. Finalized: $\{A\}$.
2. **Extract $B$ ($d=2$):** Relax $D (d=2+2=4)$. Finalized: $\{A, B\}$.
3. **Extract $D$ ($d=4$):** Relax $F (d=4+2=6)$. Finalized: $\{A, B, D\}$.
4. **Extract $F$ ($d=6$):** Relax $G (d=6+2=8)$. Finalized: $\{A, B, D, F\}$.
5. **Extract $C$ ($d=7$):**
   - $(C, D, -1)$: candidate $7 - 1 = 6 \nless 4$ (no change).
   - $(C, E, 2)$: $d[E] = \min(12, 7 + 2) = \mathbf{9}$. Finalized: $\{A, B, D, F, C\}$.
6. **Extract $G$ ($d=8$):** $(G, D, 1)$: candidate $8 + 1 = 9 \nless 4$. Finalized: $\{A, B, D, F, C, G\}$.
7. **Extract $E$ ($d=9$):**
   - Edge $(E, G, -7)$: candidate $9 - 7 = \mathbf{2}$.
   - **FAILURE POINT:** $G$ is **already in finalized set $S$**! In standard Dijkstra, vertices in $S$ are never re-evaluated.
- **Values Given by Dijkstra (Alphabetical order $A..G$):**
  $$\mathbf{[0, \; 2, \; 7, \; 4, \; 9, \; 6, \; 8]}$$
- **True Shortest Paths $\delta(A, \cdot)$:**
  $$\mathbf{[0, \; 2, \; 7, \; 3, \; 9, \; 5, \; 2]}$$
  *(Dijkstra severely overestimates distances to $D, F, G$ due to greedy premature commitment!)*

---

## Critical Theorems on Negative Edges & Longest Paths (ClassPractice Q15b, c, d)
1. **Can Dijkstra be modified for shortest paths on all-negative graphs?** **NO (FALSE).**
   Paths with more edges become more negative. Greedy monotonic expansion fails completely.
2. **Can Dijkstra be modified for longest paths on all-negative graphs?** **NO (FALSE).**
   Finding longest simple paths in general graphs is **NP-hard**.
3. **Can Dijkstra produce valid shortest paths on graphs with negative edges?** **YES (TRUE).**
   - **Concrete Example:** $s \xrightarrow{5} u \xrightarrow{-2} v$.
   - Dijkstra extracts $s (0) \to u (5) \to v (3)$.
   - Outputs: $d[s]=0, d[u]=5, d[v]=3$—**100% correct** because the negative edge respects topological order without bypassing any finalized vertex!

---

## Why Naive Reweighting ($w + C$) Destroys Shortest Paths
Let path $p_1$ have $k_1$ edges (weight $W_1$) and path $p_2$ have $k_2$ edges (weight $W_2$).
Under reweighting $\omega'(e) = \omega(e) + C$:
$$\omega'(p_1) - \omega'(p_2) = (W_1 - W_2) + (k_1 - k_2) \cdot C$$
Adding $C$ adds **more penalty to paths with more edges**, reversing the shortest path inequality! (To fix this, one must use Johnson's node potentials: $\omega'(u, v) = \omega(u, v) + h(u) - h(v)$).

---

## Dial's Algorithm & Layered Graphs
1. **Dial's Algorithm (Bucket Queue):** Integer edge weights in $\{1, \dots, W\}$.
   - Array of $W + 1$ circular buckets.
   - `Decrease-Key`: $O(1)$. Total pointer advances: $O(WV)$.
   - Running time: $\mathbf{O(E + WV)}$. When $W = O(1)$, runs in strictly linear $\mathbf{O(E)}$ time!
2. **Shortest Path with at Most $K$ Edges:**
   - **Dynamic Programming (Truncated Bellman-Ford):** $K$ passes in $\mathbf{O(K \cdot E)}$ time.
   - **Layered Graph Dijkstra:** $K + 1$ vertex copies in $\mathbf{O(K E \log(K V))}$ time.

---

# 8. All-Pairs Shortest Paths (APSP): Floyd-Warshall Loop Invariance, In-Place Proof & Johnson's

## Floyd-Warshall Dynamic Programming Recurrence
Let $d_{ij}^{(k)}$ be the shortest path from $i$ to $j$ using intermediate vertices exclusively from $\{1, \dots, k\}$:
$$d_{ij}^{(k)} = \min \left( d_{ij}^{(k-1)}, \; d_{ik}^{(k-1)} + d_{kj}^{(k-1)} \right)$$

---

## Why the $k$-Loop MUST be Outermost
Consider path $1 \to 2 \to 3 \to 4$ with all weights = 1.
If a programmer mistakenly places $i$ outermost:
```text
for i = 1 to n do         // WRONG!
    for j = 1 to n do
        for k = 1 to n do
            D[i, j] = min(D[i, j], D[i, k] + D[k, j])
```
- When $i = 1, j = 4$: $D[1, 4] = \min_k(D[1, k] + D[k, 4])$.
- For $k = 2$: $D[2, 4]$ has **not been computed yet** ($i = 2$ hasn't executed!).
- The algorithm finishes $i = 1$ with $D[1, 4] = \infty$ and never revisits it!
- **Reason:** The outer loop $k$ is the **induction variable**. Phase $k$ must complete globally across all pairs before phase $k + 1$ can use vertex $k$.

---

## Arbitrary Permutation Invariance & In-Place Correctness
1. **Arbitrary Permutation Theorem:** Intermediate vertices can be processed in **any arbitrary permutation $\sigma$** of $\{1, \dots, n\}$. As long as the outer loop iterates through all $n$ vertices, $S_n = V$, guaranteeing optimal shortest paths!
2. **In-Place Update Proof (No Race Conditions):**
   In iteration $k$, does updating $D[i, j]$ in-place overwrite values needed for other pairs?
   - Consider entries in row $k$ and column $k$:
     $$D[k, j] = \min(D[k, j], \; D[k, k] + D[k, j]) = \min(D[k, j], \; 0 + D[k, j]) = D[k, j]$$
     $$D[i, k] = \min(D[i, k], \; D[i, k] + D[k, k]) = \min(D[i, k], \; D[i, k] + 0) = D[i, k]$$
   - **Row $k$ and Column $k$ NEVER change during phase $k$!**
   - Therefore, reading in-place reads the exact same values as reading from a previous copy $D^{(k-1)}$. Memory drops from $O(V^3) \to \mathbf{\Theta(V^2)}$.

---

## Negative Cycle Rules in APSP
1. **Floyd-Warshall Detection:** Negative cycle exists $\iff$ **diagonal entry $D[i, i] < 0$**.
2. **Undirected Negative Weight Rule:** In an undirected graph, a single negative edge $(u, v)$ with weight $-w < 0$ forms an instant **negative 2-cycle**:
   $$u \to v \to u \quad \text{with weight } (-w) + (-w) = -2w < 0$$
   Shortest paths are instantly undefined ($-\infty$) for the entire component!

---

## Bitset Transitive Closure & Dynamic Updates
1. **Bitset Warshall:** Using bitwise `OR` across 64-bit words:
   $$\text{If } T[i, k] == 1 \implies \text{row}_i = \text{row}_i \lor \text{row}_k$$
   Runs in $\mathbf{O(V^3 / 64)}$ time (under 15 ms for $V = 1000$).
2. **Dynamic Edge Decrease in $O(V^2)$:** If edge $(u, v)$ decreases to $w'$:
   $$D'[i, j] = \min \left( D[i, j], \; D[i, u] + w' + D[v, j] \right) \quad \forall i, j$$
3. **Johnson's Algorithm for Sparse Graphs:**
   - Add dummy source $s$ with 0-weight edges to all nodes. Run Bellman-Ford from $s$ to compute potentials $h(v)$.
   - Reweight: $\hat{\omega}(u, v) = \omega(u, v) + h(u) - h(v) \ge 0$.
   - Run Dijkstra $\times V$ on non-negative weights $\hat{\omega}$.
   - Time: $\mathbf{O(V^2 \log V + VE)}$ (optimal for sparse graphs with negative weights).

---

# 9. Binary Trees, BSTs & Segment Trees: Traversals, Successor Splicing & Complexity

## Binary Tree Fundamentals
- **Full Binary Tree Leaf Theorem:** $L = I + 1$, $n = 2I + 1 = 2L - 1$.
- **Null Pointers in $n$-node Binary Tree:** Exactly $\mathbf{n + 1}$ `NIL` pointers (since $2n - (n - 1) = n + 1$).

---

## Traversal Reconstruction Theorems (GATE Classics)
1. **Inorder + Preorder $\implies$ UNIQUE Reconstruction.**
2. **Inorder + Postorder $\implies$ UNIQUE Reconstruction.**
3. **Preorder + Postorder $\implies$ FAILS in General!**
   - **Minimal 2-Node Counterexample:**
     - Tree 1: Root $A$, left child $B$. Preorder: $[A, B]$, Postorder: $[B, A]$.
     - Tree 2: Root $A$, right child $B$. Preorder: $[A, B]$, Postorder: $[B, A]$.
     - Identical traversals, distinct tree topologies!
   - **Exception:** Preorder + Postorder **SUCCEEDS if and only if the tree is strictly a Full Binary Tree** (every internal node has exactly 2 children).

---

## BST Successor & Deletion Mechanics
1. **Tree-Successor:**
   - **Case 1 (Right subtree exists):** $\text{Min}(x.right)$ (leftmost node in right subtree).
   - **Case 2 (Right subtree is NIL):** Climb parent pointers until reaching ancestor $y$ where current node is in $y$'s **left** subtree.
2. **Tree-Delete Case 3 (Node $z$ has 2 children):**
   - Replace $z$ with its inorder successor $y = \text{Tree-Min}(z.right)$.
   - **Crucial Invariant:** Successor $y$ has **at most 1 child** ($y.left == \text{NIL}$ always!). Splicing $y$ out of its original position is always a simple Case 1 or Case 2 deletion.

---

## Segment Trees (ClassPractice Q8)
- **Time to Build:** $\mathbf{\Theta(N)}$.
  - Recurrence: $T(N) = 2T(N/2) + O(1)$. By Master Theorem Case 1: $T(N) = \Theta(N)$.
  - A segment tree has $N$ leaves and $N - 1$ internal nodes ($2N - 1$ total nodes), with $O(1)$ merging per internal node.
- **Space Complexity:** $\mathbf{\Theta(N)}$ (bounded by **$4N$** in implicit array indexing).
- **Range Query & Point Update:** $\mathbf{O(\log N)}$.

---

# 10. Minimum Spanning Trees (MST): Cut/Cycle Properties, Reverse-Delete & Optimization

## Core Spanning Tree Axioms
1. **Cut Property (Green Rule):** For any cut $(S, V \setminus S)$, the **strictly lightest edge crossing the cut belongs to every MST**.
2. **Cycle Property (Red Rule):** For any simple cycle $C$, the **strictly heaviest edge on $C$ CANNOT belong to any MST**.

---

## Kruskal's Algorithm & Disjoint-Set / Union-Find (ClassPractice Q2 & Q3)
- Kruskal sorts edges and uses **Union-Find** to prevent cycles:
  `MAKE-SET` ($V$), `FIND-SET` ($2E$), `UNION` ($V - 1$).
  With Path Compression and Union by Rank: $\mathbf{O(E \log V + E \alpha(V)) = O(E \log V)}$.
- **ClassPractice Q3 Wheel Graph Trace (7 Nodes):**
  - Center $D$, outer cycle $A, B, E, G, F, C$.
  - Edges added by Kruskal in exact order:
    $$\mathbf{(A, D) \to (E, G) \to (B, D) \to (D, E) \to (F, G) \to (A, C)}$$
  - Rejected cycle edges: $(B, E)=15$, $(D, G)=16$, $(A, B)=20$.
  - Total MST Weight $= 1 + 3 + 4 + 9 + 17 + 23 = \mathbf{57}$.

---

## Negative Weights, Reverse-Delete & Doubling Edge Costs (ClassPractice Q4)
1. **Prim & Kruskal with Negative Weights:** **WORK 100% CORRECTLY.** MST correctness depends purely on relative edge orderings, not sign!
2. **Reverse-Delete Algorithm (Reversed Kruskal):**
   - Start with $T = E$.
   - Iterate edges in descending order (largest to smallest).
   - If deleting $e$ leaves $T$ connected, delete $e$.
   - **Correctness:** Provably correct by the Cycle Property (deletes heaviest edge on cycles).
3. **Doubling All Edge Weights:**
   - Order of weights is unchanged $\implies$ **MST edges remain identical**!
   - New cost $= 2 \times \text{old\_cost}$, computable in **$O(1)$ constant time** (NOT $\Omega(E)$!).

---

## Weight Squaring Invariance (ClassPractice Q11)
> **Theorem:** For any graph with positive edge weights, squaring all edge weights ($w' = w^2$) **preserves the exact same Minimum Spanning Tree**.
- Proof: $f(x) = x^2$ is strictly monotonically increasing for $x > 0$. Relative order of every cut crossing is preserved.
- **MST Cycle Fallacy:** A spanning tree **NEVER** consists of a cycle, regardless of whether weights are positive, negative, or zero!

---

## Complete Graph $K_4$ Edge Sum Minimization (ClassPractice Q12)
> **Problem:** Complete graph $K_4$ (6 edges), all edge weights $> 0$, one edge has weight 4, MST has weight 10. Find the **minimum possible sum of all 6 edges**.

```
                 (1) ──────── (2)
                  │ \        / │
                  │   \    /   │
                  │     ╳      │
                  │   /    \   │
                  │ /        \ │
                 (3) ──────── (4)
```
- MST has 3 edges: $w_1 + w_2 + w_3 = 10$.
- Non-tree edges: $e_4, e_5, e_6$.
- By Cycle Property, each non-tree edge $e \ge$ max edge on its fundamental cycle.
- **Triangle Rejection Configuration:**
  - Let vertices $A, B, C$ form triangle: $(A, B)=w_1, (B, C)=w_2$.
  - Edge $(A, C)$ closes cycle $A - B - C - A \implies \omega(A, C) \ge \max(w_1, w_2) = w_2$.
  - Vertex $D$ connects via tree edge $(C, D) = w_3$.
  - Non-tree edges to $D$: $(A, D) \ge w_3, (B, D) \ge w_3$.
  - Sum of all 6 edges: $\sum \omega = 10 + \omega(A, C) + \omega(A, D) + \omega(B, D) \ge 10 + w_2 + 2w_3$.
  - Set $w_3 = 4$ (given edge 4!). Then $w_1 + w_2 = 10 - 4 = 6 \implies w_2 \ge 3$.
  - With integer weights: $w_1 = 3, w_2 = 3, w_3 = 4$.
  - Non-tree edges: $\omega(A, C) = 3, \; \omega(A, D) = 4, \; \omega(B, D) = 4$.
  - **Minimum Total Sum:** $3 + 3 + 4 + 3 + 4 + 4 = \mathbf{21}$.

---

## Analysis of Modified MST Algorithms (ClassPractice Q16)
1. **`MAYBE-MST-A` (Reverse-Delete):**
   Sort non-increasing, $T = E$, delete $e$ if $T - \{e\}$ connected.
   $\implies$ **TRUE.** (Cycle Property).
2. **`MAYBE-MST-B` (Arbitrary Order Cycle-Free):**
   $T = \emptyset$, for each $e$ in arbitrary order, add if no cycle.
   $\implies$ **FALSE.**
   *Counterexample:* Triangle with edges 1, 2, 100. If 100 processed first, it adds 100 and 2, producing spanning tree of cost 102 instead of 3!
3. **`MAYBE-MST-C` (Dynamic Cycle Elimination):**
   $T = \emptyset$, for each $e$ in arbitrary order, add $e$; if cycle $c$ forms, delete max edge on $c$.
   $\implies$ **TRUE.** (Red rule: heaviest edge on cycle is never in MST).

---

# 11. Master Algorithmic Complexity & Property Matrix

| Algorithm | Problem Solved | Data Structure | Time (Sparse: $E \approx V$) | Time (Dense: $E \approx V^2$) | Space | Negative Weights? | Negative Cycles? |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **BFS** | Unweighted SSSP | FIFO Queue | $\Theta(V + E)$ | $\Theta(V^2)$ | $\Theta(V)$ | No (assumes $w=1$) | N/A |
| **DFS** | Graph Exploration | Stack / Recursion | $\Theta(V + E)$ | $\Theta(V^2)$ | $\Theta(V)$ | N/A | Detects cycles |
| **Kahn's Topo** | Topological Sort | In-degree array + Queue | $\Theta(V + E)$ | $\Theta(V^2)$ | $\Theta(V)$ | N/A | Detects cycles |
| **Kosaraju SCC**| Strongly Connected Comp.| 2 DFS Passes + Transpose | $\Theta(V + E)$ | $\Theta(V^2)$ | $\Theta(V)$ | N/A | N/A |
| **Bellman-Ford**| Single-Source SP | Edge Array | $O(VE)$ | $O(V^3)$ | $\Theta(V)$ | **YES** | **Detects & Reports** |
| **DAG-SSSP** | DAG Shortest Paths | Topo Sort + Array | $\Theta(V + E)$ | $\Theta(V^2)$ | $\Theta(V)$ | **YES** | N/A (Acyclic) |
| **Dijkstra** | Non-Negative SSSP | Binary Min-Heap | $O(E \log V)$ | $O(V^2 \log V)$ | $\Theta(V)$ | **NO** (fails greedy) | Fails |
| **Dijkstra** | Non-Negative SSSP | Fibonacci Heap | $O(V \log V + E)$| $O(V^2)$ | $\Theta(V)$ | **NO** | Fails |
| **Dijkstra** | Non-Negative SSSP | Unsorted Array | $\Theta(V^2)$ | $\Theta(V^2)$ | $\Theta(V)$ | **NO** | Fails |
| **Dial's 0-K** | Small Integer SSSP | Bucket Array $[0..W]$ | $O(E + WV)$ | $O(V^2)$ | $O(V + W)$ | No | Fails |
| **Floyd-Warshall**| All-Pairs SP | $V \times V$ Matrix | $\Theta(V^3)$ | $\Theta(V^3)$ | $\Theta(V^2)$ | **YES** | **Detects ($D[i,i]<0$)** |
| **Johnson's** | All-Pairs SP (Sparse) | BF + Dijkstra $\times V$ | $O(V^2 \log V)$ | $O(V^3)$ | $\Theta(V^2)$ | **YES** | **Detects via BF** |
| **Kruskal MST** | Minimum Spanning Tree | Union-Find | $O(E \log V)$ | $O(V^2 \log V)$ | $\Theta(V)$ | **YES** | N/A (Undirected) |
| **Prim MST** | Minimum Spanning Tree | Binary Min-Heap | $O(E \log V)$ | $O(V^2 \log V)$ | $\Theta(V)$ | **YES** | N/A (Undirected) |
| **Prim MST** | Minimum Spanning Tree | Fibonacci Heap | $O(V \log V + E)$| $O(V^2)$ | $\Theta(V)$ | **YES** | N/A (Undirected) |
| **Segment Tree**| Range Queries / Updates| Array ($4N$) | Build: $O(N)$ | Query: $O(\log N)$ | $\Theta(N)$ | N/A | N/A |

---

# 12. High-Yield "Trap & Counterexample" Rapid-Fire Revision Bank

### Traps on Shortest Paths:
1. **"Adding a constant $C > 0$ to make all edges non-negative allows Dijkstra to run."**
   $\implies$ **FALSE.** Penalizes paths with more edges ($W' = W + kC$), altering the shortest path.
2. **"If all edge weights are distinct positive numbers, the shortest path between any two vertices is unique."**
   $\implies$ **FALSE.** $s \to a \to b$ ($2 + 3 = 5$) has the same length as direct edge $s \to b$ ($5$). Distinct edge weights do not imply distinct path sums!
3. **"Dijkstra always fails if there is a negative edge."**
   $\implies$ **FALSE.** If the negative edge is on an isolated branch or respects topological extraction order, Dijkstra can output valid shortest paths (e.g. $s \xrightarrow{5} u \xrightarrow{-2} v$).
4. **"If a graph has negative edges, Bellman-Ford and Dijkstra produce the same distance."**
   $\implies$ **FALSE.** Dijkstra's greedy choice finalizes vertices prematurely, producing incorrect overestimates.
5. **"DAG-SSSP requires edge weights to be non-negative."**
   $\implies$ **FALSE.** DAG-SSSP runs in $\Theta(V + E)$ on **arbitrary real edge weights** (positive and negative) because cycles cannot exist.

---

### Traps on Trees & Graph Structure:
6. **"The depth of a BFS tree is the graph diameter."**
   $\implies$ **FALSE.** It is the eccentricity of the root. In $P_5$, starting at the midpoint gives depth 2 while diameter is 4.
7. **"Double BFS computes the diameter of any graph."**
   $\implies$ **FALSE.** Provably correct **only for trees**. On graphs with cycles, it gets trapped in local cycle loops.
8. **"Every DAG has a unique topological sort."**
   $\implies$ **FALSE.** A DAG has a unique topological sort $\iff$ it has a **Hamiltonian Path**.
9. **"Adding an edge from last to first vertex in topological order always creates a cycle."**
   $\implies$ **FALSE.** In disconnected graphs ($A \to B$ and $C \to D$, order $[A, C, B, D]$), adding $D \to A$ creates path $C \to D \to A \to B$, which is strictly acyclic.
10. **"An undirected graph with non-tree edges can have Cross edges in DFS."**
    $\implies$ **FALSE.** In undirected DFS, every non-tree edge is strictly a **Back edge**.

---

### Traps on Minimum Spanning Trees (MST):
11. **"If edge weights are squared, the MST can change."**
    $\implies$ **FALSE.** $f(x) = x^2$ is strictly monotonic for $x > 0$. Relative order is preserved.
12. **"If all edge weights in a graph are distinct, the MST is unique."**
    $\implies$ **TRUE.** By the Cut Property, every cut has a unique light edge, determining a unique MST.
13. **"If a graph has a cycle with negative edge weights, an MST may contain a cycle."**
    $\implies$ **FALSE.** By definition, a spanning **tree** is acyclic.
14. **"The shortest path tree computed by Dijkstra is always an MST."**
    $\implies$ **FALSE.** SSSP minimizes distance from a single source; MST minimizes global sum of edge weights.
15. **"If an edge $e$ is the strictly minimum-weight edge in the entire graph, it must belong to every MST."**
    $\implies$ **TRUE.** By the Cut Property applied to the cut separating one endpoint of $e$ from the rest of the graph.

---

### Traps on APSP & Dynamic Programming:
16. **"In Floyd-Warshall, changing the loop order to make $i$ outermost computes valid shortest paths."**
    $\implies$ **FALSE.** Paths of length $> 2$ fail to propagate transitively. In $1 \to 2 \to 3 \to 4$, $D[1, 4]$ remains $\infty$.
17. **"Floyd-Warshall requires vertices to be numbered $1..n$ sequentially."**
    $\implies$ **FALSE.** Any arbitrary permutation $\sigma$ of intermediate vertices works identically.
18. **"An undirected graph with one negative edge can have shortest paths computed by Floyd-Warshall."**
    $\implies$ **FALSE.** A single negative edge forms an instant negative 2-cycle ($u \leftrightarrow v$, sum $2w < 0$), making distances $-\infty$.
19. **"Transitive closure cannot be computed in less than $\Theta(V^3)$ time."**
    $\implies$ **FALSE.** Bitset parallelism computes it in $O(V^3 / 64)$, and fast matrix multiplication computes it in $O(V^{2.373})$.
20. **"A Segment Tree over $N$ elements takes $O(N \log N)$ space."**
    $\implies$ **FALSE.** It takes strictly $\Theta(N)$ space (at most $4N$ elements in an array).

---

## Final 60-Second Exam Checklist
- [ ] **Shortest path on unweighted graph?** $\to$ Run **BFS** in $O(V + E)$.
- [ ] **Shortest path on DAG?** $\to$ Run **Topological Sort + Relaxation** in $\Theta(V + E)$ (handles negative weights!).
- [ ] **Shortest path with non-negative weights?** $\to$ Run **Dijkstra** in $O(V \log V + E)$.
- [ ] **Shortest path with negative weights?** $\to$ Run **Bellman-Ford** in $O(VE)$ or **Johnson's** in $O(V^2 \log V + VE)$.
- [ ] **Diameter on tree?** $\to$ Run **Double-BFS** (2 passes).
- [ ] **Diameter on general graph?** $\to$ Run **APSP** (BFS $\times V$ or Floyd-Warshall).
- [ ] **Cycle detection in directed graph?** $\to$ Run **DFS**; back edge (Grey $\to$ Grey) $\iff$ cycle.
- [ ] **Tree traversal reconstruction?** $\to$ Need **Inorder** + (Preorder OR Postorder). Preorder + Postorder works **only if full binary tree**.
- [ ] **MST with negative weights?** $\to$ Run **Kruskal / Prim** (works normally!).
- [ ] **Check negative cycle in Floyd-Warshall?** $\to$ Check if any **diagonal $D[i, i] < 0$**.
