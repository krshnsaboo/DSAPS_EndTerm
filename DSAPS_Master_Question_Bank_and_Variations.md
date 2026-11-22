# DSAPS Master Question Bank & Exam Variations Guide
## Complete Problem-Solving Repository: PYQs, Slip Tests, Class Tests & Advanced Variations
### Course: Data Structures and Algorithms for Problem Solving (CS1.304) — IIIT Hyderabad

> **Purpose:** A single, all-inclusive master question bank containing every exam question, slip test, class puzzle, and theoretical variation discussed across the course. Every question includes its full context, rigorous mathematical reasoning, exact solutions, and counterexamples.

---

## Table of Contents
1. [Topic 1: Johnson's Algorithm & Shortest Path Variations](#topic-1-johnsons-algorithm--shortest-path-variations)
2. [Topic 2: Minimum Spanning Trees (MST) & Graph Theorems](#topic-2-minimum-spanning-trees-mst--graph-theorems)
3. [Topic 3: Gale-Shapley Stable Marriage & Matching](#topic-3-gale-shapley-stable-marriage--matching)
4. [Topic 4: Interval Scheduling & Classroom Partitioning](#topic-4-interval-scheduling--classroom-partitioning)
5. [Topic 5: Dynamic Programming — Coin Change Problem](#topic-5-dynamic-programming--coin-change-problem)
6. [Topic 6: Dynamic Programming — LCS & Edit Distance](#topic-6-dynamic-programming--lcs--edit-distance)
7. [Topic 7: Dynamic Programming — Knapsack & Multiple Knapsacks](#topic-7-dynamic-programming--knapsack--multiple-knapsacks)
8. [Topic 8: Divide-and-Conquer — Integer Multiplication](#topic-8-divide-and-conquer--integer-multiplication)
9. [Topic 9: Divide-and-Conquer — Matrix Multiplication & MCM](#topic-9-divide-and-conquer--matrix-multiplication--mcm)
10. [Topic 10: Recurrences & Matrix Exponentiation (Amoeba Population)](#topic-10-recurrences--matrix-exponentiation-amoeba-population)
11. [Topic 11: Disjoint Set Union (DSU) & Grid Pathfinding](#topic-11-disjoint-set-union-dsu--grid-pathfinding)

---

# Topic 1: Johnson's Algorithm & Shortest Path Variations

---

### Question 1.1: Modifying Johnson's Algorithm (EndSem 2025, Puzzle 1)
> **Problem Statement:**  
> In Johnson’s algorithm for all-pairs shortest paths, we introduce a new vertex $s \notin V$ and add directed edges $(s, v)$ with weight $0$ for all $v \in V$.  
> Suppose a student proposes the following modification:  
> Instead of introducing a new vertex $s$, choose an **existing vertex $A \in V$**, and add directed edges $(A, v)$ of weight $0$ from $A$ to all other vertices $v \in V \setminus \{A\}$.  
> Analyze whether this modification works in all cases:  
> **Case 1:** $A$ cannot reach all other vertices in the original graph.  
> **Case 2:** $A$ is part of a cycle in the original graph.

#### Detailed Solution & Proof:
- **Verdict:** **The modification FAILS in both cases!**
- **Analysis of Case 1 (Reachability Failure):**
  - If vertex $A$ cannot reach some vertex $u$ in the original graph $G$, adding directed edges $(A, v)$ of weight 0 solves the forward reachability from $A$, **BUT** Bellman-Ford computes potentials $h(v) = \delta(A, v)$.
  - If there are disconnected components with negative edges that cannot be reached without passing through non-existent paths, the potentials $h(v)$ become distorted or remain undefined ($\infty$), causing reweighting $w'(u, v) = w(u, v) + h(u) - h(v)$ to fail.
- **Analysis of Case 2 (Artificial Negative Cycles):**
  - Suppose the original graph contains a path from some vertex $B$ back to $A$ with weight $w(B, A) = -2$.
  - In the original graph, there was NO cycle involving $A$ and $B$, or the existing cycle was non-negative.
  - By adding a new directed edge $(A, B)$ with weight $0$, we have explicitly introduced a cycle:
    $$A \xrightarrow{\text{weight } 0} B \xrightarrow{\text{weight } -2} A$$
  - The weight of this new cycle is $0 + (-2) = -2 < 0$.
  - Bellman-Ford now detects a **negative weight cycle that never existed in the original graph** and aborts!
- **Conclusion:** Vertex $s$ MUST be an external dummy node with **$\text{in-degree}(s) = 0$**. It can never have incoming edges, guaranteeing that no new cycles are created.

---

### Question 1.2: Why Constant Shift Reweighting Fails
> **Problem Statement:**  
> Let $G = (V, E)$ be a directed graph with negative edge weights. Suppose we find the minimum edge weight $w_{\min} = \min_{e \in E} w(e) < 0$, and define a new weight function:
> $$w'(e) = w(e) + |w_{\min}|$$
> Since $w'(e) \ge 0$ for all edges, can we run Dijkstra's algorithm on $G$ with weights $w'$ to find shortest paths?

#### Detailed Solution & Proof:
- **Verdict:** **NO! It strictly fails.**
- **Mathematical Explanation:**
  - Let $p$ be a path containing $k$ edges. The total transformed weight of path $p$ is:
    $$w'(p) = \sum_{e \in p} (w(e) + |w_{\min}|) = w(p) + k \cdot |w_{\min}|$$
  - Notice the term $k \cdot |w_{\min}|$: it depends directly on the **number of edges $k$** in the path!
  - A path with **more edges is penalized much more heavily** than a path with fewer edges.
- **Concrete Counterexample:**
  - Let $u \to v$ have two paths:
    - Path 1: 1 edge with weight $5$.
    - Path 2: 3 edges with weights $1, 1, 1$ (Total weight = 3).
  - Shortest path in $G$ is Path 2 ($3 < 5$).
  - Suppose $|w_{\min}| = 2$:
    - $w'(\text{Path 1}) = 5 + 1 \times 2 = 7$.
    - $w'(\text{Path 2}) = 3 + 3 \times 2 = 9$.
  - Now Path 1 appears shorter ($7 < 9$)! The shortest path identity is destroyed.

---

### Question 1.3: Modifying Dijkstra for Longest Simple Paths (EndSem 2025, Puzzle 3)
> **Problem Statement:**  
> Can Dijkstra's algorithm be modified (e.g., using a Max-Heap instead of a Min-Heap or negating edge weights) to find the longest simple path between two vertices:  
> (a) On general directed graphs?  
> (b) On directed acyclic graphs (DAGs) or trees/forests?

#### Detailed Solution:
- **Part (a) General Graphs:** **NO!**
  - Finding the longest simple path in a general graph is **NP-Hard** (the Hamiltonian Path problem directly reduces to it).
  - Negating edge weights creates negative cycles, which causes Dijkstra's greedy selection property to break down completely (nodes cannot be finalized once visited).
- **Part (b) DAGs and Forests:** **YES!**
  - On a DAG, topological sorting allows finding the longest path in $O(V + E)$ time using dynamic programming.
  - On a tree or forest, between any two connected vertices there is **exactly one simple path**, so running modified Dijkstra or standard BFS/DFS finds it trivially.

---

# Topic 2: Minimum Spanning Trees (MST) & Graph Theorems

---

### Question 2.1: Reverse-Delete and Cycle-Breaking MST (EndSem 2025, Puzzle 2)
> **Problem Statement:**  
> Analyze the correctness of the following two algorithms for finding an MST in an undirected, connected, weighted graph $G = (V, E)$:  
> **Algorithm `MAYBE-MST-A` (Reverse-Delete):**  
> 1. Sort all edges in decreasing order of weight.  
> 2. For each edge $e$, if removing $e$ does NOT disconnect the graph, delete $e$; otherwise, keep $e$.  
> **Algorithm `MAYBE-MST-C` (Cycle-Breaking):**  
> 1. While there is any cycle in the graph, find the cycle and delete its heaviest edge.  
> Does each algorithm always output an MST?

#### Detailed Solution & Proof:
- **Both algorithms ALWAYS output an MST!**
- **Proof for `MAYBE-MST-A` (Reverse-Delete):**
  - When an edge $e = (u, v)$ is considered for deletion, it is deleted if and only if there still exists an alternative path between $u$ and $v$ in the remaining graph.
  - This alternative path, together with $e$, forms a **simple cycle $C$**.
  - Since edges are processed in descending order, all edges in this alternative path have weights $\le w(e)$.
  - Thus, $e$ is the **strictly heaviest edge on cycle $C$**.
  - By the **Cycle Property**, the heaviest edge on any cycle cannot belong to any MST. Therefore, deleting $e$ is safe.
  - The remaining subgraph is connected and acyclic (a spanning tree) and consists solely of MST edges.
- **Proof for `MAYBE-MST-C`:**
  - Identical logic: deleting the heaviest edge of any cycle never eliminates an MST edge by the Cycle Property.

---

### Question 2.2: Monotonic Transformations Bank: Does the MST Change?
> **Problem Statement:**  
> Let $T$ be an MST of graph $G = (V, E)$ with edge weights $w(e)$. We apply a function $f: \mathbb{R} \to \mathbb{R}$ to every edge weight: $w'(e) = f(w(e))$.  
> For each of the following functions, state whether $T$ remains guaranteed to be an MST of the transformed graph $G'$:  
> 1. $f(x) = x + c$ ($c \in \mathbb{R}$)  
> 2. $f(x) = c \cdot x$ ($c > 0$)  
> 3. $f(x) = \sqrt{x}$ ($x \ge 0$)  
> 4. $f(x) = x^3$  
> 5. $f(x) = x^2$ (where edge weights can be negative)  
> 6. $f(x) = -x$  
> 7. $f(x) = 1/x$ ($x > 0$)  
> 8. $f(x) = \lfloor x \rfloor$

#### Detailed Solution:
1. **$f(x) = x + c$:** **MST DOES NOT CHANGE.** Total weight changes by $(V-1)c$ for all spanning trees. Relative order is preserved.
2. **$f(x) = c \cdot x$ ($c > 0$):** **MST DOES NOT CHANGE.** Relative comparisons $w(e_1) < w(e_2)$ are identical.
3. **$f(x) = \sqrt{x}$ ($x \ge 0$):** **MST DOES NOT CHANGE.** $\sqrt{x}$ is strictly monotonically increasing for $x \ge 0$. If $w(e_1) < w(e_2) \implies \sqrt{w(e_1)} < \sqrt{w(e_2)}$.
4. **$f(x) = x^3$:** **MST DOES NOT CHANGE.** $x^3$ is strictly increasing across all real numbers $\mathbb{R}$.
5. **$f(x) = x^2$ (with negative weights):** **MST CAN CHANGE!** E.g., $-5 < 2$, but $(-5)^2 = 25 > 2^2 = 4$. Order inverts!
6. **$f(x) = -x$:** **MST INVERTS!** The minimum spanning tree becomes the **Maximum Spanning Tree**.
7. **$f(x) = 1/x$ ($x > 0$):** **MST INVERTS!** Strictly decreasing function.
8. **$f(x) = \lfloor x \rfloor$:** **ORIGINAL MST REMAINS AN MST, BUT MAY LOSE UNIQUENESS.** Creates ties ($2.1 \to 2$ and $2.9 \to 2$).

---

### Question 2.3: MST vs Minimum Bottleneck Spanning Tree (MBST)
> **Problem Statement:**  
> (a) Is every Minimum Spanning Tree (MST) a Minimum Bottleneck Spanning Tree (MBST)?  
> (b) Is every MBST an MST? Prove or give a counterexample.

#### Detailed Solution:
- **Part (a): YES! Every MST is an MBST.**  
  If there existed an MBST with a max edge weight $W_{\text{bottleneck}} < \max_{e \in T_{\text{MST}}} w(e)$, adding edges of that MBST to the MST would create a cycle containing an edge strictly heavier than all edges on the cycle, contradicting the Cycle Property.
- **Part (b): NO! An MBST is NOT necessarily an MST.**
  - **Counterexample:**  
    Consider a graph with 3 vertices $A, B, C$ and edges:
    - $(A, B)$ with weight 10
    - $(B, C)$ with weight 10
    - $(A, C)$ with weight 1
    - MST picks $(A, C)$ [wt 1] and $(B, C)$ [wt 10]. Total weight = **11**, Bottleneck = **10**.
    - Another spanning tree picks $(A, B)$ [wt 10] and $(B, C)$ [wt 10]. Total weight = **20**, Bottleneck = **10**.
    - Both trees have the same bottleneck (10), so both are MBSTs, but only the first is an MST!

---

# Topic 3: Gale-Shapley Stable Marriage & Matching

---

### Question 3.1: Gale-Shapley Mechanics & Uniqueness (EndSem 2025, Puzzle 6)
> **Problem Statement:**  
> Consider $n$ men and $n$ women with strict preference lists.  
> 1. State the upper bound on the maximum number of proposals made during the Gale-Shapley algorithm.  
> 2. Explain the terms "Proposer-Optimal" and "Receiver-Pessimal".  
> 3. Devise an $O(n^2)$ algorithm to determine whether a given instance has a **unique** stable matching.

#### Detailed Solution:
1. **Maximum Proposals:**  
   $$\mathbf{n^2 - n + 1}$$
   - *Proof:* In any execution, no man proposes to the same woman twice. There are $n \times n = n^2$ total possible pairs.
   - For the algorithm to continue, at least one man must be free.
   - If $n - 1$ women have received proposals from all $n$ men (total $(n-1)n = n^2 - n$ proposals), the remaining woman has received only 1 proposal.
   - At this point, all $n$ women have received at least one proposal, so every woman is engaged and every man is matched. The algorithm halts immediately. Total proposals $\le n^2 - n + 1$.
2. **Proposer-Optimality & Receiver-Pessimality:**
   - **Proposer-Optimal:** Every man is matched to his highest-ranked valid partner among all possible stable matchings.
   - **Receiver-Pessimal:** Every woman is matched to her lowest-ranked valid partner among all possible stable matchings.
3. **Algorithm for Uniqueness Check ($O(n^2)$):**
   - Step 1: Run **Men-Proposing Gale-Shapley** $\implies$ outputs matching $M_{\text{men}}$.
   - Step 2: Run **Women-Proposing Gale-Shapley** $\implies$ outputs matching $M_{\text{women}}$.
   - Step 3: Compare $M_{\text{men}}$ and $M_{\text{women}}$.
     - If $M_{\text{men}} == M_{\text{women}}$, the stable matching is **strictly unique**.
     - If $M_{\text{men}} \ne M_{\text{women}}$, there are **multiple stable matchings**.
   - Total time: $2 \times O(n^2) = \mathbf{O(n^2)}$.

---

### Question 3.2: Strategic Manipulation in Gale-Shapley
> **Problem Statement:**  
> Can participants achieve a strictly better partner by lying about their preference lists in Gale-Shapley?  
> (a) Can a **proposer** benefit by lying?  
> (b) Can a **receiver** benefit by lying?

#### Detailed Solution:
- **(a) Proposers:** **NO!** Gale-Shapley is **strategy-proof for proposers**. Truth-telling is a dominant strategy for men in male-proposing GS.
- **(b) Receivers:** **YES!** A woman can truncate her preference list (declaring certain low-ranked men unacceptable) or swap rankings to force earlier rejections, causing men to propose down their lists and triggering a cascading chain of proposals that lands her a more preferred partner.

---

# Topic 4: Interval Scheduling & Classroom Partitioning

---

### Question 4.1: The 6 Heuristics for Interval Scheduling (Class Slides Summary)
> **Problem Statement:**  
> For each of the following 6 algorithms for single-room interval scheduling, evaluate whether it is **Feasible** (produces no clashes) and whether it is **Optimal** (maximizes classes):  
> - **Algorithm 1:** EARLIEST-START-TIME  
> - **Algorithm 2:** SELECT-SMALLEST-INTERVAL (Shortest Duration)  
> - **Algorithm 3:** DELETE-LARGEST-INTERVAL  
> - **Algorithm 4:** DELETE-DOMINATING-INTERVALS  
> - **Algorithm 5:** Algorithm 4 + Algorithm 1  
> - **Algorithm 6:** EARLIEST-END-TIME

#### Master Evaluation Table:
| Alg # | Name | Feasible? | Optimal? | Counterexample / Mathematical Reason |
|:---:|:---|:---:|:---:|:---|
| **1** | EARLIEST-START-TIME | **YES** | **NO** | Counterexample: $[1, 100]$ blocks $[2, 5], [6, 10], [11, 15]$. |
| **2** | SELECT-SMALLEST-INTERVAL | **YES** | **NO** | Center trap: $[9, 11]$ blocks $[1, 10]$ and $[10, 20]$. |
| **3** | DELETE-LARGEST-INTERVAL | **YES** | **NO** | Asymmetric chains cause sub-optimal deletions. |
| **4** | DELETE-DOMINATING-INTERVALS | **NO** | **—** | **Fails feasibility!** Does not delete partial overlaps (e.g., $[1, 5]$ and $[3, 8]$). |
| **5** | Alg 4 + Alg 1 | **YES** | **YES** | No domination $\implies s_A < s_B \iff e_A < e_B$. Equivalent to Earliest End Time! |
| **6** | EARLIEST-END-TIME | **YES** | **YES** | **Optimal!** Proven via **Greedy Stays Ahead** induction ($e(g_r) \le e(o_r)$). |

---

### Question 4.2: Classroom Partitioning Homework (Slides 71–72)
> **Problem Statement:**  
> You are given the following 8 class intervals:  
> $$[1, 4], \quad [7, 9], \quad [3, 8], \quad [11, 15], \quad [2, 13], \quad [10, 18], \quad [14, 19], \quad [5, 6]$$  
> What is the minimum number of classrooms required to schedule all classes without any clashes? Explain.

#### Detailed Solution:
1. **The Interval Depth Theorem:**  
   The minimum number of classrooms required to schedule all intervals without conflict is equal to the **depth $d$** of the set of intervals:
   $$d = \max_{t} \Big| \{ I_i \mid s_i \le t < e_i \} \Big|$$
2. **Finding the Maximum Overlap:**
   - At $t = 1$: active is $[1, 4]$ (count: 1).
   - At $t = 2$: active are $[1, 4], [2, 13]$ (count: 2).
   - At $t = 3$: active are $[1, 4], [2, 13], [3, 8]$ (count: **3**).
   - At $t = 5$: active are $[2, 13], [3, 8], [5, 6]$ (count: **3**).
   - At $t = 7$: active are $[2, 13], [3, 8], [7, 9]$ (count: **3**).
   - At $t = 11$: active are $[2, 13], [10, 18], [11, 15]$ (count: **3**).
   - At $t = 14$: active are $[10, 18], [11, 15], [14, 19]$ (count: **3**).
   - At no point in time do 4 intervals overlap simultaneously.
3. **Conclusion:** Peak depth is **$d = 3$**. Exactly **3 classrooms** are required.
4. **Classroom Assignments:**
   - **Room 1:** $[1, 4] \longrightarrow [5, 6] \longrightarrow [7, 9] \longrightarrow [11, 15]$
   - **Room 2:** $[3, 8] \longrightarrow [10, 18]$
   - **Room 3:** $[2, 13] \longrightarrow [14, 19]$

---

# Topic 5: Dynamic Programming — Coin Change Problem

---

### Question 5.1: Bounded Denominations (Slip Test 1, 10 Aug 2026, Puzzle 1)
> **Problem Statement:**  
> Coin denominations are $1, 6, 8,$ and $13$.  
> Coins $1, 6, 8$ are available in **unlimited** quantities.  
> Coin $13$ is available in a **maximum quantity of 2** (at most two 13-coins).  
> What is the minimum number of coins needed to make:  
> **(a) 45**  
> **(b) 59**  
> **(c) 1000**

#### Detailed Solution:
We evaluate $\min_{m \in \{0, 1, 2\}} \big( m + \text{UnboundedMinCoins}_{\{1, 6, 8\}}(A - 13m) \big)$:
- **(a) Target $A = 45$:**
  - If $m = 1$: Remainder $= 45 - 13 = 32$.
  - $32 = 4 \times 8$ (4 coins of 8).
  - Total coins $= 1 + 4 = \mathbf{5 \text{ coins}}$ ($\mathbf{1 \times 13 + 4 \times 8}$).
- **(b) Target $A = 59$:**
  - If $m = 1$: Remainder $= 59 - 13 = 46 = 5 \times 8 + 1 \times 6$ (6 coins). Total $= 1 + 6 = \mathbf{7 \text{ coins}}$.
  - If $m = 2$: Remainder $= 59 - 26 = 33 = 4 \times 8 + 1 \times 1$ (5 coins). Total $= 2 + 5 = \mathbf{7 \text{ coins}}$.
  - Minimum coins $= \mathbf{7 \text{ coins}}$.
- **(c) Target $A = 1000$:**
  - If $m = 2$: Remainder $= 1000 - 26 = 974$.
  - Divide by 8: $974 = 121 \times 8 + 6$.
  - Exact match: 121 coins of 8 and 1 coin of 6!
  - Total coins $= 2 (\text{of } 13) + 121 (\text{of } 8) + 1 (\text{of } 6) = \mathbf{124 \text{ coins}}$.

---

### Question 5.2: Pseudo-Polynomial Complexity Proof (Slip Test 25 Aug 2025, Puzzle 4)
> **Problem Statement:**  
> In the coin denominations problem, if there are $n$ different denominations and the total amount is $A$, the algorithm studied in class runs in $O(n \cdot A)$ time.  
> **Is this a polynomial-time algorithm?**  
> A. YES  
> B. NO

#### Detailed Solution & Proof:
- **Correct Answer:** **B. NO! It is PSEUDO-POLYNOMIAL.**
- **Proof:**
  1. Input size $L$ is the number of bits needed to represent $A$: $L = \lceil \log_2(A + 1) \rceil$.
  2. Numerical value of $A$ in terms of bit length: $A \approx 2^L$.
  3. Substituting into runtime: $O(n \cdot A) = O(n \cdot 2^L)$.
  4. The running time is **exponential** in the input bit-length $L$. Hence, pseudo-polynomial.

---

### Question 5.3: Loop Order Invariant: Combinations vs Permutations
> **Problem Statement:**  
> Explain how changing the nesting order of loops in Coin Change changes the problem from finding **Combinations** to finding **Permutations**.

#### Detailed Solution:
- **Loop 1: Coin Outer, Amount Inner $\implies$ Combinations (Distinct Sets):**
  ```cpp
  for (int coin : coins)
      for (int a = coin; a <= target; ++a)
          dp[a] += dp[a - coin];
  ```
  Each coin is considered sequentially. Order does not matter: $[1, 2]$ is formed, but $[2, 1]$ is never double-counted.
- **Loop 2: Amount Outer, Coin Inner $\implies$ Permutations (Order Matters):**
  ```cpp
  for (int a = 1; a <= target; ++a)
      for (int coin : coins)
          if (a >= coin) dp[a] += dp[a - coin];
  ```
  Every transition at amount $a$ considers all coins as the potential *last* coin, counting distinct sequence orderings.

---

# Topic 6: Dynamic Programming — LCS & Edit Distance

---

### Question 6.1: Levenshtein Distance Evaluations (Slip Test 2, 17 Aug 2026, Puzzle 1)
> **Problem Statement:**  
> Using the alignment and word-evolution notation, evaluate:  
> 1. $\text{dist}(\text{"sleeveless"}, \text{"lessens"})$  
> 2. $\text{dist}(\text{"intelligent"}, \text{"belligerent"})$  
> 3. $\text{dist}(\text{"melanin"}, \text{"millennial"})$

#### Detailed Solutions:
1. **$\text{dist}(\text{"sleeveless"}, \text{"lessens"}) = \mathbf{6}$:**
   - Alignment:
     ```
     s   l   e   e   v   e   l   e   s   s
         l       e       s   s   e   n   s
     ```
   - Operations: Delete `s`, delete `e`, delete `v`, sub `e` $\to$ `s`, sub `l` $\to$ `s`, sub `s` $\to$ `n` (3 deletions + 3 substitutions = 6).
2. **$\text{dist}(\text{"intelligent"}, \text{"belligerent"}) = \mathbf{5}$:**
   - Alignment:
     ```
     i   n   t   e   l   l   i   g           e   n   t
             b   e   l   l   i   g   e   r   e   n   t
     ```
   - Operations: Delete `i`, delete `n`, sub `t` $\to$ `b`, insert `e`, insert `r` (2 deletions + 1 substitution + 2 insertions = 5).
3. **$\text{dist}(\text{"melanin"}, \text{"millennial"}) = \mathbf{6}$:**
   - Alignment:
     ```
     m       e   l       a   n   i       n
     m   i   l   l   e   n   n   i   a   l
     ```
   - Operations: Insert `i`, sub `e` $\to$ `l`, insert `e`, sub `a` $\to$ `n`, insert `a`, sub `n` $\to$ `l` (3 insertions + 3 substitutions = 6).

---

### Question 6.2: Class Lecture Trace: "bafdecafdc" vs "abcdef" (Slide 31)
> **Problem Statement:**  
> In the lecture slides on LCS (`Longest common subsequence.pdf`), evaluate cell `X` located at row `e` and column `b` when aligning $X = \text{"bafdecafdc"}$ against $Y = \text{"abcdef"}$. What is the final LCS length?

#### Detailed Solution:
- At cell row `e`, column `b` (prefix `b` of $X$ and prefix `abcde` of $Y$):
  - Characters `b` and `e` mismatch.
  - $DP[\text{row } e, \text{col } b] = \max(DP[\text{row } e-1, \text{col } b], DP[\text{row } e, \emptyset]) = \max(1, 0) = \mathbf{1}$.
- Bottom-right cell $DP[6][10] = \mathbf{4}$.
- Valid LCS strings of length 4 include: `"adef"`, `"bdef"`, `"acdc"`, `"bcdc"`.

---

# Topic 7: Dynamic Programming — Knapsack & Multiple Knapsacks

---

### Question 7.1: The Two Thieves Knapsack (Slip Test 3, 31 Aug 2026, Puzzle 1)
> **Problem Statement:**  
> Two thieves have carrying capacities $W_1 = 15$ and $W_2 = 17$. The items available are:  
>
> | Item | Item 1 | Item 2 | Item 3 | Item 4 | Item 5 | Item 6 | Item 7 |
> | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
> | **Value ($v_i$)** | 24 | 23 | 18 | 20 | 13 | 14 | 11 |
> | **Weight ($w_i$)** | 8 | 7 | 6 | 5 | 4 | 4 | 3 |
>
> What is the maximum total value of items they can steal?

#### Detailed Solution:
1. **Total Parameters:**
   - Total weight $= 8 + 7 + 6 + 5 + 4 + 4 + 3 = 37$.
   - Combined capacity $= 15 + 17 = 32$.
   - Total value $= 24 + 23 + 18 + 20 + 13 + 14 + 11 = 123$.
2. **Minimizing Left-Behind Value:**
   - Must leave behind items with total weight $\ge 37 - 32 = 5$.
   - Candidates with weight $\ge 5$:
     - Item 4 (wt 5, val 20) $\implies$ leaves value 20.
     - **Item 3 (wt 6, val 18) $\implies$ leaves value 18!**
     - Item 2 (wt 7, val 23) $\implies$ leaves value 23.
   - Leaving behind **Item 3 alone** sacrifices the absolute minimum value (**18**)!
   - Upper bound on stolen value $= 123 - 18 = \mathbf{105}$.
3. **Partitioning the Remaining 6 Items:**
   - **Thief 1 (Capacity 15):** Item 1 (wt 8, val 24) + Item 2 (wt 7, val 23) $\implies$ Weight $= 15 \le 15$, Value $= 47$.
   - **Thief 2 (Capacity 17):** Item 4 (5, 20) + Item 5 (4, 13) + Item 6 (4, 14) + Item 7 (3, 11) $\implies$ Weight $= 16 \le 17$, Value $= 58$.
   - **Total Value Stolen:** $47 + 58 = \mathbf{105}$.

---

### Question 7.2: Monotonic Weights and Values (Slip Test 25 Aug 2025, Puzzle 1)
> **Problem Statement:**  
> In 0/1 knapsack, suppose items sorted by increasing weight are also sorted by decreasing value:  
> $w_1 \le w_2 \le \dots \le w_n$ AND $v_1 \ge v_2 \ge \dots \ge v_n$.  
> Does the greedy algorithm work?

#### Detailed Solution:
- **Verdict:** **YES! Greedy always gives an optimal solution.**
- **Reason:** Item 1 has the lowest weight AND the highest value. It strictly dominates all other items in ratio and absolute utility. Any optimal solution that omits Item 1 can exchange any included item for Item 1 without increasing weight while strictly increasing or maintaining value.

---

### Question 7.3: Exponential Values (Slip Test 25 Aug 2025, Puzzle 3)
> **Problem Statement:**  
> In 0/1 knapsack, suppose $v_{i+1} = 2 v_i$ for all items. Which greedy strategy is guaranteed to find an optimal solution?

#### Detailed Solution:
- **Verdict:** **Greedily choosing the item with the HIGHEST VALUE (regardless of weight) is strictly optimal!**
- **Reason:** The sum of all smaller items combined is:
  $$\sum_{j=1}^{k-1} v_j = v_1(2^{k-1} - 1) < v_k$$
  Even taking every single smaller item cannot compensate for omitting item $k$. Therefore, if item $k$ fits in the remaining capacity, you must take it.

---

# Topic 8: Divide-and-Conquer — Integer Multiplication

---

### Question 8.1: Karatsuba Recurrence & Change of Base
> **Problem Statement:**  
> Derive the Karatsuba recurrence relation $T(n) = 3T(n/2) + O(n)$ from Gauss's trick, and solve for its exact asymptotic time complexity using the change of base formula.

#### Detailed Solution:
1. **Gauss's Trick:**
   To compute $(X_1 \cdot 10^{n/2} + X_0)(Y_1 \cdot 10^{n/2} + Y_0) = X_1 Y_1 \cdot 10^n + (X_1 Y_0 + X_0 Y_1) \cdot 10^{n/2} + X_0 Y_0$:
   Compute:
   - $z_2 = X_1 \times Y_1$
   - $z_0 = X_0 \times Y_0$
   - $z_1 = (X_1 + X_0) \times (Y_1 + Y_0)$
   Middle term $= z_1 - z_2 - z_0 = X_1 Y_0 + X_0 Y_1$.
2. **Recurrence:** 3 subproblems of size $n/2$, plus $O(n)$ additions and shifts:
   $$T(n) = 3T(n/2) + O(n)$$
3. **Master Theorem & Change of Base:**
   $$\log_b a = \log_2 3 = \frac{\ln 3}{\ln 2} \approx \frac{1.098612}{0.693147} \approx \mathbf{1.58496}$$
   $$T(n) = \mathbf{\Theta(n^{\log_2 3}) \approx \Theta(n^{1.585})}$$

---

# Topic 9: Divide-and-Conquer — Matrix Multiplication & MCM

---

### Question 9.1: Reconstructing Matrix Product $C$ (Slip Test 4, 3 Sept 2026, Puzzle 1)
> **Problem Statement:**  
> Using Strassen’s 7 variables $m_1 \dots m_7$, express $c_{11}, c_{21}, c_{22}$ using only addition and subtraction operations.

#### Detailed Solution:
- **$c_{11} = m_1 + m_4 - m_5 + m_7$**
- **$c_{12} = m_3 + m_5$** *(given)*
- **$c_{21} = m_2 + m_4$**
- **$c_{22} = m_1 - m_2 + m_3 + m_6$**

---

### Question 9.2: Total Arithmetic Operations Count (Slip Test 4, Bonus [1 Mark])
> **Problem Statement:**  
> Derive the exact total number of arithmetic operations $(\times, +, -)$ performed by Strassen's algorithm to multiply two $n \times n$ matrices.

#### Detailed Solution:
1. **Multiplications:** $M(n) = 7 M(n/2) \implies M(n) = n^{\log_2 7}$.
2. **Additions / Subtractions:** 18 additions of $(n/2) \times (n/2)$ submatrices per step:
   $$A(n) = 7 A(n/2) + 18 \left(\frac{n}{2}\right)^2 = 7 A(n/2) + \frac{9}{2} n^2, \quad A(1) = 0$$
   Solving the recurrence gives:
   $$A(n) = 6 n^{\log_2 7} - 6 n^2$$
3. **Total Arithmetic Operations:**
   $$\text{Total Ops}(n) = M(n) + A(n) = n^{\log_2 7} + (6 n^{\log_2 7} - 6 n^2) = \mathbf{7 n^{\log_2 7} - 6 n^2}$$
   *(For $n = 2$: $7(7) - 6(4) = 49 - 24 = \mathbf{25 \text{ operations}}$: 7 mults + 18 adds/subs).*

---

### Question 9.3: DeepMind AlphaTensor (Slip Test 4, Puzzle 3)
> **Problem Statement:**  
> In October 2022, DeepMind's AlphaTensor multiplied two $4 \times 4$ binary matrices using just 47 multiplications. Write the recurrence for $T(n)$ in terms of $T(n/4)$ and solve it.

#### Detailed Solution:
- Dividing $n \times n$ into sixteen $(n/4) \times (n/4)$ submatrices:
  $$T(n) = 47 T(n/4) + \Theta(n^2), \quad T(1) = 1$$
- Critical exponent:
  $$\log_4 47 = \frac{\ln 47}{\ln 4} \approx \frac{3.85015}{1.38629} \approx \mathbf{2.7773}$$
- By Master Theorem:
  $$T(n) = \mathbf{\Theta(n^{\log_4 47}) \approx \Theta(n^{2.7773})}$$
  *(Strictly faster than Strassen's $O(n^{2.8074})$).*

---

# Topic 10: Recurrences & Matrix Exponentiation (Amoeba Population)

---

### Question 10.1: Amoeba Reproduction Rules (Slip Test, 4 Aug 2025, Puzzles 1–4)
> **Problem Statement:**  
> Each amoeba starts giving birth 1 hour after its birth, gives birth to 1 new amoeba every hour for 3 hours, and dies immediately after its 3rd birth.  
> At $t = 0$, there is 1 amoeba. Let $F(t)$ be the number of amoebas alive immediately after time $t$.  
> 1. Compute $F(5), F(6), F(7), F(8)$.  
> 2. What is the matrix $A$ such that $A \begin{pmatrix} F(t-3) \\ F(t-2) \\ F(t-1) \end{pmatrix} = \begin{pmatrix} F(t-2) \\ F(t-1) \\ F(t) \end{pmatrix}$?  
> 3. Provide an $O(\log t)$ algorithm to compute $F(t)$.

#### Detailed Solution:
1. **The Recurrence Relation:**
   - An amoeba alive at time $t$ is either:
     - Born at $t$ (parents are amoebas alive and producing at $t-1, t-2, t-3$).
     - Or surviving from previous hours.
   - Tracing populations:
     - $F(0) = 1$
     - $F(1) = 2$
     - $F(2) = 4$
     - $F(3) = 7$
     - $F(4) = 13$
   - Notice the pattern: **$F(t) = F(t-1) + F(t-2) + F(t-3)$** (Tribonacci sequence)!
     - $F(5) = 4 + 7 + 13 = \mathbf{24}$
     - $F(6) = 7 + 13 + 24 = \mathbf{44}$
     - $F(7) = 13 + 24 + 44 = \mathbf{81}$
     - $F(8) = 24 + 44 + 81 = \mathbf{149}$
2. **Transition Matrix:**
   $$A = \begin{pmatrix} 0 & 1 & 0 \\ 0 & 0 & 1 \\ 1 & 1 & 1 \end{pmatrix}$$
3. **$O(\log t)$ Algorithm via Matrix Exponentiation:**
   $$\begin{pmatrix} F(t-2) \\ F(t-1) \\ F(t) \end{pmatrix} = A^{t-2} \begin{pmatrix} F(0) \\ F(1) \\ F(2) \end{pmatrix} = \begin{pmatrix} 0 & 1 & 0 \\ 0 & 0 & 1 \\ 1 & 1 & 1 \end{pmatrix}^{t-2} \begin{pmatrix} 1 \\ 2 \\ 4 \end{pmatrix}$$
   Computing $A^{t-2}$ using binary exponentiation takes **$O(3^3 \log t) = \mathbf{O(\log t)}$ time**!

---

# Topic 11: Disjoint Set Union (DSU) & Grid Pathfinding

---

### Question 11.1: Friendship Queries & DSU (EndSem 2025, Puzzle 5)
> **Problem Statement:**  
> Explain how Disjoint Set Union (DSU) with **Path Compression** and **Union by Rank** handles dynamic friendship queries and connected components. What is the amortized complexity per operation?

#### Detailed Solution:
- **Operations:**
  - `find(x)`: Recursively follows parent pointers to the set representative, applying path compression (`parent[x] = find(parent[x])`) to flatten the tree.
  - `union(x, y)`: Finds roots $r_x$ and $r_y$. Attaches the tree of lower rank under the tree of higher rank. If ranks are equal, increment root rank by 1.
- **Complexity:** $O(m \cdot \alpha(n))$ for $m$ operations on $n$ elements, where $\alpha(n) \le 4$ is the inverse Ackermann function. Nearly $O(1)$ amortized per query!

---

### Question 11.2: Grid Navigation & BFS (EndSem 2025, Puzzle 7)
> **Problem Statement:**  
> On an unweighted $N \times M$ grid with obstacles, devise an algorithm to find the shortest path from start $(0, 0)$ to destination $(N-1, M-1)$.

#### Detailed Solution:
- **Algorithm:** Standard **Breadth-First Search (BFS)** using a FIFO queue.
- **Why BFS?** On unweighted graphs (where each grid move has unit edge weight 1), BFS visits nodes in strictly non-decreasing order of distance from the source, guaranteeing that the first time the destination is dequeued, the shortest path distance is found.
- **Complexity:** $O(N \cdot M)$ time and space. Backtracking via `parent[r][c]` yields the exact sequence of moves.
