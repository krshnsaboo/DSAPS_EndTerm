# DSAPS Ultimate Last-Day Revision Notes
## Data Structures and Algorithms for Problem Solving (CS1.304) — IIIT Hyderabad

> **Purpose:** High-yield, comprehensive exam-eve distillation covering every core topic, theorem, recurrence relation, proof technique, trap, and Slip Test / EndSem PYQ variation.  
> **How to use this file:** Read through sections sequentially or jump directly to specific topics before your exam. Every concept includes the mathematical rule, the underlying intuition, and the standard counterexamples professors use to test your understanding.

---

## Quick Navigation
1. [Master Complexity & Recurrence Cheat Sheet](#1-master-complexity--recurrence-cheat-sheet)
2. [Johnson's Algorithm & Shortest Path Variations](#2-johnsons-algorithm--shortest-path-variations)
3. [Minimum Spanning Trees (MST) & Graph Theorems](#3-minimum-spanning-trees-mst--graph-theorems)
4. [Gale-Shapley Stable Marriage Algorithm](#4-gale-shapley-stable-marriage-algorithm)
5. [Interval Scheduling & Classroom Partitioning](#5-interval-scheduling--classroom-partitioning)
6. [Dynamic Programming: Coin Change Problem](#6-dynamic-programming-coin-change-problem)
7. [Dynamic Programming: LCS & Edit Distance](#7-dynamic-programming-lcs--edit-distance)
8. [Dynamic Programming: Knapsack Problem & Multiple Knapsacks](#8-dynamic-programming-knapsack-problem--multiple-knapsacks)
9. [Integer Multiplication (Karatsuba & Beyond)](#9-integer-multiplication-karatsuba--beyond)
10. [Matrix Multiplication (Strassen, AlphaTensor & MCM)](#10-matrix-multiplication-strassen-alphatensor--mcm)
11. [Disjoint Set Union (DSU) & Grid BFS](#11-disjoint-set-union-dsu--grid-bfs)
12. [The Top 20 "Twisted Exam Traps" Quickfire Bank](#12-the-top-20-twisted-exam-traps-quickfire-bank)

---

## 1. Master Complexity & Recurrence Cheat Sheet

| Problem / Algorithm | Recurrence / Mechanism | Time Complexity | Space Complexity | Notes / Invariants |
|:---|:---|:---:|:---:|:---|
| **Johnson's APSP** | $1 \times \text{BF} + V \times \text{Dijkstra}$ | $O(VE + V^2 \log V)$ | $O(V^2)$ | Sparse graphs with negative edges. $s \notin V$. |
| **Dijkstra** | Min-Heap priority queue | $O((V + E) \log V)$ | $O(V)$ | Fails with negative edges or general longest path. |
| **Bellman-Ford** | $V-1$ relaxation passes | $O(VE)$ | $O(V)$ | Detects negative weight cycles. |
| **Kruskal (MST)** | Sort edges + DSU | $O(E \log E)$ | $O(V)$ | Light edge of a cut. Works via Cut Property. |
| **Prim (MST)** | Grow single tree via heap | $O(E \log V)$ | $O(V)$ | Identical MST to Kruskal if edge weights unique. |
| **Reverse-Delete (MST)** | Delete max edge if cycle | $O(E \log V (\log \log V)^3)$ | $O(V)$ | Works via Cycle Property (`MAYBE-MST-A`). |
| **Gale-Shapley** | Propose-and-reject | $O(n^2)$ | $O(n^2)$ | Max proposals $\le n^2 - n + 1$. Proposer optimal. |
| **Interval Scheduling** | Sort by finish time (Greedy) | $O(n \log n)$ | $O(n)$ | Greedy Stays Ahead: $e(g_r) \le e(o_r)$. |
| **Classroom Partitioning** | Sort by start time + Min-Heap | $O(n \log n)$ | $O(n)$ | $\text{Min Rooms} = \text{Depth } d = \max_t(\text{overlaps})$. |
| **Weighted Interval** | $DP[i] = \max(DP[i-1], v_i + DP[p(i)])$ | $O(n \log n)$ | $O(n)$ | Binary search for predecessor $p(i)$. Greedy fails! |
| **Coin Change (Min)** | $DP[a] = \min_c(DP[a-c] + 1)$ | $O(n \cdot A)$ | $O(A)$ | **Pseudo-polynomial!** ($A \approx 2^L$). |
| **Coin Change (Ways)** | Outer: coins, Inner: amounts | $O(n \cdot A)$ | $O(A)$ | Combinations (distinct sets). Inner coins = permutations. |
| **LCS Length** | $DP[i][j] = DP[i-1][j-1]+1$ or $\max$ | $O(m \cdot n)$ | $O(\min(m, n))$ | Rolling array. $|SCS| = m + n - |LCS|$. |
| **Hirschberg's LCS** | Divide & Conquer + DP | $O(m \cdot n)$ | $\mathbf{O(m + n)}$ | Reconstructs full string in linear space. |
| **Levenshtein Distance** | Del (+1), Ins (+1), Sub (+1/0) | $O(n_1 \cdot n_2)$ | $O(n_1 \cdot n_2)$ | Slip Test 2: alignment notation with gaps. |
| **0/1 Knapsack** | $DP[w] = \max(DP[w], DP[w-w_i] + v_i)$ | $O(n \cdot W)$ | $O(W)$ | Loop capacity **backwards** ($W \to w_i$). Weakly NP-C. |
| **Knapsack (Dual DP)** | $DP[v] = \min$ weight for value $v$ | $O(n \cdot V_{\max})$ | $O(V_{\max})$ | Use when capacity $W$ is huge ($W \ge 10^9$). |
| **Two Thieves Knapsack** | $DP[c_1][c_2] = \max(\text{stay}, c_1, c_2)$ | $O(n \cdot W_1 W_2)$ | $O(W_1 W_2)$ | Slip Test 3: loop both capacities backwards! |
| **Karatsuba Multiplication**| $T(n) = 3T(n/2) + O(n)$ | $\Theta(n^{\log_2 3}) \approx O(n^{1.585})$| $O(n)$ | Gauss trick: 4 products reduced to 3. |
| **Strassen Matrix Mult** | $T(n) = 7T(n/2) + \Theta(n^2)$ | $\Theta(n^{\log_2 7}) \approx O(n^{2.807})$| $O(n^2)$ | 7 products ($m_1 \dots m_7$). Exact ops: $7n^{\log_2 7} - 6n^2$. |
| **AlphaTensor Binary** | $T(n) = 47T(n/4) + \Theta(n^2)$ | $\Theta(n^{\log_4 47}) \approx O(n^{2.7773})$| $O(n^2)$ | DeepMind 2022: $4 \times 4$ binary matrices in 47 mults. |
| **Matrix Chain Mult (MCM)**| $DP[i][j] = \min_k(DP[i][k] + DP[k+1][j] + d)$ | $O(n^3)$ | $O(n^2)$ | Diagonal order by chain length $L$. Catalan trees. |
| **Disjoint Set Union (DSU)**| Path compression + Union by rank | $O(m \cdot \alpha(n))$ | $O(n)$ | $\alpha(n) \le 4$ for all practical universe sizes. |

---

## 2. Johnson's Algorithm & Shortest Path Variations

### The Core Essence
Finds all-pairs shortest paths on a directed graph with positive and negative edge weights, but **NO negative-weight cycles**, in $O(VE + V^2 \log V)$ time.

### The Mechanism
1. Add a **dummy vertex $s \notin V$** with directed edges $(s, v)$ of weight $0$ to all $v \in V$.
2. Run **Bellman-Ford** from $s$ to compute potential $h(v) = \delta(s, v)$ for all $v$. If a negative cycle is detected $\implies$ abort.
3. Reweight every edge:
   $$w'(u, v) = w(u, v) + h(u) - h(v)$$
   *Triangle inequality guarantees non-negativity:* Since $h(v) \le h(u) + w(u, v) \implies w'(u, v) \ge 0$.
4. Remove $s$ and run **Dijkstra $V$ times** (once from each vertex $u \in V$) using weights $w'$.
5. Recover true shortest path distances:
   $$\delta(u, v) = \delta'(u, v) - h(u) + h(v)$$

---

### Crucial Theorems & Invariants
- **Telescoping Cancellation Over Paths:**
  For any path $p = \langle v_0, v_1, \dots, v_k \rangle$:
  $$w'(p) = \sum_{i=1}^k w'(v_{i-1}, v_i) = \sum_{i=1}^k \big(w(v_{i-1}, v_i) + h(v_{i-1}) - h(v_i)\big) = w(p) + h(v_0) - h(v_k)$$
  Because the added term $+ h(u) - h(v)$ depends **only on the endpoints**, the relative weight difference between *any two paths between the same pair of vertices* is completely preserved! Shortest paths in $G$ are identical to shortest paths in $G'$.
- **Cycles Invariance:**
  For any cycle $C$ (where $v_0 = v_k$):
  $$w'(C) = w(C) + h(v_0) - h(v_0) = w(C)$$
  - Zero-weight cycles remain zero-weight.
  - Negative cycles remain negative cycles.
  - Positive cycles remain positive cycles.

---

### Exam Traps & Counterexamples
> **Trap 1: Why can't we just add a constant $C = |\min_{(u,v)} w(u,v)|$ to all edges?**  
> **Because paths have different numbers of edges!** A path with 10 edges receives $+10C$, while a path with 2 edges receives only $+2C$. This heavily penalizes paths with many edges, destroying the shortest path structure!

> **Trap 2: Can we add 0-weight edges from an EXISTING vertex $A$ instead of a dummy vertex $s$?**  
> **NO!** (EndSem 2025 Puzzle 1):
> 1. If $A$ cannot reach some component, Bellman-Ford leaves $h(v) = \infty$, failing to reweight all edges.
> 2. If $A$ already has incoming edges (in-degree $> 0$), adding 0-weight edges can create **artificial negative cycles** involving $A$, even when the original graph had none!
> *Rule:* $s$ MUST be a newly created node with $\text{in-degree}(s) = 0$.

> **Trap 3: Running Dijkstra to find Longest Simple Path?**  
> - On **general graphs**: Longest Simple Path is **NP-Hard** (Hamiltonian Path reduces to it). Inverting edge weights creates negative cycles, causing Dijkstra to fail.
> - On **DAGs / Forests**: Solvable in $O(V + E)$ by Topological Sorting + DP, or running Dijkstra on tree branches (EndSem Puzzle 3).

---

## 3. Minimum Spanning Trees (MST) & Graph Theorems

### The Twin Pillars of MST
1. **The Cut Property (Edge Inclusion):**  
   For *any* cut $(S, V \setminus S)$ in graph $G$, the **strictly lightest edge** crossing the cut belongs to **EVERY MST** of $G$. If there are ties, at least one lightest edge belongs to SOME MST.
2. **The Cycle Property (Edge Exclusion):**  
   For *any* simple cycle $C$ in graph $G$, the **strictly heaviest edge** in $C$ **CANNOT belong to ANY MST** of $G$.

---

### Monotonic Transformations on Edge Weights: Does MST Change?

| Function Applied: $w'(e) = f(w(e))$ | Does the MST Change? | Reasoning / Proof |
|:---|:---:|:---|
| **$f(x) = x + c$** ($c \in \mathbb{R}$) | **NO** | Total weight shifts by $(V-1)c$. Relative order of all edges is identical. |
| **$f(x) = c \cdot x$** ($c > 0$) | **NO** | Strictly preserves all relative edge comparisons. |
| **$f(x) = x^3$ or $x^k$ ($k$ odd, or $x > 0$)** | **NO** | Strictly increasing function preserves relative ordering of every cut/cycle edge. |
| **$f(x) = \sqrt{x}$** ($x \ge 0$) | **NO** | Strictly monotonically increasing! If $w_1 < w_2 \implies \sqrt{w_1} < \sqrt{w_2}$. |
| **$f(x) = e^x$ or $\ln x$ ($x > 0$)** | **NO** | Strictly increasing $\implies$ Kruskal/Prim make the exact same sequence of choices. |
| **$f(x) = -x$** | **YES (Inverts!)** | Minimum Spanning Tree becomes the **Maximum Spanning Tree**! |
| **$f(x) = 1/x$** ($x > 0$) | **YES (Inverts!)** | Strictly decreasing $\implies$ lightest edges become heaviest. |
| **$f(x) = \lfloor x \rfloor$ or $x^2$ (with neg weights)**| **MAYBE** | Non-strictly monotonic creates ties; original MST remains valid, but new MSTs may appear. |

> **Crucial Contrast with Shortest Path Tree (SPT):**  
> **SPT is NOT invariant under $f(x) = x + c$ or $f(x) = \sqrt{x}$!**  
> Adding $c$ to edge weights penalizes paths with more edges ($\text{dist}'(p) = \text{dist}(p) + k \cdot c$). Squaring or square-rooting distorts path sum comparisons. **Only $f(x) = c \cdot x$ ($c > 0$) preserves SPT!**

---

### MST vs Minimum Bottleneck Spanning Tree (MBST)
- **MBST Definition:** A spanning tree that minimizes the weight of its **heaviest edge** ($\max_{e \in T} w(e)$).
- **The Golden Theorem:**
  $$\mathbf{\text{Every MST is an MBST, but NOT every MBST is an MST!}}$$
- **Counterexample:** A path with weights $1, 1, 10$ has max edge 10 (total weight 12). A path with weights $9, 9, 10$ also has max edge 10 (total weight 28). Both are MBSTs, but only the first is an MST!

---

### The Mystery MST Algorithms (`MAYBE-MST`)
- **`MAYBE-MST-A` (Reverse-Delete):** Sort edges descending. For each edge, if removing it disconnects the graph, keep it; else delete it. $\implies$ **ALWAYS FINDS AN MST!** (Proven via the Cycle Property: any deleted edge was the heaviest edge on some cycle).
- **`MAYBE-MST-C` (Cycle-Breaking):** Find any cycle, delete its heaviest edge. Repeat until no cycles remain. $\implies$ **ALWAYS FINDS AN MST!**

---

## 4. Gale-Shapley Stable Marriage Algorithm

### Core Principles & Invariants
- **Input:** $n$ men and $n$ women, each having a strict, complete preference ranking over all members of the opposite sex.
- **Definition of Instability:** A matching $M$ is unstable if there exists a **blocking pair $(m, w)$** such that:
  1. $m$ prefers $w$ over his assigned partner $M(m)$.
  2. $w$ prefers $m$ over her assigned partner $M(w)$.
- **A matching is STABLE if and only if NO blocking pair exists.**

---

### The Four Ironclad Theorems of Gale-Shapley
1. **Proposer-Optimality:**  
   Every proposer receives their **best valid partner** in *any* stable matching.
2. **Receiver-Pessimality:**  
   Every receiver receives their **worst valid partner** in *any* stable matching.
3. **Order-Invariance:**  
   The order in which unmatched proposers make proposals **does not matter**. Every execution sequence converges to the exact same matching.
4. **Weak Pareto Optimality:**  
   There is no matching (stable or unstable) that every proposer strictly prefers to the proposer-optimal stable matching.

---

### Key Formulas & Implementation Bounds
- **Maximum Number of Proposals:**
  $$\mathbf{\text{Max Proposals} = n^2 - n + 1}$$
  *(In the worst case, one woman receives only 1 proposal, and the other $n-1$ women receive $n$ proposals each).*
- **Optimal Time Complexity:** $O(n^2)$ using an inverted lookup ranking matrix `rank[w][m]` where `rank[w][m]` stores $m$'s rank in $w$'s preference list ($O(1)$ decision for women).
- **Testing for a UNIQUE Stable Matching:**
  Run Gale-Shapley twice:
  1. Run **Men-Proposing GS** $\implies$ produces $M_{\text{men}}$.
  2. Run **Women-Proposing GS** $\implies$ produces $M_{\text{women}}$.
  3. If $M_{\text{men}} == M_{\text{women}}$, the stable matching is **strictly unique**!

---

## 5. Interval Scheduling & Classroom Partitioning

### The 6 Interval Scheduling Algorithms (Lecture Slide 68 Summary)

| Alg # | Name | Feasible? | Optimal? | Counterexample / Reason |
|:---:|:---|:---:|:---:|:---|
| **1** | **EARLIEST-START-TIME** | **YES** | **NO** | $[1, 100]$ blocks $[2, 5], [6, 10], [11, 15]$. |
| **2** | **SELECT-SMALLEST-INTERVAL** | **YES** | **NO** | Center trap: $[9, 11]$ blocks $[1, 10]$ and $[10, 20]$. |
| **3** | **DELETE-LARGEST-INTERVAL** | **YES** | **NO** | Overlapping chains eliminate wrong candidates. |
| **4** | **DELETE-DOMINATING-INTERVALS** | **NO** | **—** | Partial overlaps remain! E.g., $[1, 5]$ and $[3, 8]$. |
| **5** | **Alg 4 + Alg 1** | **YES** | **YES** | No domination $\implies s_A < s_B \iff e_A < e_B$. Equivalent to Alg 6! |
| **6** | **EARLIEST-END-TIME** | **YES** | **YES** | Optimal! Proved via **Greedy Stays Ahead**. |

---

### The "Greedy Stays Ahead" Induction Proof Steps
1. Let greedy be $G = \{g_1, \dots, g_k\}$ and optimal be $O = \{o_1, \dots, o_m\}$.
2. **Lemma:** For all $r \le k$, $e(g_r) \le e(o_r)$.
   - *Base Case ($r=1$):* $g_1$ has the globally earliest finish time $\implies e(g_1) \le e(o_1)$.
   - *Inductive Step:* Since $s(o_{r+1}) \ge e(o_r) \ge e(g_r)$, $o_{r+1}$ was compatible when greedy picked $g_{r+1}$. Since greedy picks the earliest finisher, $e(g_{r+1}) \le e(o_{r+1})$.
3. **Contradiction:** If $m > k$, interval $o_{k+1}$ starts after $e(o_k) \ge e(g_k)$, so greedy could have picked at least one more interval. Hence $k = m$.

---

### Classroom Scheduling / Interval Partitioning (Homework Slides 71–72)
- **Problem:** Minimum rooms to schedule all classes without clashes.
- **The Fundamental Theorem:**
  $$\mathbf{\text{Minimum Classrooms Required} = \text{Depth } d = \max_t \Big| \{I_i \mid s_i \le t < e_i\} \Big|}$$
- **Algorithm:** Sort by **Start Time**, maintain Min-Heap of room finish times ($O(n \log n)$).
- **The 8-Class Lecture Homework:**
  Classes: $[1,4], [7,9], [3,8], [11,15], [2,13], [10,18], [14,19], [5,6]$.
  - At $t = 3$, active classes are $\{[1,4], [2,13], [3,8]\} \implies \text{Count} = \mathbf{3}$.
  - At no point do 4 classes overlap $\implies \mathbf{\text{Depth } d = 3}$.
  - **Answer: Exactly 3 classrooms required.**

---

### Weighted Interval Scheduling (When Greedy Fails)
- **Recurrence:** Sort by finish time $e_1 \le e_2 \le \dots \le e_n$.
  $$DP[i] = \max\Big( DP[i-1], \quad v_i + DP[p(i)] \Big)$$
  where $p(i)$ is the latest compatible job ($e_{p(i)} \le s_i$) found via **binary search in $O(\log n)$**. Total time: **$O(n \log n)$**.

---

## 6. Dynamic Programming: Coin Change Problem

### Problem 1: Minimum Coins (Optimization)
$$dp[a] = \min_{\substack{c \in C \\ c \le a}} \big(dp[a - c] + 1\big), \quad dp[0] = 0$$

### Why $O(n \cdot A)$ is PSEUDO-POLYNOMIAL (Slip Test PYQ):
- Input size in binary: $L = \lceil \log_2(A + 1) \rceil$ bits $\implies A \approx 2^L$.
- Running time: $O(n \cdot A) = O(n \cdot 2^L)$, which is **exponential** in input bit-length $L$!

---

### The #1 Exam Trap: Combinations vs Permutations

```cpp
// 1. COMBINATIONS (Distinct coin sets, order does NOT matter - LeetCode 518)
for (int coin : coins) {
    for (int a = coin; a <= amount; ++a) {
        dp[a] += dp[a - coin];
    }
}

// 2. PERMUTATIONS (Order DOES matter: [1, 2] != [2, 1] - LeetCode 377)
for (int a = 1; a <= amount; ++a) {
    for (int coin : coins) {
        if (a >= coin) dp[a] += dp[a - coin];
    }
}
```

---

### Slip Test 1 (10 Aug 2026) Solutions
- Coins: $\{1, 6, 8\}$ (unlimited) and $\{13\}$ (at most 2):
  - $A = 45 \implies \mathbf{5 \text{ coins}}$ ($1 \times 13 + 4 \times 8$).
  - $A = 59 \implies \mathbf{7 \text{ coins}}$ ($1 \times 13 + 5 \times 8 + 1 \times 6$).
  - $A = 1000 \implies \mathbf{124 \text{ coins}}$ ($2 \times 13 + 121 \times 8 + 1 \times 6$).
- General Bounded Coin $c_n \le k$: Run unbounded DP on $c_1 \dots c_{n-1}$ ($O(n A)$), then check $m \in [0, k]$ ($O(k)$). Total time: **$O(n A + k)$**, Space: **$O(A)$**.

---

## 7. Dynamic Programming: LCS & Edit Distance

### LCS Core Recurrence & Space Optimization
$$DP[i][j] = \begin{cases}
DP[i-1][j-1] + 1 & \text{if } X[i] == Y[j] \\
\max(DP[i-1][j], DP[i][j-1]) & \text{if } X[i] \ne Y[j]
\end{cases}$$
- **Space:** Length needs only $O(\min(m, n))$ space (rolling array). Full string reconstruction in $O(m + n)$ space requires **Hirschberg's Divide-and-Conquer DP**.
- **Reductions:**
  - Shortest Common Supersequence: $|SCS| = |X| + |Y| - |LCS|$.
  - Longest Palindromic Subsequence: $LPS(S) = LCS(S, \text{reverse}(S))$.

---

### Levenshtein Distance (Slip Test 2, 17 Aug 2026)
$$DP[i][j] = \begin{cases}
DP[i-1][j-1] & \text{if } A[i] == B[j] \\
1 + \min\Big( DP[i-1][j], DP[i][j-1], DP[i-1][j-1] \Big) & \text{if } A[i] \ne B[j]
\end{cases}$$
- **Evaluations from Slip Test 2:**
  - $\text{dist}(\text{"sleeveless"}, \text{"lessens"}) = \mathbf{6}$
  - $\text{dist}(\text{"intelligent"}, \text{"belligerent"}) = \mathbf{5}$
  - $\text{dist}(\text{"melanin"}, \text{"millennial"}) = \mathbf{6}$

---

## 8. Dynamic Programming: Knapsack Problem & Multiple Knapsacks

### 0/1 Knapsack Formulations
1. **Capacity-State DP:** $DP[w] = \max(DP[w], DP[w - w_i] + v_i)$.  
   *MANDATORY:* Inner loop traverses capacity **BACKWARDS** ($W \to w_i$) to prevent taking the same item multiple times! Running time: $O(n W)$.
2. **Value-State Dual DP:** $DP[v] = \min$ weight to achieve value $v$.  
   *Use when $W$ is massive ($W = 10^9$) but values are small ($V_{\max} \le 1000$).* Running time: $O(n \cdot \sum v_i)$.

---

### When Does Greedy Work for 0/1 Knapsack?
- **Monotonic Weights & Values ($w_1 \le \dots \le w_n$ AND $v_1 \ge \dots \ge v_n$):** Greedy by ratio **WORKS!** Item 1 strictly dominates all subsequent items.
- **Exponential Values ($v_{i+1} = 2 v_i$):** Greedy from highest value **WORKS!** Because $v_k > \sum_{j=1}^{k-1} v_j$.
- **Equal Ratios ($v_i / w_i = r$):** Greedy **FAILS!** (Reduces to Subset Sum).

---

### Slip Test 3: The Two Thieves Knapsack ($W_1 = 15, W_2 = 17$)
- **Puzzle 1:** Total item weight = 37, combined capacity = 32. Must leave behind $\ge 5$ weight. Item 3 (wt 6, val 18) sacrifices the least value! Optimal value: **105**.
  - Thief 1: Items 1 & 2 (Weight 15, Value 47).
  - Thief 2: Items 4, 5, 6, 7 (Weight 16, Value 58).
- **Puzzle 2 (Two-Knapsack DP):**
  $$DP[c_1][c_2] = \max(DP[c_1][c_2], DP[c_1 - w_i][c_2] + v_i, DP[c_1][c_2 - w_i] + v_i)$$
  Traverse both $c_1$ and $c_2$ in reverse! Time: **$O(n \cdot W_1 \cdot W_2)$**, Space: **$O(W_1 \cdot W_2)$**.

---

## 9. Integer Multiplication (Karatsuba & Beyond)

### The Complexity Hierarchy
- **Addition:** $O(\max(\log a, \log b)) = \mathbf{O(n)}$ (Linear).
- **Repeated Addition:** $O(b \log a) = \mathbf{O(2^L \cdot L)}$ (Exponential).
- **Schoolbook Multiplication:** $\mathbf{\Theta(n^2)}$.
- **Karatsuba (1960):** $\mathbf{\Theta(n^{\log_2 3}) \approx \Theta(n^{1.585})}$.
- **Schönhage–Strassen (1971):** $\mathbf{O(n \log n \log \log n)}$ (FFT).
- **Harvey–Hoeven (2019):** $\mathbf{O(n \log n)}$ (Conjectured optimal).

---

### The Karatsuba Identity
Split $X = X_1 \cdot 10^{n/2} + X_0$ and $Y = Y_1 \cdot 10^{n/2} + Y_0$.
Compute 3 products instead of 4:
1. $z_2 = X_1 \times Y_1$
2. $z_0 = X_0 \times Y_0$
3. $z_1 = (X_1 + X_0) \times (Y_1 + Y_0)$
$$\text{Middle Term } (X_1 Y_0 + X_0 Y_1) = z_1 - z_2 - z_0$$
$$X \cdot Y = z_2 \cdot 10^n + (z_1 - z_2 - z_0) \cdot 10^{n/2} + z_0$$
$$T(n) = 3 T(n/2) + O(n) \implies \log_2 3 = \frac{\ln 3}{\ln 2} \approx \mathbf{1.58496}$$

---

## 10. Matrix Multiplication (Strassen, AlphaTensor & MCM)

### Strassen’s 7 Matrix Multiplications ($O(n^{2.807})$)
$T(n) = 7 T(n/2) + \Theta(n^2) \implies n^{\log_2 7} \approx n^{2.8074}$.
- **Reconstruction Formulas (Slip Test 4, Puzzle 1):**
  - $c_{11} = m_1 + m_4 - m_5 + m_7$
  - $c_{12} = m_3 + m_5$
  - $c_{21} = m_2 + m_4$
  - $c_{22} = m_1 - m_2 + m_3 + m_6$
- **Exact Arithmetic Operations Count (Slip Test 4 Bonus):**
  $$M(n) = n^{\log_2 7}, \quad A(n) = 6 n^{\log_2 7} - 6 n^2 \implies \mathbf{\text{Total}(n) = 7 n^{\log_2 7} - 6 n^2}$$
  *(For $n = 2$: $7(7) - 6(4) = 49 - 24 = \mathbf{25 \text{ operations}}$: 7 mults + 18 adds/subs).*

---

### DeepMind AlphaTensor (October 2022 - Slip Test 4, Puzzle 3)
Multiplies two $4 \times 4$ binary matrices using **47 multiplications** (compared to $4^3 = 64$ standard, or $7^2 = 49$ Strassen):
$$T(n) = 47 T(n/4) + \Theta(n^2) \implies \mathbf{O\left(n^{\log_4 47}\right) \approx O\left(n^{2.7773}\right)}$$

---

### Matrix Chain Multiplication (MCM)
- **Catalan Explosion:** Number of parenthesizations is $C_{n-1} = \frac{1}{n}\binom{2n-2}{n-1} = \Omega\left(\frac{4^n}{n^{3/2}}\right)$.
- **DP Recurrence:** $m[i, j]$ = min multiplications to compute $A_i \dots A_j$:
  $$m[i, j] = \min_{i \le k < j} \Big( m[i, k] + m[k+1, j] + d_{i-1} d_k d_j \Big)$$
- **Table Order:** By chain length $L = 2, 3, \dots, n$. Time: $\mathbf{O(n^3)}$, Space: $\mathbf{O(n^2)}$.

---

## 11. Disjoint Set Union (DSU) & Grid BFS

### Disjoint Set Union (DSU) Invariants
- **Two Core Optimizations:**
  1. **Union by Rank / Size:** Keeps trees shallow ($\le \log n$ depth without compression).
  2. **Path Compression:** Flattens tree on `find(x)`, pointing every node on path directly to root.
- **Combined Amortized Time:** $O(m \cdot \alpha(n))$ for $m$ operations on $n$ elements, where $\alpha$ is the Inverse Ackermann function ($\alpha(n) \le 4$ for any $n \le 10^{80}$).
- **Exam Rule:** Using **both** gives $\alpha(n)$; using **only one** gives $O(\log n)$.

---

### Grid Route Finding (EndSem Puzzle 7)
- On an unweighted $N \times M$ grid with obstacles, **Breadth-First Search (BFS)** computes the absolute shortest path in **$O(N \cdot M)$ time and space**.
- Maintain a parent array `parent[r][c]` to backtrack the optimal sequence of steps from target to start.

---

## 12. The Top 20 "Twisted Exam Traps" Quickfire Bank

1. **Q: Does $f(x) = \sqrt{x}$ change the MST of graph $G$?**  
   **A: NO!** $\sqrt{x}$ is strictly monotonically increasing on $x \ge 0$, perfectly preserving the relative order of all edges across every cut and cycle.

2. **Q: Does $f(x) = \sqrt{x}$ change the Shortest Path Tree (SPT)?**  
   **A: YES!** Path sums are distorted: $\sqrt{4} + \sqrt{4} = 4$, while $\sqrt{1} + \sqrt{9} = 4$, but originally $4+4=8$ vs $1+9=10$.

3. **Q: Can Dijkstra find the longest simple path on a DAG?**  
   **A: YES!** By running on the negated DAG (since DAGs cannot have negative cycles) or using Topological Sort DP in $O(V + E)$. On general graphs, it is NP-Hard!

4. **Q: In Johnson's Algorithm, why can't we add 0-weight edges from an existing vertex $A$?**  
   **A: Because $A$ might not reach all nodes (leaving potentials undefined), and if $A$ has incoming edges, it can create artificial negative cycles!**

5. **Q: What is the maximum number of proposals in Gale-Shapley with $n$ pairs?**  
   **A: $n^2 - n + 1$.**

6. **Q: Can a woman ever end up with someone better than her Gale-Shapley partner in a stable matching?**  
   **A: YES!** Gale-Shapley (proposer-oriented) assigns receivers their **worst** valid partner. In other stable matchings, she may do strictly better.

7. **Q: Does Gale-Shapley depend on the order in which free men propose?**  
   **A: NO!** Every proposal execution order produces the exact same proposer-optimal stable matching.

8. **Q: Why does Algorithm 4 (Delete Dominating Intervals) fail to schedule classes?**  
   **A: It is NOT FEASIBLE!** It only removes subset containment ($B \subseteq A$), leaving partial overlaps (like $[1, 5]$ and $[3, 8]$) untouched!

9. **Q: Why is Algorithm 5 (Delete Dominating + Earliest Start) strictly optimal?**  
   **A: Because once dominating intervals are deleted, $s_i < s_j \iff e_i < e_j$, making Earliest Start Time mathematically identical to Earliest End Time!**

10. **Q: What is the minimum number of classrooms required for intervals $[1,4], [7,9], [3,8], [11,15], [2,13], [10,18], [14,19], [5,6]$?**  
    **A: Exactly 3 classrooms** (maximum depth $d = 3$ at $t = 3$).

11. **Q: Is $O(n \cdot A)$ for Coin Change a polynomial time algorithm?**  
    **A: NO! It is PSEUDO-POLYNOMIAL.** $A$ requires $\lceil \log_2 A \rceil$ bits, so the running time is exponential in the input size.

12. **Q: Why must the capacity loop run BACKWARDS in 0/1 Knapsack?**  
    **A: To ensure each item is used at most once!** Running forwards causes $dp[w]$ to build upon $dp[w - w_i]$ from the *current* item's iteration, degenerating into Unbounded Knapsack.

13. **Q: If $v_1/w_1 = v_2/w_2 = \dots = v_n/w_n$, which greedy choice is optimal for 0/1 Knapsack?**  
    **A: NONE!** It reduces to the Subset Sum problem, which is NP-complete.

14. **Q: When is greedy provably optimal for 0/1 Knapsack?**  
    **A: When items sorted by increasing weight are also sorted by decreasing value ($w_1 \le \dots \le w_n$ and $v_1 \ge \dots \ge v_n$), or when values are strictly exponential ($v_{i+1} = 2v_i$).**

15. **Q: What is the total arithmetic operation count for Strassen's matrix multiplication?**  
    **A: $7 n^{\log_2 7} - 6 n^2$.** (For $n=2$, this gives 25 operations).

16. **Q: What is the running time exponent of Google DeepMind's AlphaTensor?**  
    **A: $O(n^{\log_4 47}) \approx O(n^{2.7773})$, beating Strassen's $O(n^{2.8074})$.**

17. **Q: Can every Minimum Bottleneck Spanning Tree (MBST) be an MST?**  
    **A: NO! Every MST is an MBST, but an MBST can contain unnecessarily heavy edges.**

18. **Q: How does Hirschberg’s Algorithm reconstruct an LCS string in $O(m + n)$ space?**  
    **A: By combining Divide-and-Conquer with Dynamic Programming:** It finds the midpoint split in $Y$ where the forward and backward DP arrays sum to the maximum LCS length.

19. **Q: In DSU, what is the running time if you ONLY use Path Compression without Union by Rank?**  
    **A: $O(m \log n)$** (or $O(m \log_{1 + m/n} n)$), NOT $\alpha(n)$! Both optimizations are required for $\alpha(n)$.

20. **Q: What is the Levenshtein distance between "intelligent" and "belligerent"?**  
    **A: Exactly 5** (Delete 'i', delete 'n', substitute 't' $\to$ 'b', insert 'e', insert 'r').
