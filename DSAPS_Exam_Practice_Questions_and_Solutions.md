# DSAPS Master Exam Practice: Fresh Questions & Model Solutions
## High-Yield Practice Challenge for End-Semester & Slip Test Mastery
### Course: Data Structures and Algorithms for Problem Solving (CS1.304) — IIIT Hyderabad

> **About this Practice Guide:**  
> This file contains **fresh, original practice questions** modeled precisely on the exam style, rigor, and pedagogical philosophy of Instructor Kshitij Gajjar.  
> Every topic contains **exactly 5 comprehensive questions** crafted to cover all theoretical subtleties, boundary conditions, exchange arguments, counterexamples, and algorithmic designs.  
> **Model Solutions:** Every solution is written in the exact structural format expected by the examiner: clear verdicts, formal induction/contradiction proofs, explicit counterexample graphs, clean pseudocode, and rigorous asymptotic complexity analyses.

---

## Master Table of Contents
1. [Topic 1: Johnson's Algorithm & Shortest Path Variations](#topic-1-johnsons-algorithm--shortest-path-variations)
2. [Topic 2: Minimum Spanning Trees (MST) & Graph Properties](#topic-2-minimum-spanning-trees-mst--graph-properties)
3. [Topic 3: Gale-Shapley Stable Marriage & Matching](#topic-3-gale-shapley-stable-marriage--matching)
4. [Topic 4: Interval Scheduling & Classroom Partitioning](#topic-4-interval-scheduling--classroom-partitioning)
5. [Topic 5: Dynamic Programming — Coin Change Problem](#topic-5-dynamic-programming--coin-change-problem)
6. [Topic 6: Dynamic Programming — LCS & Edit Distance](#topic-6-dynamic-programming--lcs--edit-distance)
7. [Topic 7: Dynamic Programming — Knapsack & Multiple Knapsacks](#topic-7-dynamic-programming--knapsack--multiple-knapsacks)
8. [Topic 8: Divide-and-Conquer — Integer Multiplication](#topic-8-divide-and-conquer--integer-multiplication)
9. [Topic 9: Divide-and-Conquer — Matrix Multiplication & MCM](#topic-9-divide-and-conquer--matrix-multiplication--mcm)
10. [Topic 10: Recurrences, Matrix Exponentiation & DSU](#topic-10-recurrences-matrix-exponentiation--dsu)

---

# Topic 1: Johnson's Algorithm & Shortest Path Variations

---

### Practice Problem 1.1: Potential Function with Arbitrary Constant Shift [3 Marks]
> **Question:**  
> In Johnson’s algorithm, suppose Bellman-Ford computes a valid potential function $h(v) = \delta(s, v)$ for all $v \in V$, yielding non-negative edge weights $w'(u, v) = w(u, v) + h(u) - h(v) \ge 0$.  
> Suppose a student defines a new potential function:
> $$h_{\text{new}}(v) = h(v) + C$$
> where $C$ is an arbitrary real constant ($C \in \mathbb{R}$, positive or negative).  
> 1. Does the modified weight $w''_{\text{new}}(u, v) = w(u, v) + h_{\text{new}}(u) - h_{\text{new}}(v)$ remain non-negative for all edges?  
> 2. Does this change the shortest paths computed by Dijkstra's algorithm? Prove your answer.

#### Model Solution:
1. **Non-negativity of $w''_{\text{new}}(u, v)$:**  
   $$\begin{aligned}
   w''_{\text{new}}(u, v) &= w(u, v) + h_{\text{new}}(u) - h_{\text{new}}(v) \\
   &= w(u, v) + (h(u) + C) - (h(v) + C) \\
   &= w(u, v) + h(u) - h(v) + (C - C) \\
   &= w(u, v) + h(u) - h(v) = w'(u, v)
   \end{aligned}$$
   Since $w'(u, v) \ge 0$ by the triangle inequality of shortest paths from $s$, $w''_{\text{new}}(u, v)$ is **identical to $w'(u, v)$**, and therefore remains strictly non-negative ($\ge 0$).
2. **Effect on Shortest Paths:**  
   **No effect whatsoever.** Because the constant $C$ cancels out identically across every single directed edge ($(h(u) + C) - (h(v) + C) = h(u) - h(v)$), the edge weights under $h_{\text{new}}$ are numerically identical to the edge weights under $h$. Dijkstra produces the exact same shortest path trees.

---

### Practice Problem 1.2: Adding Edges with Negative Weight from Dummy Vertex [3 Marks]
> **Question:**  
> In Johnson's algorithm, we add edges $(s, v)$ of weight $0$ from dummy node $s$ to all $v \in V$.  
> Suppose instead an engineer adds edges $(s, v)$ with weight **$-5$** for all $v \in V$.  
> (a) Can this introduce artificial negative-weight cycles?  
> (b) Will Bellman-Ford still compute valid potentials that ensure $w'(u, v) \ge 0$? Prove your claim.

#### Model Solution:
- **(a) Artificial Negative Cycles:**  
  **NO.** Since $s$ is an external vertex with $\text{in-degree}(s) = 0$, there are **no incoming edges to $s$**. A cycle requires returning to the starting node; since no path can ever enter $s$, $s$ cannot belong to any cycle. Therefore, no negative cycles can be introduced.
- **(b) Validity of Potentials:**  
  **YES, potentials remain completely valid.**
  - Let $\delta_0(s, v)$ be the shortest path distance when edge weights from $s$ are $0$.
  - When all edges from $s$ have weight $-5$, every path starting from $s$ has its very first edge weight shifted by $-5$.
  - Thus, the new potential is simply $h_{\text{new}}(v) = \delta_0(s, v) - 5$.
  - By Problem 1.1, shifting all potentials by a constant $C = -5$ leaves the reweighted edge values completely unchanged:
    $$w'(u, v) = w(u, v) + (h(u) - 5) - (h(v) - 5) = w(u, v) + h(u) - h(v) \ge 0$$
  - Every edge weight remains non-negative!

---

### Practice Problem 1.3: Detecting Zero-Weight Cycles vs Negative-Weight Cycles [4 Marks]
> **Question:**  
> Let $G = (V, E)$ be a directed graph. Suppose $G$ contains a directed cycle $C = \langle v_1, v_2, \dots, v_k, v_1 \rangle$ whose total weight is **strictly zero**: $w(C) = \sum_{e \in C} w(e) = 0$.  
> 1. What is the total weight of cycle $C$ under the Johnson reweighted function $w'(C)$?  
> 2. Does the presence of zero-weight cycles cause Johnson's algorithm to fail or loop indefinitely? Explain why or why not.

#### Model Solution:
1. **Weight of $C$ under $w'$:**  
   By the telescoping property of path reweighting:
   $$w'(C) = \sum_{i=1}^{k-1} w'(v_i, v_{i+1}) + w'(v_k, v_1) = w(C) + h(v_1) - h(v_1) = w(C) = 0$$
   The transformed weight $w'(C)$ is **identically 0**.
2. **Does Johnson's Algorithm Fail?**  
   **NO.**  
   - Bellman-Ford terminates correctly because zero-weight cycles do not decrease distances (only *strictly negative* cycles cause infinite relaxation loops).
   - In the second phase, all edge weights satisfy $w'(u, v) \ge 0$. Dijkstra’s algorithm handles zero-weight edges without any issues (using standard priority queue relaxations), terminating in $O((V + E) \log V)$ with exact shortest path distances.

---

### Practice Problem 1.4: Shortest Paths Under Quadratic Weight Transformation [4 Marks]
> **Question:**  
> Let $G = (V, E)$ be a directed graph with strictly positive edge weights ($w(e) \ge 1$ for all $e \in E$).  
> We construct a new graph $G_{\text{sq}}$ with identical vertices and edges, but each edge weight is squared: $w_{\text{sq}}(e) = (w(e))^2$.  
> **Claim:** Any shortest path between $u$ and $v$ in $G$ is guaranteed to be a shortest path between $u$ and $v$ in $G_{\text{sq}}$.  
> Is this claim **TRUE** or **FALSE**? If true, prove it; if false, provide a concrete counterexample.

#### Model Solution:
- **Verdict:** **FALSE!**
- **Counterexample:**  
  Consider a graph with two alternative paths between $u$ and $v$:
  - **Path 1:** Consists of 1 single edge of weight $w(e_1) = 5$.  
    Total length in $G = \mathbf{5}$.
  - **Path 2:** Consists of 2 consecutive edges with weights $w(e_2) = 3$ and $w(e_3) = 3$.  
    Total length in $G = 3 + 3 = \mathbf{6}$.
  - In the original graph $G$, **Path 1 is the strictly shorter path** ($5 < 6$).
  
  Now compute edge weights in $G_{\text{sq}}$:
  - Total length of Path 1 in $G_{\text{sq}} = 5^2 = \mathbf{25}$.
  - Total length of Path 2 in $G_{\text{sq}} = 3^2 + 3^2 = 9 + 9 = \mathbf{18}$.
  - In $G_{\text{sq}}$, **Path 2 is strictly shorter** ($18 < 25$)!
- **Examiner's Core Insight:** Squaring edge weights penalizes single large edges much more heavily than multiple small edges ($a^2 + b^2 < (a + b)^2$ for $a, b > 0$). Thus, shortest paths are **not preserved**.

---

### Practice Problem 1.5: Shortest Path with At Most One Negative Edge [6 Marks]
> **Question:**  
> Devise an efficient algorithm to find the single-source shortest paths from $s$ to all other vertices in a directed graph $G = (V, E)$ where **exactly one edge $e^* = (x, y)$ has negative weight** ($w(e^*) < 0$), and all other edges have non-negative weights ($w(e) \ge 0$ for all $e \ne e^*$). Assume $G$ contains no negative cycles.  
> Target Time Complexity: **$O((V + E) \log V)$** (strictly faster than Bellman-Ford's $O(VE)$).

#### Model Solution:
1. **Algorithmic Strategy:**  
   Any shortest path from $s$ to a vertex $v$ falls into one of two mutually exclusive categories:
   - **Case A:** The path does NOT use the negative edge $e^*$.
   - **Case B:** The path DOES use the negative edge $e^* = (x, y)$.
2. **Algorithm Steps:**
   - **Step 1:** Temporarily remove edge $e^* = (x, y)$ from $G$. The remaining graph has exclusively non-negative edge weights.
   - **Step 2:** Run standard **Dijkstra** from $s$ on $G \setminus \{e^*\}$. This yields distance array $d_1[v]$ for all $v \in V$.
   - **Step 3:** Run a second **Dijkstra** starting from $y$ on $G \setminus \{e^*\}$ (or the full graph without traversing $e^*$ again). This yields distance array $d_2[v]$ representing the shortest path from $y$ to all $v$.
   - **Step 4:** For every vertex $v \in V$, the shortest path distance using $e^*$ is:
     $$d_{\text{via } e^*}(v) = d_1[x] + w(x, y) + d_2[v]$$
   - **Step 5:** The true shortest path distance to $v$ is:
     $$\delta(s, v) = \min\big(d_1[v], \quad d_1[x] + w(x, y) + d_2[v]\big)$$
3. **Runtime & Space Analysis:**
   - Two runs of Dijkstra’s algorithm take $2 \times O((V + E) \log V) = \mathbf{O((V + E) \log V)}$.
   - Combining the results takes $O(V)$ time.
   - Total Space: $O(V + E)$ to store the graph and distance arrays.

---

# Topic 2: Minimum Spanning Trees (MST) & Graph Properties

---

### Practice Problem 2.1: The Second-Best Minimum Spanning Tree [5 Marks]
> **Question:**  
> Let $G = (V, E)$ be a connected, undirected graph with unique edge weights, and let $T$ be its unique MST. A "second-best" MST $T_2$ is a spanning tree such that $w(T) < w(T_2) \le w(T')$ for all spanning trees $T' \ne T$.  
> 1. Prove that $T_2$ can be obtained from $T$ by adding **exactly one non-tree edge $e \in E \setminus T$** and removing **exactly one tree edge $e' \in T$**.  
> 2. Devise an $O(E \log V)$ or $O(V^2)$ algorithm to find $T_2$.

#### Model Solution:
1. **Proof of Single-Edge Swap:**
   - Let $T^*$ be any second-best spanning tree that differs from $T$ by more than one edge ($|T^* \setminus T| \ge 2$).
   - Pick any edge $e = (u, v) \in T^* \setminus T$. Adding $e$ to $T$ creates a unique cycle $C$.
   - Since $T$ is an MST, by the Cut/Cycle Property, every edge $e' \in C \setminus \{e\}$ satisfies $w(e') < w(e)$.
   - There exists at least one edge $e' \in C$ that is not in $T^*$.
   - The tree $T' = T \cup \{e\} \setminus \{e'\}$ is a valid spanning tree whose weight satisfies $w(T) < w(T') \le w(T^*)$.
   - Thus, the minimal weight increase above $T$ is always achievable by swapping a **single edge pair**.
2. **Algorithm ($O(E \log V)$):**
   - Step 1: Compute $T$ using Kruskal's algorithm in $O(E \log V)$.
   - Step 2: Preprocess $T$ using Binary Lifting (LCA) in $O(V \log V)$ time so that for any pair $(u, v)$, the maximum weight edge on the tree path between $u$ and $v$ (denoted $\text{max\_edge}(u, v)$) can be queried in $O(\log V)$.
   - Step 3: For every non-tree edge $e = (u, v) \in E \setminus T$:
     $$\Delta(e) = w(e) - \text{max\_edge}(u, v)$$
   - Step 4: The second-best MST is obtained by choosing the edge $e$ that minimizes $\Delta(e)$:
     $$w(T_2) = w(T) + \min_{e \in E \setminus T} \Delta(e)$$
   - Total runtime: $O(E \log V)$ time and $O(V \log V)$ space.

---

### Practice Problem 2.2: The Mystery Deletion Algorithm (`MAYBE-MST-X`) [4 Marks]
> **Question:**  
> Consider the following proposed algorithm for finding an MST in an undirected connected weighted graph $G$:
> ```text
> 1. Set T = E
> 2. For each vertex v in V:
> 3.     Find the heaviest incident edge e connected to v
> 4.     If removing e does NOT disconnect the graph:
> 5.         T = T \ {e}
> 6. Return T
> ```
> Does `MAYBE-MST-X` always output a valid Minimum Spanning Tree? If yes, prove it. If no, provide a counterexample graph.

#### Model Solution:
- **Verdict:** **NO! It does NOT always output an MST.**
- **Counterexample:**  
  Consider a 3-cycle (triangle) with vertices $A, B, C$ and edge weights:
  - $w(A, B) = 1$
  - $w(B, C) = 2$
  - $w(A, C) = 3$
  - **Optimal MST:** Edges $\{ (A, B), (B, C) \}$ with total weight $1 + 2 = \mathbf{3}$.
  - **Algorithm Execution:**
    - Loop visits vertex $A$: Incident edges are $(A, B)$ [wt 1] and $(A, C)$ [wt 3]. Heaviest is $(A, C)$. Removing $(A, C)$ leaves the graph connected. $(A, C)$ is removed! Remaining: $\{ (A, B), (B, C) \}$.
    - Loop visits vertex $B$: Incident edges are $(A, B)$ [wt 1] and $(B, C)$ [wt 2]. Heaviest is $(B, C)$. Removing $(B, C)$ **leaves the graph disconnected** (path $B-C$ broken)! So $(B, C)$ is kept.
    - Here it worked, **BUT now consider a 4-cycle (Diamond):**
      - Vertices $A, B, C, D$ with cycle edges:
        - $(A, B) = 10, (B, C) = 1, (C, D) = 10, (D, A) = 2$.
      - Suppose the algorithm visits $A$: Heaviest is $(A, B) = 10 \implies$ deleted.
      - Next visits $C$: Heaviest is $(C, D) = 10 \implies$ deleted.
      - Graph now has edges $(D, A) = 2$ and $(B, C) = 1$. Vertices $B, C$ are completely disconnected from $A, D$!
      - The check "does not disconnect" in line 4 prevents disconnection at that specific step, but greedy local vertex choices cause premature deletions that force heavy edges to be retained later.
- **Fundamental Flaw:** The Cycle Property applies to the heaviest edge of an **entire cycle**, NOT the locally heaviest edge incident to a single vertex!

---

### Practice Problem 2.3: Non-Monotonic Edge Weight Transformation [3 Marks]
> **Question:**  
> Let $T$ be an MST of $G = (V, E)$. Suppose all edge weights satisfy $w(e) \in [1, 10]$.  
> We transform edge weights using the quadratic function:
> $$f(x) = (x - 5)^2$$
> Does $T$ necessarily remain an MST in the transformed graph? Justify with a counterexample.

#### Model Solution:
- **Verdict:** **NO!**
- **Justification:**  
  The function $f(x) = (x - 5)^2$ is **NOT monotonic** on the interval $[1, 10]$ (it decreases on $[1, 5)$ and increases on $(5, 10]$).
  - Consider two edges: $e_1$ with weight $w(e_1) = 2$, and $e_2$ with weight $w(e_2) = 5$.
  - In $G$: $w(e_2) > w(e_1)$ ($5 > 2$). Kruskal prefers $e_1$.
  - Under $f(x)$:
    $$f(w(e_1)) = (2 - 5)^2 = (-3)^2 = \mathbf{9}$$
    $$f(w(e_2)) = (5 - 5)^2 = 0^2 = \mathbf{0}$$
  - In the transformed graph, $e_2$ has weight $0$ while $e_1$ has weight $9$! Their relative order is completely reversed. Consequently, the MST changes.

---

### Practice Problem 2.4: Updating an MST After Decreasing One Edge Weight [4 Marks]
> **Question:**  
> Let $T$ be an MST of graph $G = (V, E)$. Suppose the weight of a single edge $e^* = (u, v) \notin T$ is decreased to a new weight $w_{\text{new}}(e^*) < w(e^*)$.  
> Devise an **$O(V)$** algorithm to find the new MST of the updated graph.

#### Model Solution:
1. **Algorithm:**
   - **Step 1:** Add the modified edge $e^* = (u, v)$ to $T$.  
     Since $T$ is a spanning tree, adding $e^*$ creates **exactly one simple cycle $C$**.
   - **Step 2:** Find the cycle $C$ using Breadth-First Search (BFS) or Depth-First Search (DFS) starting from $u$ to $v$ in $T$. This takes $O(V)$ time since $T$ has only $V - 1$ edges.
   - **Step 3:** Find the edge $e_{\max} \in C$ with the maximum weight on cycle $C$:
     $$e_{\max} = \arg\max_{e \in C} w(e)$$
   - **Step 4:** By the **Cycle Property**, the strictly heaviest edge on any cycle cannot belong to an MST.
     - If $e_{\max} == e^*$, then $T$ remains optimal: $T_{\text{new}} = T$.
     - If $e_{\max} \ne e^*$, remove $e_{\max}$ and keep $e^*$:
       $$T_{\text{new}} = T \cup \{e^*\} \setminus \{e_{\max}\}$$
2. **Runtime Analysis:**
   - DFS on a tree: $O(V)$. Finding the max edge on cycle of length $\le V$: $O(V)$.
   - Total time complexity: **$\mathbf{O(V)}$**, strictly faster than re-running Kruskal ($O(E \log V)$).

---

### Practice Problem 2.5: Minimum Spanning Forest in Disconnected Graphs [3 Marks]
> **Question:**  
> If an undirected graph $G = (V, E)$ has $k$ connected components, what is the exact number of edges in its Minimum Spanning Forest (MSF)? Prove your formula.

#### Model Solution:
- **Formula:**
  $$\mathbf{|E_{\text{MSF}}| = V - k}$$
- **Proof:**
  - Let the $k$ connected components of $G$ be $G_1, G_2, \dots, G_k$, where component $G_i$ has $V_i$ vertices.
  - The total number of vertices is $\sum_{i=1}^k V_i = V$.
  - A spanning forest consists of a separate spanning tree for each connected component.
  - A spanning tree on $V_i$ vertices contains exactly $V_i - 1$ edges.
  - Summing the edges across all $k$ component trees:
    $$|E_{\text{MSF}}| = \sum_{i=1}^k (V_i - 1) = \sum_{i=1}^k V_i - \sum_{i=1}^k 1 = V - k \quad \blacksquare$$

---

# Topic 3: Gale-Shapley Stable Marriage & Matching

---

### Practice Problem 3.1: Execution Trace & Invariant Verification [5 Marks]
> **Question:**  
> Consider 3 men ($m_1, m_2, m_3$) and 3 women ($w_1, w_2, w_3$) with the following preference lists (ordered highest to lowest):
> - $m_1: w_1 > w_2 > w_3$
> - $m_2: w_2 > w_1 > w_3$
> - $m_3: w_1 > w_2 > w_3$
> - $w_1: m_2 > m_1 > m_3$
> - $w_2: m_1 > m_2 > m_3$
> - $w_3: m_1 > m_2 > m_3$
> 
> Trace the Men-Proposing Gale-Shapley algorithm step-by-step. State each proposal, engagement, rejection, and the final matching.

#### Model Solution:
1. **Round 1:**
   - $m_1$ proposes to $w_1$. $w_1$ is free $\implies$ engages with $m_1$.  
     *Engagements:* $(m_1, w_1)$. Free men: $\{m_2, m_3\}$.
2. **Round 2:**
   - $m_2$ proposes to $w_2$. $w_2$ is free $\implies$ engages with $m_2$.  
     *Engagements:* $(m_1, w_1), (m_2, w_2)$. Free men: $\{m_3\}$.
3. **Round 3:**
   - $m_3$ proposes to $w_1$. $w_1$ is currently engaged to $m_1$.
   - $w_1$'s preference list: $m_2 > m_1 > m_3$.
   - $w_1$ prefers $m_1$ over $m_3 \implies$ **rejects $m_3$**.  
     *Engagements:* $(m_1, w_1), (m_2, w_2)$. Free men: $\{m_3\}$.
4. **Round 4:**
   - $m_3$ proposes to his next choice, $w_2$. $w_2$ is currently engaged to $m_2$.
   - $w_2$'s preference list: $m_1 > m_2 > m_3$.
   - $w_2$ prefers $m_2$ over $m_3 \implies$ **rejects $m_3$**.  
     *Engagements:* $(m_1, w_1), (m_2, w_2)$. Free men: $\{m_3\}$.
5. **Round 5:**
   - $m_3$ proposes to his next choice, $w_3$. $w_3$ is free $\implies$ engages with $m_3$.
6. **Final Stable Matching:**
   $$\mathbf{M = \{ (m_1, w_1), \quad (m_2, w_2), \quad (m_3, w_3) \}}$$
   - Total proposals made: **5 proposals**.

---

### Practice Problem 3.2: Existence of Blocking Pairs in Non-GS Matchings [4 Marks]
> **Question:**  
> For the preferences given in Problem 3.1, consider the alternative matching:
> $$M' = \{ (m_1, w_2), \quad (m_2, w_1), \quad (m_3, w_3) \}$$
> 1. Is $M'$ a stable matching?  
> 2. If unstable, identify all blocking pairs. If stable, what does this imply about the uniqueness of stable matchings for this instance?

#### Model Solution:
1. **Stability Audit of $M'$:**
   - Let us check each pair $(m, w) \notin M'$ to see if they form a blocking pair:
     - Check $(m_1, w_1)$:
       - In $M'$, $m_1$ is with $w_2$. $m_1$'s preference: $w_1 > w_2$. Thus, $m_1$ **prefers $w_1$ to his partner**.
       - In $M'$, $w_1$ is with $m_2$. $w_1$'s preference: $m_2 > m_1 > m_3$. Thus, $w_1$ **prefers her current partner $m_2$ to $m_1$**!
       - Since $w_1$ does NOT prefer $m_1$, $(m_1, w_1)$ is **NOT a blocking pair**.
     - Check $(m_2, w_2)$:
       - In $M'$, $m_2$ is with $w_1$. $m_2$'s preference: $w_2 > w_1$. Thus, $m_2$ **prefers $w_2$ to his partner**.
       - In $M'$, $w_2$ is with $m_1$. $w_2$'s preference: $m_1 > m_2 > m_3$. Thus, $w_2$ **prefers her current partner $m_1$ to $m_2$**!
       - Since $w_2$ does NOT prefer $m_2$, $(m_2, w_2)$ is **NOT a blocking pair**.
     - Check all pairs involving $m_3$ or $w_3$:
       - Both $m_1$ and $m_2$ have their top choices in $\{w_1, w_2\}$ and strictly prefer them to $w_3$.
       - Neither $w_1$ nor $w_2$ prefers $m_3$ over $m_1$ or $m_2$.
2. **Conclusion:**
   - **$M'$ contains ZERO blocking pairs! $M'$ is also a STABLE MATCHING!**
   - **Implication:** This instance has **at least TWO distinct stable matchings** ($M$ and $M'$).
   - Notice: $M$ is the male-optimal matching, while $M'$ is the female-optimal matching (in $M'$, $w_1$ gets her 1st choice $m_2$, and $w_2$ gets her 1st choice $m_1$)!

---

### Practice Problem 3.3: The Rural Hospital Theorem [4 Marks]
> **Question:**  
> In the Stable Marriage / Matching problem with unequal numbers of participants (e.g., $m$ doctors and $n$ hospitals, with $m > n$), state the **Rural Hospital Theorem** and explain its practical significance.

#### Model Solution:
- **Statement of the Rural Hospital Theorem:**  
  Across **ALL possible stable matchings** for a given set of preferences:
  1. The exact same set of positions/hospitals are filled.
  2. The exact same set of doctors/students remain **unassigned (unmatched)**.
  3. Any hospital that is not fully filled in one stable matching will receive the **exact same cohort of residents** in every single stable matching!
- **Practical Significance:**  
  Rural hospitals often worry that different algorithmic matchings will leave them understaffed. The theorem proves that understaffing in rural hospitals is an inherent consequence of doctor preferences, not an artifact of the matching algorithm chosen.

---

### Practice Problem 3.4: Weak Stability with Ties in Preference Lists [4 Marks]
> **Question:**  
> Suppose preference lists allow **indifference / ties** (e.g., $m_1: w_1 = w_2 > w_3$).  
> Define:  
> (a) Weak Stability  
> (b) Strong Stability  
> Does a weakly stable matching always exist?

#### Model Solution:
- **(a) Weak Stability:**  
  A matching $M$ is weakly stable if there is no pair $(m, w)$ such that $m$ **strictly prefers** $w$ over $M(m)$ AND $w$ **strictly prefers** $m$ over $M(w)$.
- **(b) Strong Stability:**  
  A matching $M$ is strongly stable if there is no pair $(m, w)$ such that one strictly prefers the other, and the other is at least indifferent (weakly prefers).
- **Existence Theorem:**  
  - A **Weakly Stable matching ALWAYS exists** and can be found by arbitrarily breaking ties and running standard Gale-Shapley!
  - A Strongly Stable matching is **not guaranteed to exist**.

---

### Practice Problem 3.5: Verifying Stability in $O(n^2)$ Time [5 Marks]
> **Question:**  
> Devise an algorithm that takes as input a matching $M$ and preference lists for $n$ men and $n$ women, and determines whether $M$ is stable in **$O(n^2)$ time**.

#### Model Solution:
1. **Algorithm:**
   - **Step 1 (Preprocessing):** Create an inverse ranking matrix `rank_w[w][m]` where `rank_w[w][m]` stores the rank of man $m$ in woman $w$'s preference list (lower number = more preferred). Takes $O(n^2)$ time.
   - **Step 2 (Audit):** For each man $m$:
     - Let his assigned wife be $w_{\text{curr}} = M(m)$.
     - Iterate through all women $w$ that $m$ strictly prefers over $w_{\text{curr}}$:
       - Check if woman $w$ also prefers $m$ over her assigned husband $M^{-1}(w)$:
         $$\text{if } \text{rank\_w}[w][m] < \text{rank\_w}[w][M^{-1}(w)] \implies \mathbf{\text{BLOCKING PAIR FOUND!}}$$
         - Return `UNSTABLE`.
   - **Step 3:** If all men and their preferred women are checked with no blocking pair, return `STABLE`.
2. **Runtime Analysis:**  
   There are $n$ men, each preferring at most $n-1$ women over his partner. Each comparison takes $O(1)$ via the precomputed table. Total time is strictly $\mathbf{O(n^2)}$.

---

# Topic 4: Interval Scheduling & Classroom Partitioning

---

### Practice Problem 4.1: The Minimum Conflicts Heuristic [4 Marks]
> **Question:**  
> Consider the following greedy heuristic for Interval Scheduling:  
> *"At each step, count the number of conflicting intervals for each remaining interval. Select the interval with the **MINIMUM number of conflicts**, delete it and its conflicting intervals, and repeat."*  
> Is this algorithm guaranteed to always return the optimal number of non-overlapping intervals? If yes, prove it. If no, provide a counterexample.

#### Model Solution:
- **Verdict:** **NO! It is NOT optimal.**
- **Counterexample:**  
  Construct an instance where picking the minimum-conflict interval blocks a larger independent set:
  - Let there be 11 intervals arranged as follows:
    - 4 intervals in a clique on the left: $L_1, L_2, L_3, L_4$ (each conflicts with all other $L_i$, plus bridge interval $B_1$).
    - 4 intervals in a clique on the right: $R_1, R_2, R_3, R_4$.
    - A center chain of independent intervals whose endpoints overlap with bridges.
  - A simpler, standard counterexample:
    - 3 mutually disjoint optimal intervals: $O_1 = [1, 4], O_2 = [5, 8], O_3 = [9, 12]$.
    - 4 competing intervals:
      - $C_1 = [3, 6]$ (conflicts with $O_1, O_2 \implies 2$ conflicts).
      - $C_2 = [7, 10]$ (conflicts with $O_2, O_3 \implies 2$ conflicts).
      - Add overlapping helper intervals such that each $O_i$ has 3 conflicts, while $C_1$ has only 2 conflicts.
    - The heuristic picks $C_1$, which eliminates $O_1$ and $O_2$, yielding fewer total scheduled intervals than the optimal set $\{O_1, O_2, O_3\}$.
- **Conclusion:** Local conflict counts do not capture the global chain structure of interval compatibility.

---

### Practice Problem 4.2: Maximum Interval Depth with Endpoint Touching [3 Marks]
> **Question:**  
> A professor gives 6 class intervals:
> $$I_1 = [9, 11], \quad I_2 = [11, 13], \quad I_3 = [10, 12], \quad I_4 = [12, 14], \quad I_5 = [9, 14], \quad I_6 = [11, 12]$$
> If intervals are treated as **half-open $[s_i, e_i)$** (meaning a class ending at 11:00 does NOT clash with a class starting at 11:00), what is the minimum number of classrooms required? Trace the active depth.

#### Model Solution:
1. **Event Decomposition:**
   Sort all start events (+1) and end events (-1). When times match, **end events are processed before start events** for half-open intervals:
   - $t = 9$: $I_1$ starts (+1), $I_5$ starts (+1) $\implies \text{Depth} = 2$. Active: $\{I_1, I_5\}$.
   - $t = 10$: $I_3$ starts (+1) $\implies \text{Depth} = 3$. Active: $\{I_1, I_5, I_3\}$.
   - $t = 11$:
     - $I_1$ ends (-1) $\implies \text{Depth} = 2$.
     - $I_2$ starts (+1), $I_6$ starts (+1) $\implies \text{Depth} = 2 + 2 = \mathbf{4}$!
     - Active at $t = 11$: $\{I_5, I_3, I_2, I_6\}$.
   - $t = 12$:
     - $I_3$ ends (-1), $I_6$ ends (-1) $\implies \text{Depth} = 2$.
     - $I_4$ starts (+1) $\implies \text{Depth} = 3$.
   - $t = 13$: $I_2$ ends (-1) $\implies \text{Depth} = 2$.
   - $t = 14$: $I_4$ ends (-1), $I_5$ ends (-1) $\implies \text{Depth} = 0$.
2. **Conclusion:**  
   Maximum depth is **$d = 4$** (at time $t = 11$).  
   **Minimum Classrooms Required = 4.**

---

### Practice Problem 4.3: Interval Scheduling with Machine Setup Times [5 Marks]
> **Question:**  
> Suppose between any two consecutive jobs on a single machine, a mandatory **setup/cleaning time of $\Delta$ units** is required. That is, job $j$ can follow job $i$ if and only if:
> $$s_j \ge e_i + \Delta$$
> How can we solve this problem using standard Earliest-End-Time Interval Scheduling? What is its time complexity?

#### Model Solution:
1. **Mathematical Reduction:**
   - The condition is $s_j \ge e_i + \Delta \iff s_j - \Delta \ge e_i$.
   - Equivalently, define modified intervals $I'_i = [s_i, e_i + \Delta]$.
   - Now, job $j$ is compatible with job $i$ if and only if $s_j \ge e'_i = e_i + \Delta$, which is the standard non-overlapping interval condition!
2. **Algorithm:**
   - Transform every interval $[s_i, e_i]$ into $[s_i, e_i + \Delta]$.
   - Sort intervals by their modified end time $e'_i = e_i + \Delta$ (which is identical to sorting by $e_i$, since $\Delta$ is a constant shift!).
   - Greedily pick the interval with earliest finish time that satisfies $s_{\text{next}} \ge e_{\text{prev}} + \Delta$.
3. **Correctness & Complexity:**  
   Inherits the exact optimality of the Greedy Stays Ahead theorem.  
   Running time: **$O(n \log n)$** (sorting dominant).

---

### Practice Problem 4.4: Maximum Weight Independent Set on Intervals (Weighted DP) [5 Marks]
> **Question:**  
> Given 4 jobs:
> - $J_1: [1, 4]$, Value $= 50$
> - $J_2: [3, 5]$, Value $= 20$
> - $J_3: [4, 7]$, Value $= 70$
> - $J_4: [6, 9]$, Value $= 60$
> 
> Compute the maximum profit achievable using Weighted Interval Scheduling DP. Show predecessor array $p$ and DP table values.

#### Model Solution:
1. **Jobs Sorted by End Time:**
   - $J_1: [1, 4], v_1 = 50$
   - $J_2: [3, 5], v_2 = 20$
   - $J_3: [4, 7], v_3 = 70$
   - $J_4: [6, 9], v_4 = 60$
2. **Compute Predecessor $p(i)$ (Latest non-conflicting job with $e_j \le s_i$):**
   - For $J_1$: $p(1) = 0$ (none).
   - For $J_2$: $s_2 = 3$. No job ends $\le 3 \implies p(2) = 0$.
   - For $J_3$: $s_3 = 4$. $J_1$ ends at $4 \le 4 \implies p(3) = 1$.
   - For $J_4$: $s_4 = 6$. $J_2$ ends at $5 \le 6 \implies p(4) = 2$.
3. **Fill DP Table ($DP[i] = \max(DP[i-1], v_i + DP[p(i)])$):**
   - $DP[0] = 0$
   - $DP[1] = \max(DP[0], v_1 + DP[0]) = \max(0, 50 + 0) = \mathbf{50}$
   - $DP[2] = \max(DP[1], v_2 + DP[0]) = \max(50, 20 + 0) = \mathbf{50}$
   - $DP[3] = \max(DP[2], v_3 + DP[1]) = \max(50, 70 + 50) = \mathbf{120}$
   - $DP[4] = \max(DP[3], v_4 + DP[2]) = \max(120, 60 + 50) = \mathbf{120}$
4. **Reconstruction:**
   - Optimal value = **120**.
   - Jobs selected: **$J_1$ and $J_3$** (Total profit $= 50 + 70 = 120$).

---

### Practice Problem 4.5: Proof of Interval Partitioning Lower Bound [4 Marks]
> **Question:**  
> Formally prove that for any set of intervals with depth $d$, no algorithm can schedule all intervals using strictly fewer than $d$ classrooms.

#### Model Solution:
- **Proof by Pigeonhole Principle:**
  - By definition of depth $d = \max_t |\{I_i \mid s_i \le t < e_i\}|$, there exists some specific timestamp $t^*$ at which there are **$d$ distinct intervals** running simultaneously:
    $$S = \{I_{j_1}, I_{j_2}, \dots, I_{j_d}\} \quad \text{such that for every } I \in S, \quad s_I \le t^* < e_I$$
  - Every pair of intervals in $S$ mutually overlaps at time $t^*$.
  - Therefore, no two intervals in $S$ can be assigned to the same classroom without creating a clash at time $t^*$.
  - By the Pigeonhole Principle, assigning $d$ mutually conflicting intervals requires at least $d$ distinct containers (classrooms).
  - Hence, $\text{Classrooms} \ge d$. $\blacksquare$

---

# Topic 5: Dynamic Programming — Coin Change Problem

---

### Practice Problem 5.1: Non-Canonical Coin System Counterexample [3 Marks]
> **Question:**  
> Prove that the coin denomination set $C = \{1, 5, 7\}$ is **not canonical** by finding the smallest target amount $A$ where the greedy algorithm fails to find the minimum number of coins.

#### Model Solution:
- **Smallest Counterexample: $A = 10$**
  - **Greedy Strategy:**
    - Largest coin $\le 10$ is **$7$**. Remainder: $10 - 7 = 3$.
    - Next largest coin $\le 3$ is **$1$**. Takes three $1$-coins.
    - **Greedy Result:** $\{7, 1, 1, 1\} \implies \mathbf{4 \text{ coins}}$.
  - **Optimal Strategy:**
    - Take two $5$-coins: $5 + 5 = 10 \implies \mathbf{2 \text{ coins}}$!
- **Conclusion:** Since greedy uses 4 coins while optimal uses 2 coins, $\{1, 5, 7\}$ is **non-canonical**.

---

### Practice Problem 5.2: Bounded Coin Change with Exact Counts [5 Marks]
> **Question:**  
> You are given coin denominations $\{2, 5, 10\}$ where:
> - Coin $2$ is available in quantity **3**
> - Coin $5$ is available in quantity **2**
> - Coin $10$ is available in quantity **1**
> 
> Can you make amount $A = 17$? If yes, what is the minimum number of coins? Trace the DP states.

#### Model Solution:
1. **Available Coins List:**
   - $2$-coins: $\{2, 2, 2\}$ (Total sum from $2$s = 6)
   - $5$-coins: $\{5, 5\}$ (Total sum from $5$s = 10)
   - $10$-coins: $\{10\}$ (Total sum from $10$s = 10)
   - Total money available $= 6 + 10 + 10 = 26 \ge 17$.
2. **Analysis for $A = 17$:**
   - To make an odd sum ($17$), we MUST use an odd number of odd coins ($5$).
   - The only odd denomination is $5$. We can take either **one $5$** or **three $5$s** (but only two are available, so we must take exactly one $5$).
   - If we use one $5$:
     $$\text{Remaining amount} = 17 - 5 = 12$$
   - Can we make $12$ using $\{2, 2, 2\}$ and $\{10\}$?
     - Case A: Use one $10 \implies$ remaining is $12 - 10 = 2$. Use one $2$.  
       Coins used: **one 10, one 5, one 2** ($10 + 5 + 2 = 17$).  
       Total coins $= 1 + 1 + 1 = \mathbf{3 \text{ coins}}$.
     - Case B: Use zero $10$s $\implies$ need 12 from $2$s, but max from $2$s is $3 \times 2 = 6 < 12$ (impossible).
3. **Verdict:** **YES**, amount 17 can be formed using **minimum 3 coins** ($\{10, 5, 2\}$).

---

### Practice Problem 5.3: Counting Distinct Coin Change Representations Modulo $10^9+7$ [5 Marks]
> **Question:**  
> Write clean C++ code to compute the number of **distinct combinations** to make amount $A$ using denominations $C = \{c_1, \dots, c_n\}$ (unlimited supply) modulo $10^9 + 7$. Explain why the space complexity is $O(A)$ rather than $O(n \cdot A)$.

#### Model Solution:
```cpp
#include <vector>

using namespace std;

int change(int amount, vector<int>& coins) {
    const int MOD = 1e9 + 7;
    // 1D DP array of size amount + 1
    vector<int> dp(amount + 1, 0);
    dp[0] = 1; // Base case: 1 way to make amount 0 (pick nothing)

    // Outer loop over coins ensures each denomination is considered in order
    // preventing permutation duplicates like {1, 2} and {2, 1}
    for (int coin : coins) {
        for (int a = coin; a <= amount; ++a) {
            dp[a] = (dp[a] + dp[a - coin]) % MOD;
        }
    }

    return dp[amount];
}
```
- **Why Space is $O(A)$:**  
  The 2D state $dp[i][a]$ (using first $i$ coins) only depends on the current coin $i$ at a smaller amount $dp[i][a - c_i]$ and the previous coin at the same amount $dp[i-1][a]$. By updating the array in place, `dp[a]` naturally accumulates the sum over coin types without needing an item dimension, reducing space from $O(n \cdot A)$ to **$O(A)$**.

---

### Practice Problem 5.4: Unary Encoding Complexity Experiment [4 Marks]
> **Question:**  
> Explain the theoretical consequence of encoding the input amount $A$ in **unary notation** (e.g., $A=5$ represented as `11111`) versus standard **binary notation** (`101`). Under which encoding is Coin Change formally a polynomial-time algorithm?

#### Model Solution:
- **Binary Encoding:**
  - Length of input: $L = \lceil \log_2(A + 1) \rceil$ bits.
  - Value of amount: $A \approx 2^L$.
  - Complexity: $O(n \cdot A) = O(n \cdot 2^L)$. Exponential in input size $L$ $\implies$ **Pseudo-polynomial**.
- **Unary Encoding:**
  - Length of input: $L_{\text{unary}} = A$ bits (literally $A$ characters).
  - Complexity in terms of input length: $O(n \cdot L_{\text{unary}})$.
  - This is a linear (polynomial) function of the input length!
- **Conclusion:**  
  Coin change is formally a **polynomial-time algorithm ONLY under unary encoding**.

---

### Practice Problem 5.5: Minimum Coins with Lexicographically Smallest Reconstruction [4 Marks]
> **Question:**  
> If an amount $A$ can be formed by multiple combinations using the minimum number of coins, explain how to reconstruct the solution that uses the **largest coin values first** (lexicographically largest coin denominations).

#### Model Solution:
1. First, sort the coin array in **descending order**: $c_1 > c_2 > \dots > c_n$.
2. Compute the standard minimum coins table $DP[0..A]$.
3. For reconstruction from amount $A$ down to $0$:
   - At current amount `curr`, iterate through sorted coins from largest to smallest ($c_1, c_2, \dots$):
     - If `curr >= c` AND $DP[\text{curr} - c] == DP[\text{curr}] - 1$:
       - Greedily pick coin $c$!
       - Update $\text{curr} \leftarrow \text{curr} - c$, and break to the next step.
4. Because the coins were scanned from largest to smallest, the first valid predecessor choice encountered is mathematically guaranteed to belong to an optimal path with the largest possible coin.

---

# Topic 6: Dynamic Programming — LCS & Edit Distance

---

### Practice Problem 6.1: Full Alignment Trace & Distance Calculation [4 Marks]
> **Question:**  
> Using the standard Levenshtein operations (Insertion, Deletion, Substitution), evaluate:
> $$\text{dist}(\text{"kitten"}, \text{"sitting"})$$
> Show the two-row alignment diagram, the step-by-step word transitions, and the operation counts.

#### Model Solution:
1. **Alignment Representation:**
   ```
   k   i   t   t   e   n   _
   s   i   t   t   i   n   g
   ```
2. **Step-by-Step Word Evolution:**
   - `kitten`
   - $\xrightarrow{\text{substitute } k \to s}$ `sitten`
   - $\xrightarrow{\text{substitute } e \to i}$ `sittin`
   - $\xrightarrow{\text{insert } g}$ `sitting`
3. **Operations Breakdown:**
   - 2 substitutions ($k \to s, e \to i$)
   - 1 insertion ($g$)
   - 0 deletions
   - Total operations $= 2 + 1 = \mathbf{3}$.
4. **Verdict:** $\mathbf{\text{dist}(\text{"kitten"}, \text{"sitting"}) = 3}$.

---

### Practice Problem 6.2: Hirschberg's Algorithm Divide-and-Conquer Recurrence [5 Marks]
> **Question:**  
> Explain how Hirschberg’s algorithm combines Divide-and-Conquer with Dynamic Programming to reconstruct the LCS string in **$O(m + n)$ space** while maintaining **$O(m \cdot n)$ time**. What is the recurrence for the middle split point?

#### Model Solution:
1. **The Core Dilemma:**  
   Standard DP requires an $m \times n$ matrix to backtrack pointers, taking $O(m \cdot n)$ memory. A rolling 2-row DP takes $O(n)$ memory for length, but cannot reconstruct the string.
2. **Hirschberg's Strategy:**
   - Split string $X$ exactly in half at $mid = \lfloor m/2 \rfloor$.
   - The optimal LCS string must cross the partition line $X[mid]$ at some character index $k \in [0, n]$ in string $Y$.
3. **Finding the Optimal Split Point $k$:**
   - Compute forward DP of $X[1..mid]$ against $Y[1..n] \implies L_1[0..n]$ in $O(n)$ space.
   - Compute backward DP of reversed $X[mid+1..m]$ against reversed $Y[1..n] \implies L_2[0..n]$ in $O(n)$ space.
   - The optimal split index $k^*$ maximizes the combined length:
     $$k^* = \arg\max_{0 \le k \le n} \big( L_1[k] + L_2[n - k] \big)$$
4. **Recursion:**
   - Recursively solve on left half: $X[1..mid]$ and $Y[1..k^*]$.
   - Recursively solve on right half: $X[mid+1..m]$ and $Y[k^*+1..n]$.
5. **Runtime Complexity:**
   $$T(m, n) = T(m/2, k^*) + T(m/2, n - k^*) + O(m \cdot n) = \mathbf{O(m \cdot n)}$$
   Space is bounded by the recursive call stack: $\mathbf{O(m + n)}$.

---

### Practice Problem 6.3: Longest Palindromic Subsequence Reduction [4 Marks]
> **Question:**  
> Prove that the Longest Palindromic Subsequence (LPS) of a string $S$ is mathematically equivalent to the Longest Common Subsequence between $S$ and its reverse $S^R$:
> $$\text{LPS}(S) = \text{LCS}(S, S^R)$$

#### Model Solution:
- **Proof:**
  1. Let $P$ be any palindromic subsequence of $S$.  
     By definition of a palindrome, $P = P^R$.  
     Since $P$ is a subsequence of $S$, $P^R$ is a subsequence of $S^R$.  
     Because $P = P^R$, $P$ is simultaneously a subsequence of both $S$ and $S^R$.  
     Therefore, $P$ is a common subsequence of $S$ and $S^R$.  
     Hence, $|\text{LPS}(S)| \le |\text{LCS}(S, S^R)|$.
  2. Conversely, let $C$ be the longest common subsequence of $S$ and $S^R$.  
     Since $C$ is a subsequence of $S$, $C^R$ is a subsequence of $S^R$.  
     By symmetry of common subsequences, there exists a maximal common subsequence that is identical to its own reverse, forming a valid palindrome in $S$.  
     Hence, $|\text{LCS}(S, S^R)| \le |\text{LPS}(S)|$.
  3. Combining both inequalities: $|\text{LPS}(S)| = |\text{LCS}(S, S^R)|$. $\blacksquare$

---

### Practice Problem 6.4: Shortest Common Supersequence Length Formula [3 Marks]
> **Question:**  
> Let $X$ and $Y$ be two strings of lengths $m$ and $n$, respectively. Prove that the length of their Shortest Common Supersequence (SCS) is:
> $$|\text{SCS}(X, Y)| = m + n - |\text{LCS}(X, Y)|$$

#### Model Solution:
- **Proof:**
  - Any common supersequence $S$ must contain all characters of $X$ and all characters of $Y$.
  - Characters that appear in both $X$ and $Y$ in the same relative order (i.e., characters in the Longest Common Subsequence) only need to be written **once** in $S$.
  - Characters unique to $X$ (count: $m - |\text{LCS}|$) must be written once.
  - Characters unique to $Y$ (count: $n - |\text{LCS}|$) must be written once.
  - Summing the characters:
    $$|\text{SCS}| = |\text{LCS}| + (m - |\text{LCS}|) + (n - |\text{LCS}|) = m + n - |\text{LCS}| \quad \blacksquare$$

---

### Practice Problem 6.5: LCS with Strictly Distinct Characters [4 Marks]
> **Question:**  
> Suppose string $X$ contains **no duplicate characters** (all characters are pairwise distinct).  
> Explain how to find $\text{LCS}(X, Y)$ in **$O(n \log n)$ time**, breaking the standard $O(m \cdot n)$ barrier.

#### Model Solution:
1. **Reduction to Longest Increasing Subsequence (LIS):**
   - Since all characters in $X$ are distinct, each character $c \in X$ has a **unique index** $\text{pos}(c)$ in $X$.
   - Precompute a hash map / lookup array storing $\text{pos}(c)$ for all characters in $X$ in $O(m)$ time.
   - Now iterate through string $Y$:
     - For each character $Y[j]$, if $Y[j] \in X$, replace $Y[j]$ with its index $\text{pos}(Y[j])$ in $X$.
     - Discard characters not present in $X$.
   - This produces an integer array $A$.
2. **Solving via Patience Sorting:**
   - Any common subsequence between $X$ and $Y$ corresponds to an **increasing subsequence** in $A$!
   - Find the LIS of array $A$ using binary search (Patience Sorting) in **$O(|A| \log |A|) = O(n \log n)$** time.
3. **Total Runtime:** $O(m + n \log n) = \mathbf{O(n \log n)}$.

---

# Topic 7: Dynamic Programming — Knapsack & Multiple Knapsacks

---

### Practice Problem 7.1: Identical Value-to-Weight Ratios Trap [4 Marks]
> **Question:**  
> Suppose all $n$ items in a 0/1 Knapsack instance have the **exact same value-to-weight ratio**:
> $$\frac{v_1}{w_1} = \frac{v_2}{w_2} = \dots = \frac{v_n}{w_n} = r$$
> Does greedily choosing items with the smallest weight guarantee an optimal solution? If not, provide a counterexample.

#### Model Solution:
- **Verdict:** **NO! Greedily choosing smallest weight FAILS.**
- **Proof & Counterexample:**
  - Let ratio $r = 1$ (so $v_i = w_i$ for all $i$).
  - Knapsack Capacity $W = 10$.
  - Items:
    - Item 1: Weight $= 4$, Value $= 4$
    - Item 2: Weight $= 4$, Value $= 4$
    - Item 3: Weight $= 10$, Value $= 10$
  - **Greedy by Smallest Weight:**
    - Picks Item 1 (wt 4, val 4). Remaining capacity $= 6$.
    - Picks Item 2 (wt 4, val 4). Remaining capacity $= 2$.
    - Item 3 (wt 10) cannot fit.
    - **Greedy Total Value:** $4 + 4 = \mathbf{8}$.
  - **Optimal Choice:**
    - Pick Item 3 alone: Weight $= 10 \le 10$, Value $= \mathbf{10}$!
  - Optimal value ($10$) strictly beats Greedy value ($8$).
- **Theoretical Reason:** When $v_i = w_i$, 0/1 Knapsack reduces to the **Subset Sum Problem**, which is NP-complete. No simple greedy strategy can solve it optimally.

---

### Practice Problem 7.2: Two Knapsacks with Unequal Capacities [5 Marks]
> **Question:**  
> Two containers have capacities $C_1 = 8$ and $C_2 = 10$. There are 5 items:
> - Item 1: Weight 6, Value 15
> - Item 2: Weight 5, Value 12
> - Item 3: Weight 4, Value 10
> - Item 4: Weight 4, Value 10
> - Item 5: Weight 3, Value 7
> 
> What is the maximum total value that can be packed across both containers? Show the optimal item assignment.

#### Model Solution:
1. **Total Parameters:**
   - Total weight $= 6 + 5 + 4 + 4 + 3 = 22$.
   - Total capacity $= 8 + 10 = 18$.
   - Must leave behind items weighing at least $22 - 18 = 4$.
2. **Minimizing Sacrificed Value:**
   - Candidate items with weight $\ge 4$:
     - Item 1 (wt 6, val 15)
     - Item 2 (wt 5, val 12)
     - Item 3 (wt 4, val 10) $\implies$ leaves value 10!
     - Item 4 (wt 4, val 10) $\implies$ leaves value 10!
   - Sacrificing Item 3 (weight 4, value 10) leaves remaining weight $22 - 4 = 18 \le 18$.
   - Total value of remaining 4 items $= 15 + 12 + 10 + 7 = \mathbf{44}$.
3. **Fitting into Capacities $C_1 = 8$ and $C_2 = 10$:**
   - **Container 1 ($C_1 = 8$):** Assign Item 2 (5) + Item 5 (3) $\implies$ Weight $= 5 + 3 = 8 \le 8$, Value $= 12 + 7 = 19$.
   - **Container 2 ($C_2 = 10$):** Assign Item 1 (6) + Item 4 (4) $\implies$ Weight $= 6 + 4 = 10 \le 10$, Value $= 15 + 10 = 25$.
   - Total weight packed $= 8 + 10 = 18$.
4. **Final Result:**
   $$\mathbf{\text{Maximum Stolen Value} = 19 + 25 = 44}$$

---

### Practice Problem 7.3: Dual DP for Enormous Capacity ($W = 10^9$) [5 Marks]
> **Question:**  
> Suppose $n = 100$, capacity $W = 10^9$, item weights $w_i \le 10^9$, but item values are small: $v_i \le 100$ for all $i$.  
> 1. Why does standard DP fail?  
> 2. Formulate the Dual DP recurrence and state its time and space complexity.

#### Model Solution:
1. **Why Standard DP Fails:**  
   Standard DP requires a table of size $n \times W = 100 \times 10^9 = 10^{11}$ entries, which exceeds both memory ($\sim 400$ GB) and compute time limits (takes minutes/hours).
2. **Dual DP Formulation:**  
   Invert the state: let **Value be the state**, and compute the **minimum weight** needed to achieve that value!
   - Maximum possible value $V_{\max} = n \times \max(v_i) = 100 \times 100 = 10,000$.
   - State: $DP[i][v]$ = minimum weight of a subset from the first $i$ items that achieves total value *exactly* $v$.
   - Base cases: $DP[0][0] = 0$, and $DP[0][v] = \infty$ for all $v > 0$.
   - Recurrence:
     $$DP[i][v] = \min\big( DP[i-1][v], \quad DP[i-1][v - v_i] + w_i \big)$$
   - Final Answer:
     $$\text{Max Value} = \max \{ v \mid DP[n][v] \le W \}$$
3. **Complexity:**
   - Time Complexity: $O(n \cdot V_{\max}) = 100 \times 10,000 = \mathbf{10^6 \text{ operations}}$ (Runs in $< 5$ milliseconds!).
   - Space Complexity: $O(V_{\max})$ using a 1D reverse array.

---

### Practice Problem 7.4: Forward Traversal Degeneration in 1D Knapsack [4 Marks]
> **Question:**  
> In 0/1 Knapsack, explain mathematically why traversing the capacity loop **forwards** ($w = w_i \to W$) solves the **Unbounded Knapsack problem**, and why traversing **backwards** ($w = W \to w_i$) is mandatory for 0/1 Knapsack.

#### Model Solution:
- **Forwards Traversal ($w = w_i \to W$):**
  - When computing cell `dp[w]`, the transition uses `dp[w - w_i]`.
  - Since `w - w_i < w`, `dp[w - w_i]` has **already been updated in the current outer iteration** (using item $i$).
  - Therefore, adding $v_i$ can incorporate item $i$ a second, third, or fourth time!
  - This matches the definition of **Unbounded Knapsack** (unlimited copies of each item).
- **Backwards Traversal ($w = W \to w_i$):**
  - When computing cell `dp[w]`, the value `dp[w - w_i]` has **NOT yet been updated in the current iteration**; it still holds the value from iteration $i-1$.
  - This ensures that item $i$ is added to a subset that contains **zero copies of item $i$**, strictly enforcing the $0/1$ constraint!

---

### Practice Problem 7.5: Fractional Knapsack with Budget Constraint [4 Marks]
> **Question:**  
> In Fractional Knapsack, prove using an exchange argument that sorting items by ratio $r_i = v_i / w_i$ in descending order is globally optimal.

#### Model Solution:
- **Proof by Exchange Argument:**
  - Let $G$ be the greedy solution, and suppose an optimal solution $O$ differs from $G$.
  - Let $i$ be the first item where $G$ takes more fraction of the item than $O$ does ($x_i^G > x_i^O$).
  - Since $O$ packs capacity $W$, $O$ must have packed some fraction of an item $j$ where $x_j^O > x_j^G$.
  - Because greedy processes items by descending ratio, we know $r_i \ge r_j$.
  - Replace an amount $\epsilon$ of weight of item $j$ in $O$ with an equal weight $\epsilon$ of item $i$.
  - The new total value changes by:
    $$\Delta V = \epsilon \cdot r_i - \epsilon \cdot r_j = \epsilon (r_i - r_j) \ge 0$$
  - The new solution remains feasible (weight unchanged) and its value is at least as good as $O$.
  - Repeating this exchange transforms $O$ into $G$ without ever decreasing total value. Hence, $G$ is optimal. $\blacksquare$

---

# Topic 8: Divide-and-Conquer — Integer Multiplication

---

### Practice Problem 8.1: Step-by-Step Karatsuba Multiplication of Two 4-Digit Numbers [5 Marks]
> **Question:**  
> Multiply $X = 2345$ and $Y = 6789$ using Karatsuba’s algorithm.  
> Clearly state $X_1, X_0, Y_1, Y_0$, the 3 recursive products $z_2, z_0, z_1$, the middle term calculation, and the final shifted sum.

#### Model Solution:
1. **Split into $n/2 = 2$ digit halves:**
   - $X_1 = 23, \quad X_0 = 45$
   - $Y_1 = 67, \quad Y_0 = 89$
2. **Compute the 3 Products:**
   - $z_2 = X_1 \times Y_1 = 23 \times 67 = \mathbf{1541}$
   - $z_0 = X_0 \times Y_0 = 45 \times 89 = \mathbf{4005}$
   - Compute sum terms:
     - $X_1 + X_0 = 23 + 45 = 68$
     - $Y_1 + Y_0 = 67 + 89 = 156$
     - $z_1 = 68 \times 156 = \mathbf{10608}$
3. **Compute Middle Term:**
   $$\text{Middle} = z_1 - z_2 - z_0 = 10608 - 1541 - 4005 = \mathbf{5062}$$
4. **Reconstruct Final Value:**
   $$\begin{aligned}
   X \times Y &= z_2 \times 10^4 + \text{Middle} \times 10^2 + z_0 \\
   &= 1541 \times 10000 + 5062 \times 100 + 4005 \\
   &= 15,410,000 + 506,200 + 4,005 \\
   &= \mathbf{15,920,205}
   \end{aligned}$$
   *(Verification: $2345 \times 6789 = 15,920,205$. Exactly correct!)*

---

### Practice Problem 8.2: Solving Recurrences with Unequal Subproblems [4 Marks]
> **Question:**  
> Suppose an engineer invents a divide-and-conquer integer multiplication algorithm that splits an $n$-bit number into 3 pieces of size $n/3$, and performs **6 recursive multiplications** of size $n/3$, with linear recombination time $O(n)$.  
> 1. Write the recurrence relation.  
> 2. Solve for its asymptotic complexity using the Master Theorem.  
> 3. Is this algorithm faster or slower than Karatsuba's algorithm?

#### Model Solution:
1. **Recurrence Relation:**
   $$T(n) = 6 T(n/3) + O(n)$$
2. **Solving by Master Theorem:**
   - $a = 6$, $b = 3$, $f(n) = O(n^1)$.
   - Critical exponent:
     $$\log_b a = \log_3 6 = \frac{\ln 6}{\ln 3} \approx \frac{1.79176}{1.09861} \approx \mathbf{1.6309}$$
   - Since $f(n) = O(n^1) = O(n^{\log_3 6 - \epsilon})$ with $\epsilon \approx 0.6309 > 0$, Case 1 applies:
     $$T(n) = \mathbf{\Theta(n^{\log_3 6}) \approx \Theta(n^{1.631})}$$
3. **Comparison with Karatsuba:**
   - Karatsuba's exponent: $\log_2 3 \approx \mathbf{1.585}$.
   - Proposed algorithm's exponent: $\log_3 6 \approx \mathbf{1.631}$.
   - Since $1.631 > 1.585$, the proposed algorithm is **strictly SLOWER than Karatsuba**.

---

### Practice Problem 8.3: Toom-Cook 3-Way Splitting (Toom-3) [5 Marks]
> **Question:**  
> The Toom-Cook algorithm (Toom-3) splits numbers into 3 parts ($X_2, X_1, X_0$) and reduces 9 multiplications to **5 multiplications** of size $n/3$.  
> 1. State its recurrence relation.  
> 2. What is its asymptotic running time?  
> 3. Why is Toom-3 not always used instead of Karatsuba in practice?

#### Model Solution:
1. **Recurrence Relation:**
   $$T(n) = 5 T(n/3) + O(n)$$
2. **Asymptotic Running Time:**
   - Critical exponent:
     $$\log_3 5 = \frac{\ln 5}{\ln 3} \approx \frac{1.6094}{1.0986} \approx \mathbf{1.46497}$$
   - Time Complexity: $\mathbf{\Theta(n^{\log_3 5}) \approx \Theta(n^{1.465})}$.
3. **Practical Considerations:**  
   Toom-Cook requires evaluating polynomials at 5 points ($0, 1, -1, 2, \infty$) and performing matrix inversion during interpolation. This introduces large constant overhead ($O(n)$ additions and division by constants). Thus, Toom-Cook is only faster than Karatsuba for numbers with several thousand digits (typically $> 3000$ bits).

---

### Practice Problem 8.4: Bit-Complexity of Elementary Operations [3 Marks]
> **Question:**  
> Prove that adding two $n$-bit integers takes $\Theta(n)$ time, while multiplying an $n$-bit integer by an $m$-bit integer using the schoolbook algorithm takes $\Theta(n \cdot m)$ time.

#### Model Solution:
- **Addition:** Adding two $n$-bit numbers requires processing bits from index $0$ to $n-1$, computing the sum and carry at each position in $O(1)$ operations. The carry propagates at most $n$ times. Total bit operations: $n \times O(1) = \mathbf{\Theta(n)}$.
- **Multiplication:** Schoolbook multiplication multiplies each of the $m$ bits of the second operand across all $n$ bits of the first operand, creating $m$ partial products of size up to $n+1$. Generating partial products takes $m \times O(n) = O(n \cdot m)$. Adding the $m$ shifted rows takes $\sum_{i=1}^m O(n) = O(n \cdot m)$. Total bit operations: $\mathbf{\Theta(n \cdot m)}$.

---

### Practice Problem 8.5: The Theoretical Horizon of Integer Multiplication [3 Marks]
> **Question:**  
> In 2019, David Harvey and Joris van der Hoeven established the milestone $O(n \log n)$ integer multiplication algorithm. Explain why $\Omega(n \log n)$ was historically conjectured as the ultimate lower bound, and whether an $o(n \log n)$ algorithm is theoretically possible.

#### Model Solution:
- Writing down the product requires at least reading the $n$ input bits and writing the $2n$ output bits ($\Omega(n)$).
- However, since multiplication is fundamentally equivalent to convolution (and polynomial multiplication), the Fast Fourier Transform (FFT) bottleneck is $\Omega(n \log n)$ under standard algebraic models.
- Harvey and van der Hoeven's $O(n \log n)$ bound matched this long-standing conjecture. Any algorithm strictly faster than $O(n \log n)$ (i.e., $o(n \log n)$) is widely believed to be impossible under standard Turing machine models.

---

# Topic 9: Divide-and-Conquer — Matrix Multiplication & MCM

---

### Practice Problem 9.1: Strassen's Product Verification [4 Marks]
> **Question:**  
> In Strassen’s algorithm, given the product formulas:
> - $m_2 = (a_{21} + a_{22}) b_{11}$
> - $m_4 = a_{22} (b_{21} - b_{11})$
> 
> Prove algebraically that $c_{21} = m_2 + m_4$.

#### Model Solution:
- **Proof:**
  $$\begin{aligned}
  m_2 &= (a_{21} + a_{22}) b_{11} = a_{21} b_{11} + a_{22} b_{11} \\
  m_4 &= a_{22} (b_{21} - b_{11}) = a_{22} b_{21} - a_{22} b_{11}
  \end{aligned}$$
  Adding both expressions:
  $$\begin{aligned}
  m_2 + m_4 &= (a_{21} b_{11} + a_{22} b_{11}) + (a_{22} b_{21} - a_{22} b_{11}) \\
  &= a_{21} b_{11} + (a_{22} b_{11} - a_{22} b_{11}) + a_{22} b_{21} \\
  &= a_{21} b_{11} + a_{22} b_{21}
  \end{aligned}$$
  By standard matrix multiplication, the entry $c_{21}$ of matrix $C = A \times B$ is indeed $a_{21} b_{11} + a_{22} b_{21}$.  
  Thus, $c_{21} = m_2 + m_4 \equiv \mathbf{c_{21}}$. $\blacksquare$

---

### Practice Problem 9.2: Exact Operations Count for $4 \times 4$ Matrix via Strassen [4 Marks]
> **Question:**  
> Using the closed-form arithmetic operation count formula for Strassen’s algorithm:
> $$\text{Total Ops}(n) = 7 n^{\log_2 7} - 6 n^2$$
> Compute the **exact number of arithmetic operations** required to multiply two $4 \times 4$ matrices ($n = 4$). Compare this to the standard schoolbook algorithm.

#### Model Solution:
1. **Strassen's Exact Operations for $n = 4$:**
   - Here $n = 4 = 2^2$ (recursion depth $k = 2$).
   - $4^{\log_2 7} = (2^2)^{\log_2 7} = (2^{\log_2 7})^2 = 7^2 = \mathbf{49}$.
   - $n^2 = 4^2 = \mathbf{16}$.
   - Substitute into formula:
     $$\text{Total Ops}(4) = 7(49) - 6(16) = 343 - 96 = \mathbf{247 \text{ arithmetic operations}}$$
     *(Breakdown: $7^2 = 49$ multiplications $+ 198$ additions/subtractions $= 247$).*
2. **Standard Algorithm for $n = 4$:**
   - Multiplications: $n^3 = 4^3 = 64$.
   - Additions: $n^2(n - 1) = 16 \times 3 = 48$.
   - Total operations $= 64 + 48 = \mathbf{112 \text{ operations}}$.
3. **Crucial Observation:** For small $n = 4$, Strassen performs **more total operations** (247 vs 112) due to addition overhead. Strassen only beats standard multiplication when $n \ge 64$ to $128$!

---

### Practice Problem 9.3: AlphaTensor for General Fields [3 Marks]
> **Question:**  
> Google DeepMind's AlphaTensor achieved $T(n) = 47 T(n/4)$ for $4 \times 4$ matrices over the binary field $\mathbb{Z}_2$. If an algorithm computes $4 \times 4$ matrix multiplication over real numbers $\mathbb{R}$ using **48 multiplications**, what is its asymptotic complexity? Would it beat Strassen?

#### Model Solution:
1. **Recurrence:** $T(n) = 48 T(n/4) + O(n^2)$.
2. **Critical Exponent:**
   $$\log_4 48 = \frac{\ln 48}{\ln 4} \approx \frac{3.8712}{1.3863} \approx \mathbf{2.7924}$$
3. **Comparison:**
   - Strassen's exponent: $\log_2 7 \approx \mathbf{2.8074}$.
   - Since $2.7924 < 2.8074$, an algorithm using 48 multiplications for $4 \times 4$ matrices would **strictly BEAT Strassen's algorithm**!

---

### Practice Problem 9.4: Matrix Chain Multiplication (MCM) Full DP Table [5 Marks]
> **Question:**  
> Find the optimal parenthesization and minimum scalar multiplications for 4 matrices with dimension sequence:
> $$p = \langle 10, 20, 30, 5, 60 \rangle$$
> Dimensions: $A_1 (10 \times 20)$, $A_2 (20 \times 30)$, $A_3 (30 \times 5)$, $A_4 (5 \times 60)$.

#### Model Solution:
1. **Chain Length $L = 2$ (Subproblems of length 2):**
   - $m[1, 2] = p_0 p_1 p_2 = 10 \times 20 \times 30 = \mathbf{6,000}$
   - $m[2, 3] = p_1 p_2 p_3 = 20 \times 30 \times 5 = \mathbf{3,000}$
   - $m[3, 4] = p_2 p_3 p_4 = 30 \times 5 \times 60 = \mathbf{9,000}$
2. **Chain Length $L = 3$ (Subproblems of length 3):**
   - **For $m[1, 3]$ (options $k=1, k=2$):**
     - $k = 1: m[1, 1] + m[2, 3] + p_0 p_1 p_3 = 0 + 3000 + 10 \times 20 \times 5 = 3000 + 1000 = 4000$
     - $k = 2: m[1, 2] + m[3, 3] + p_0 p_2 p_3 = 6000 + 0 + 10 \times 30 \times 5 = 6000 + 1500 = 7500$
     - $\implies m[1, 3] = \mathbf{4,000}$ (split at $k = 1$).
   - **For $m[2, 4]$ (options $k=2, k=3$):**
     - $k = 2: m[2, 2] + m[3, 4] + p_1 p_2 p_4 = 0 + 9000 + 20 \times 30 \times 60 = 9000 + 36000 = 45000$
     - $k = 3: m[2, 3] + m[4, 4] + p_1 p_3 p_4 = 3000 + 0 + 20 \times 5 \times 60 = 3000 + 6000 = 9000$
     - $\implies m[2, 4] = \mathbf{9,000}$ (split at $k = 3$).
3. **Chain Length $L = 4$ (Full Chain $m[1, 4]$):**
   - $k = 1: m[1, 1] + m[2, 4] + p_0 p_1 p_4 = 0 + 9000 + 10 \times 20 \times 60 = 9000 + 12000 = \mathbf{21,000}$
   - $k = 2: m[1, 2] + m[3, 4] + p_0 p_2 p_4 = 6000 + 9000 + 10 \times 30 \times 60 = 15000 + 18000 = 33000$
   - $k = 3: m[1, 3] + m[4, 4] + p_0 p_3 p_4 = 4000 + 0 + 10 \times 5 \times 60 = 4000 + 3000 = \mathbf{7,000}$!
   - $\implies m[1, 4] = \mathbf{7,000}$ (split at $k = 3$).
4. **Optimal Parenthesization:**
   - Split at $k = 3 \implies (A_1 A_2 A_3) \times A_4$.
   - $m[1, 3]$ split at $k = 1 \implies A_1 \times (A_2 A_3)$.
   - **Final Optimal Formula:** $\mathbf{((A_1 \times (A_2 \times A_3)) \times A_4)}$
   - **Minimum Scalar Multiplications:** $\mathbf{7,000}$.

---

### Practice Problem 9.5: Proof of Catalan Recurrence for MCM [4 Marks]
> **Question:**  
> Let $P(n)$ be the number of distinct parenthesizations of a chain of $n$ matrices. Prove that $P(n)$ satisfies the Catalan recurrence:
> $$P(n) = \sum_{k=1}^{n-1} P(k) P(n - k), \quad P(1) = 1$$
> What is the asymptotic growth of $P(n)$?

#### Model Solution:
- **Proof:**
  - In any parenthesization of $A_1 \times A_2 \times \dots \times A_n$, there is a **final outer multiplication** that multiplies the product of the prefix $A_1 \dots A_k$ by the suffix $A_{k+1} \dots A_n$.
  - The split point $k$ can be any integer from $1$ to $n-1$.
  - For a fixed $k$, the prefix of $k$ matrices can be parenthesized in $P(k)$ independent ways.
  - The suffix of $n - k$ matrices can be parenthesized in $P(n - k)$ independent ways.
  - By the multiplication rule of combinatorics, for a fixed $k$, there are $P(k) \times P(n - k)$ ways.
  - Summing over all mutually exclusive choices of $k \in \{1, \dots, n-1\}$:
    $$P(n) = \sum_{k=1}^{n-1} P(k) P(n - k), \quad P(1) = 1$$
- **Asymptotic Growth:**
  The solution is the $(n-1)$-th Catalan number:
  $$P(n) = C_{n-1} = \frac{1}{n}\binom{2n-2}{n-1} = \mathbf{\Theta\left(\frac{4^n}{n^{3/2}}\right)}$$
  This exponential explosion proves why brute-force search is impossible and Dynamic Programming is mandatory!

---

# Topic 10: Recurrences, Matrix Exponentiation & DSU

---

### Practice Problem 10.1: Quadranacci Amoeba Reproduction [5 Marks]
> **Question:**  
> Suppose a modified species of amoeba reproduces as follows:
> - Starts reproducing 1 hour after birth.
> - Gives birth to 1 new amoeba every hour for **4 hours**.
> - Dies immediately after giving birth to its 4th child.
> 
> Let $G(t)$ be the number of alive amoebas at time $t$.  
> 1. Write the recurrence relation for $G(t)$.  
> 2. What is the $4 \times 4$ companion matrix $M$ that advances the state vector?  
> 3. State the time complexity to compute $G(t)$ using binary matrix exponentiation.

#### Model Solution:
1. **Recurrence Relation:**
   Each amoeba gives birth at times $t-1, t-2, t-3, t-4$.
   $$G(t) = G(t-1) + G(t-2) + G(t-3) + G(t-4)$$
2. **Companion Matrix $M$ ($4 \times 4$):**
   $$\begin{pmatrix} G(t-2) \\ G(t-1) \\ G(t) \\ G(t+1) \end{pmatrix} = \begin{pmatrix} 0 & 1 & 0 & 0 \\ 0 & 0 & 1 & 0 \\ 0 & 0 & 0 & 1 \\ 1 & 1 & 1 & 1 \end{pmatrix} \begin{pmatrix} G(t-3) \\ G(t-2) \\ G(t-1) \\ G(t) \end{pmatrix}$$
3. **Time Complexity:**
   Computing $M^t$ using binary exponentiation requires $\lceil \log_2 t \rceil$ matrix multiplications of size $4 \times 4$.  
   Each multiplication takes $4^3 = 64$ operations.  
   Total runtime: $O(4^3 \log t) = \mathbf{O(\log t)}$.

---

### Practice Problem 10.2: Path Compression Alone vs Both DSU Optimizations [4 Marks]
> **Question:**  
> What is the worst-case time complexity of performing $m$ operations on $n$ elements in Disjoint Set Union (DSU) if:  
> (a) We use **Path Compression alone** (without Union by Rank)?  
> (b) We use **Union by Rank alone** (without Path Compression)?  
> (c) We use **BOTH** Path Compression and Union by Rank?

#### Model Solution:
- **(a) Path Compression alone:**  
  $$\mathbf{\Theta(m \log_{1 + m/n} n)} \quad (\text{or } O(m \log n))$$
  Without rank, adversarial unions can create a degenerate tree of depth $n$, and although path compression flattens paths, individual operations can still take $\Theta(n)$.
- **(b) Union by Rank alone:**  
  $$\mathbf{\Theta(m \log n)}$$
  Union by rank guarantees that the maximum tree depth is bounded by $\lfloor \log_2 n \rfloor$. Without path compression, each find traverses up to $O(\log n)$ ancestors.
- **(c) Both Optimizations Combined:**  
  $$\mathbf{\Theta(m \cdot \alpha(n))}$$
  where $\alpha(n)$ is the Inverse Ackermann function ($\alpha(n) \le 4$ for all practical inputs). Nearly $O(1)$ amortized per operation!

---

### Practice Problem 10.3: Checking Bipartiteness with DSU [4 Marks]
> **Question:**  
> How can DSU be used to detect whether an undirected graph contains an **odd cycle** (i.e., whether the graph is **bipartite**) in an online stream of edges?

#### Model Solution:
1. **Algorithm:**
   - Maintain a DSU with $2V$ elements: for each vertex $u$, create two virtual nodes: $u_{\text{color0}}$ and $u_{\text{color1}}$ (or represent parity using potential differences).
   - For each edge $(u, v)$:
     - Check: Is $\text{find}(u) == \text{find}(v)$?
       - If YES $\implies u$ and $v$ are already forced to share the **same color**! Adding edge $(u, v)$ creates an **odd cycle**. The graph is **NOT bipartite**!
     - Otherwise, connect opposite colors:
       - $\text{union}(u, v + V)$
       - $\text{union}(v, u + V)$
2. **Complexity:**  
   $O(E \cdot \alpha(V))$ online edge processing time.

---

### Practice Problem 10.4: Master Theorem Edge Cases ($T(n) = 2T(n/2) + n \log n$) [4 Marks]
> **Question:**  
> Solve the recurrence relation:
> $$T(n) = 2T(n/2) + n \log n$$
> Does standard Master Theorem Case 2 apply directly? Explain how to solve it.

#### Model Solution:
1. **Master Theorem Parameters:**
   - $a = 2$, $b = 2 \implies n^{\log_b a} = n^{\log_2 2} = n^1$.
   - $f(n) = n \log n$.
2. **Does Standard Master Theorem Case 2 Apply?**
   - In basic Master Theorem, Case 2 requires $f(n) = \Theta(n^{\log_b a}) = \Theta(n)$.
   - Here $f(n) = n \log n$ has an extra $\log n$ factor, so it is strictly larger than $n$, but NOT polynomially larger (cannot write $f(n) = \Omega(n^{1 + \epsilon})$ for any $\epsilon > 0$).
3. **Applying the Extended Master Theorem (Case 2b):**
   - When $f(n) = \Theta(n^{\log_b a} \log^k n)$ with $k \ge 0$:
     $$T(n) = \Theta(n^{\log_b a} \log^{k+1} n)$$
   - Here $k = 1$:
     $$T(n) = \Theta(n^1 \log^{1+1} n) = \mathbf{\Theta(n \log^2 n)}$$

---

### Practice Problem 10.5: Grid BFS Shortest Path with Fuel Cost [5 Marks]
> **Question:**  
> On an $N \times M$ grid, moving to an adjacent cell takes 1 unit of time. Some cells contain fuel stations. A robot can carry at most $B$ units of battery charge (each step consumes 1 unit).  
> Explain how to model this as a graph problem to find the minimum time to reach destination $(N-1, M-1)$ starting from $(0, 0)$ with a full battery. What is the time complexity?

#### Model Solution:
1. **State Space Augmentation:**
   - A vertex is not just a coordinate $(r, c)$, but a pair:
     $$\mathbf{State = (r, c, b)}$$
     where $r \in [0, N-1]$, $c \in [0, M-1]$, and $b \in [0, B]$ is the current battery charge.
2. **Graph Size:**
   - Total number of states (vertices): $V' = N \times M \times (B + 1)$.
   - From each state $(r, c, b)$, transitions depend on whether cell $(r, c)$ is a fuel station:
     - If fuel station: charge is replenished to $B$. Moving to neighbor $(r', c')$ transitions to $(r', c', B - 1)$.
     - If normal cell: moving to neighbor transitions to $(r', c', b - 1)$ (valid only if $b \ge 1$).
3. **Algorithm:**
   - Since every move takes uniform 1 unit of time, run standard **Breadth-First Search (BFS)** on the state graph starting at $(0, 0, B)$.
4. **Complexity:**  
   $$\text{Time Complexity} = O(V' + E') = \mathbf{O(N \cdot M \cdot B)}$$
   $$\text{Space Complexity} = \mathbf{O(N \cdot M \cdot B)}$$
