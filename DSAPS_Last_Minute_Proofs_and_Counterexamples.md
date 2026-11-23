# DSAPS Last-Minute Exam Revision: Master Proofs & Counterexamples
## High-Yield Mathematical Proofs of Optimality & Classic Counterexample Compendium
### Course: Data Structures and Algorithms for Problem Solving (CS1.304) — IIIT Hyderabad

> **Exam Focus:** This document is engineered specifically for **Proof of Optimality** questions and **Counterexample** reasoning questions.  
> Every proof is written in the exact mathematical format examiners require (Cut-and-Paste, Greedy Stays Ahead, Induction, Exchange Arguments). Every counterexample provides the exact numbers, graphs, and failure traces.

---

## Master Table of Contents
1. [Part I: The Master Proofs of Optimality](#part-i-the-master-proofs-of-optimality)
   - [Proof 1: The Cut Property of MSTs (Cut-and-Paste)](#proof-1-the-cut-property-of-msts-cut-and-paste)
   - [Proof 2: The Cycle Property of MSTs (Cycle-Breaking)](#proof-2-the-cycle-property-of-msts-cycle-breaking)
   - [Proof 3: Monotonic Transformations Invariance on MST](#proof-3-monotonic-transformations-invariance-on-mst)
   - [Proof 4: "Greedy Stays Ahead" Induction for Interval Scheduling](#proof-4-greedy-stays-ahead-induction-for-interval-scheduling)
   - [Proof 5: Interval Partitioning Depth Lower Bound & Heap Optimality](#proof-5-interval-partitioning-depth-lower-bound--heap-optimality)
   - [Proof 6: Algorithm 5 (Delete Dominating + Earliest Start) Optimality](#proof-6-algorithm-5-delete-dominating--earliest-start-optimality)
   - [Proof 7: Proposer-Optimality in Gale-Shapley (Induction on Proposals)](#proof-7-proposer-optimality-in-gale-shapley-induction-on-proposals)
   - [Proof 8: Telescoping Potential Reweighting in Johnson's Algorithm](#proof-8-telescoping-potential-reweighting-in-johnsons-algorithm)
   - [Proof 9: Optimal Substructure for 0/1 Knapsack (Cut-and-Paste)](#proof-9-optimal-substructure-for-01-knapsack-cut-and-paste)
   - [Proof 10: Exchange Argument for Fractional Knapsack](#proof-10-exchange-argument-for-fractional-knapsack)
   - [Proof 11: Monotonic Knapsack Greedy Optimality ($w \uparrow, v \downarrow$)](#proof-11-monotonic-knapsack-greedy-optimality-w-uparrow-v-downarrow)
   - [Proof 12: Exponential Value Knapsack Greedy Optimality ($v_{i+1} = 2v_i$)](#proof-12-exponential-value-knapsack-greedy-optimality-vi1--2vi)
   - [Proof 13: Pseudo-Polynomial Complexity Proof of Coin Change & Knapsack](#proof-13-pseudo-polynomial-complexity-proof-of-coin-change--knapsack)
   - [Proof 14: Equivalence of LPS and LCS ($\text{LPS}(S) = \text{LCS}(S, S^R)$)](#proof-14-equivalence-of-lps-and-lcs-textlpss--textlcss-sr)
   - [Proof 15: Exact Arithmetic Operation Count for Strassen ($7n^{\log_2 7} - 6n^2$)](#proof-15-exact-arithmetic-operation-count-for-strassen-7nlog_2-7---6n2)
2. [Part II: The Master Counterexamples Compendium](#part-ii-the-master-counterexamples-compendium)
   - [Counterexample 1: Adding a Constant $C$ to All Edges Fails Shortest Paths](#counterexample-1-adding-a-constant-c-to-all-edges-fails-shortest-paths)
   - [Counterexample 2: Squaring Edge Weights Fails Shortest Paths ($w'(e) = w(e)^2$)](#counterexample-2-squaring-edge-weights-fails-shortest-paths-we--we2)
   - [Counterexample 3: Adding 0-Weight Edges from an Existing Vertex in Johnson's](#counterexample-3-adding-0-weight-edges-from-an-existing-vertex-in-johnsons)
   - [Counterexample 4: Non-Monotonic Transformations on MST ($f(x) = (x-5)^2$)](#counterexample-4-non-monotonic-transformations-on-mst-fx--x-52)
   - [Counterexample 5: MBST is NOT Always an MST](#counterexample-5-mbst-is-not-always-an-mst)
   - [Counterexample 6: Deleting Heaviest Incident Edge (`MAYBE-MST-X`) Fails](#counterexample-6-deleting-heaviest-incident-edge-maybe-mst-x-fails)
   - [Counterexample 7: Interval Scheduling — Earliest Start Time Fails (Alg 1)](#counterexample-7-interval-scheduling--earliest-start-time-fails-alg-1)
   - [Counterexample 8: Interval Scheduling — Shortest Duration Fails (Alg 2)](#counterexample-8-interval-scheduling--shortest-duration-fails-alg-2)
   - [Counterexample 9: Interval Scheduling — Delete Dominating Fails Feasibility (Alg 4)](#counterexample-9-interval-scheduling--delete-dominating-fails-feasibility-alg-4)
   - [Counterexample 10: Interval Scheduling — Minimum Conflicts Fails](#counterexample-10-interval-scheduling--minimum-conflicts-fails)
   - [Counterexample 11: Weighted Interval Scheduling Fails Greedy](#counterexample-11-weighted-interval-scheduling-fails-greedy)
   - [Counterexample 12: Coin Change — Non-Canonical Systems ($\{1, 3, 4\}$, $\{1, 5, 7\}$, $\{1, 6, 10\}$)](#counterexample-12-coin-change--non-canonical-systems-1-3-4-1-5-7-1-6-10)
   - [Counterexample 13: 0/1 Knapsack — Greedy by Ratio Fails](#counterexample-13-01-knapsack--greedy-by-ratio-fails)
   - [Counterexample 14: 0/1 Knapsack — Identical Ratio $v_i/w_i = r$ Fails Greedy](#counterexample-14-01-knapsack--identical-ratio-viwi--r-fails-greedy)
   - [Counterexample 15: Strassen is Slower Than Schoolbook for Small $n$ ($n \le 32$)](#counterexample-15-strassen-is-slower-than-schoolbook-for-small-n-n-le-32)
3. [Part III: The "Does It Change?" Invariant Matrix](#part-iii-the-does-it-change-invariant-matrix)
4. [Part IV: The 10 Fatal Exam Assumptions That Lose Marks](#part-iv-the-10-fatal-exam-assumptions-that-lose-marks)

---

# Part I: The Master Proofs of Optimality

---

### Proof 1: The Cut Property of MSTs (Cut-and-Paste)
> **Theorem:** Let $G = (V, E)$ be a connected, undirected graph with real edge weights. Let $(S, V \setminus S)$ be any cut in $G$. If edge $e = (u, v)$ is the **strictly lightest edge crossing the cut**, then $e$ belongs to **EVERY Minimum Spanning Tree** of $G$.

#### Verbatim Exam Proof:
1. **Assumption for Contradiction:**  
   Suppose there exists an MST $T$ of $G$ such that $e \notin T$.
2. **Cycle Creation:**  
   Since $T$ is a spanning tree, it connects all vertices in $V$. Therefore, there exists a unique simple path $P$ in $T$ connecting $u \in S$ and $v \in V \setminus S$.  
   Adding edge $e = (u, v)$ to $T$ creates **exactly one simple cycle $C = P \cup \{e\}$**.
3. **Finding a Competing Cut Edge:**  
   Because $u \in S$ and $v \in V \setminus S$, the path $P$ starts in $S$ and ends in $V \setminus S$. Thus, $P$ must cross the cut $(S, V \setminus S)$ at least once.  
   Let $e' = (x, y) \ne e$ be an edge on path $P$ that crosses the cut $(S, V \setminus S)$ (with $x \in S$ and $y \in V \setminus S$).
4. **The Cut-and-Paste Exchange:**  
   Remove $e'$ from $T$ and add $e$:
   $$T' = T \cup \{e\} \setminus \{e'\}$$
   - *Connectivity:* Removing $e'$ breaks the cycle $C$, leaving $T'$ connected and acyclic. Thus, $T'$ is a valid spanning tree.
   - *Weight Comparison:*
     $$w(T') = w(T) - w(e') + w(e)$$
5. **Deriving the Contradiction:**  
   By hypothesis, $e$ is the *strictly lightest* edge crossing cut $(S, V \setminus S)$.  
   Since $e'$ also crosses $(S, V \setminus S)$ and $e' \ne e$, we have:
   $$w(e) < w(e') \implies w(e) - w(e') < 0$$
   Therefore:
   $$w(T') < w(T)$$
   This strictly contradicts the assumption that $T$ was a Minimum Spanning Tree!  
   Hence, $e$ must belong to every MST of $G$. $\blacksquare$

---

### Proof 2: The Cycle Property of MSTs (Cycle-Breaking)
> **Theorem:** Let $C$ be any simple cycle in graph $G = (V, E)$. If edge $e = (u, v)$ is the **strictly heaviest edge on cycle $C$**, then $e$ **CANNOT belong to ANY Minimum Spanning Tree** of $G$.

#### Verbatim Exam Proof:
1. **Assumption for Contradiction:**  
   Suppose there exists an MST $T$ of $G$ such that $e \in T$.
2. **Cut Creation via Edge Deletion:**  
   Removing edge $e = (u, v)$ from tree $T$ disconnects $T$ into **exactly two connected components**, $T_1$ and $T_2$.  
   Let $S$ be the vertices of $T_1$, and $V \setminus S$ be the vertices of $T_2$ (with $u \in S$ and $v \in V \setminus S$). This defines a cut $(S, V \setminus S)$.
3. **Reconnecting via Cycle $C$:**  
   Consider the cycle $C$. The path $C \setminus \{e\}$ connects $u \in S$ and $v \in V \setminus S$.  
   Therefore, there must exist at least one other edge $e' \ne e$ on cycle $C$ that crosses the cut $(S, V \setminus S)$.
4. **The Exchange:**  
   Construct a new spanning tree:
   $$T' = T \setminus \{e\} \cup \{e'\}$$
   $T'$ connects $S$ and $V \setminus S$ without cycles, so $T'$ is a valid spanning tree.
5. **Weight Contradiction:**  
   $$w(T') = w(T) - w(e) + w(e')$$
   Since $e$ is the *strictly heaviest* edge on cycle $C$:
   $$w(e') < w(e) \implies w(T') < w(T)$$
   Contradiction to $T$ being an MST! Hence, $e \notin T$ for any MST. $\blacksquare$

---

### Proof 3: Monotonic Transformations Invariance on MST
> **Theorem:** Let $T$ be an MST of $G = (V, E)$ with edge weights $w$. If $f: \mathbb{R} \to \mathbb{R}$ is any **strictly monotonically increasing function**, then $T$ is guaranteed to be an MST of $G' = (V, E)$ with weights $w'(e) = f(w(e))$.

#### Verbatim Exam Proof:
1. **Definition of Strict Monotonicity:**  
   For any two real numbers $x, y$:
   $$x < y \iff f(x) < f(y)$$
2. **Kruskal's Algorithm Equivalence:**  
   - Kruskal’s algorithm sorts all edges: $w(e_1) \le w(e_2) \le \dots \le w(e_m)$.
   - Since $f$ is strictly increasing, applying $f$ preserves the exact same relative sorted order:
     $$f(w(e_1)) \le f(w(e_2)) \le \dots \le f(w(e_m))$$
   - Kruskal inspects edges in the **identical order** in both $G$ and $G'$.
   - The decision to include an edge depends solely on whether its endpoints belong to different connected components in the Disjoint Set Union (DSU) structure (which depends only on graph topology, not edge weights).
   - Thus, Kruskal selects the **exact same set of edges** in $G'$ as it did in $G$.
3. **Conclusion:** $T$ is an MST of $G'$. $\blacksquare$

---

### Proof 4: "Greedy Stays Ahead" Induction for Interval Scheduling
> **Theorem:** The Earliest-End-Time greedy algorithm (Algorithm 6) outputs an optimal (maximum-cardinality) set of mutually compatible intervals.

#### Verbatim Exam Proof:
1. **Framework:**  
   Let $G = \{g_1, g_2, \dots, g_k\}$ be the greedy schedule ordered by finish time.  
   Let $O = \{o_1, o_2, \dots, o_m\}$ be any arbitrary optimal schedule ordered by finish time.  
   Goal: Prove $k = m$.
2. **Lemma (Greedy Stays Ahead):** For all $r \le k$, $e(g_r) \le e(o_r)$.
   - *Base Case ($r = 1$):* Greedy chooses $g_1$ as the interval with the globally earliest finish time. Thus, $e(g_1) \le e(o_1)$. The base case holds.
   - *Inductive Hypothesis:* Assume $e(g_{r-1}) \le e(o_{r-1})$.
   - *Inductive Step ($r$):*  
     Because $O$ is a clash-free schedule:
     $$s(o_r) \ge e(o_{r-1})$$
     Applying the inductive hypothesis:
     $$s(o_r) \ge e(o_{r-1}) \ge e(g_{r-1})$$
     This proves that interval $o_r$ starts after $g_{r-1}$ finishes. Thus, $o_r$ is a **valid, compatible candidate** available for greedy to choose at step $r$.  
     Since greedy selects the candidate with the *minimal finish time*, its choice $g_r$ must satisfy:
     $$e(g_r) \le e(o_r)$$
   - The lemma is established for all $r \le k$.
3. **Contradiction (Proving $k = m$):**  
   Suppose for contradiction that $m > k \implies m \ge k + 1$.  
   By the Lemma for $r = k$:
   $$e(g_k) \le e(o_k)$$
   In the optimal schedule, interval $o_{k+1}$ exists and satisfies:
   $$s(o_{k+1}) \ge e(o_k) \ge e(g_k)$$
   This implies $o_{k+1}$ starts after the last greedy interval $g_k$ finishes!  
   Thus, $o_{k+1}$ was compatible and available for greedy to select. But greedy stopped at $k$ intervals only when **no compatible intervals remained**.  
   Contradiction! Therefore, $k = m$, and the greedy algorithm is strictly optimal. $\blacksquare$

---

### Proof 5: Interval Partitioning Depth Lower Bound & Heap Optimality
> **Theorem:** Let $d = \max_t |\{I_i \mid s_i \le t < e_i\}|$ be the depth of a set of intervals. The minimum number of classrooms required to schedule all intervals without clashes is **strictly equal to $d$**.

#### Verbatim Exam Proof:
1. **Lower Bound ($\text{Classrooms} \ge d$):**  
   At the specific timestamp $t^*$ where depth $d$ is attained, $d$ distinct intervals overlap simultaneously. Since no two overlapping intervals can share the same classroom at time $t^*$, each of these $d$ intervals must be assigned to a distinct classroom. Hence, at least $d$ classrooms are required.
2. **Upper Bound via Greedy Algorithm ($\text{Classrooms} \le d$):**  
   - Sort all intervals by **start time** $s_i$.
   - For interval $I_i$, assign it to any classroom that is free at $s_i$. If no room is free, open a new room.
   - Suppose the algorithm opens classroom $k$.  
     This only occurs when all $k - 1$ existing classrooms are currently occupied by intervals that overlap with $I_i$ at time $s_i$.
   - Including $I_i$, there are $k$ intervals simultaneously overlapping at time $s_i$.
   - By definition of depth:
     $$k \le \text{depth at time } s_i \le d$$
   - Thus, the algorithm never opens more than $d$ classrooms ($k \le d$).
3. **Conclusion:** Lower bound = Upper bound $\implies \mathbf{\text{Min Classrooms} = d}$. $\blacksquare$

---

### Proof 6: Algorithm 5 (Delete Dominating + Earliest Start) Optimality
> **Theorem:** Algorithm 5 (First delete all dominating intervals, then apply Earliest Start Time) always produces an optimal interval schedule.

#### Verbatim Exam Proof:
1. **Definition of Domination:** Interval $A = [s_A, e_A]$ dominates $B = [s_B, e_B]$ if $s_A \le s_B$ and $e_A \ge e_B$.
2. **The Non-Dominance Invariant:**  
   Let $S$ be the surviving set of intervals after all dominating intervals are deleted.  
   **Claim:** For any two intervals $A, B \in S$:
   $$s_A < s_B \iff e_A < e_B$$
   *Proof of Claim:*  
   Suppose $s_A < s_B$ but $e_A \ge e_B$. Then $s_A \le s_B$ and $e_A \ge e_B$, meaning interval $A$ dominates interval $B$. But Phase 1 deleted all dominating intervals! Contradiction. Hence, we must have $e_A < e_B$.
3. **Equivalence to Earliest End Time:**  
   Because $s_A < s_B \iff e_A < e_B$, **sorting by start time produces the exact same sequence as sorting by finish time**!
   Therefore, Earliest Start Time on the non-dominating set executes Algorithm 6 (Earliest End Time), which is provably optimal by Proof 4. $\blacksquare$

---

### Proof 7: Proposer-Optimality in Gale-Shapley (Induction on Proposals)
> **Theorem:** In the Men-Proposing Gale-Shapley algorithm, every man receives his **best valid partner** across all possible stable matchings.

#### Verbatim Exam Proof:
1. **Definition:** A woman $w$ is a *valid partner* for man $m$ if there exists at least one stable matching in which $(m, w) \in M$.
2. **Induction on Rejections:**  
   Suppose for contradiction that some man is rejected by a valid partner.  
   Consider the **very first time** during the execution of the algorithm that *any* man is rejected by a valid partner.  
   Let this occur when woman $w$ rejects man $m$ (who is valid for her).
3. **Why did $w$ reject $m$?**  
   Woman $w$ rejected $m$ because she either received a proposal from or was already engaged to some man $m'$ whom she strictly prefers to $m$:
   $$\text{In } w\text{'s list: } m' > m$$
4. **Properties of $m'$:**  
   Since this was the *first* rejection of a valid partner in the entire execution, man $m'$ has **not yet been rejected by any valid partner**.  
   Therefore, $m'$ prefers $w$ to all valid partners except possibly those who already rejected him (none of which were valid).  
   Hence, in $m'$'s preference list, $w$ is ranked higher than his partner in *any* stable matching where $m'$ does not get $w$.
5. **The Contradiction via Alternative Matching:**  
   Since $w$ is a valid partner for $m$, there exists a stable matching $M^*$ where $(m, w) \in M^*$.  
   In $M^*$:
   - Man $m'$ must be matched to some partner $w' = M^*(m') \ne w$.
   - As established in Step 4, $m'$ strictly prefers $w$ over $w'$: $w >_{m'} w'$.
   - Furthermore, woman $w$ is matched to $m$ in $M^*$, but she strictly prefers $m'$ over $m$: $m' >_w m$.
   - Therefore, the pair $(m', w)$ forms a **blocking pair** in matching $M^*$!
   - This contradicts the assumption that $M^*$ is a stable matching!
6. **Conclusion:** No man can ever be rejected by a valid partner. Each man gets his highest-ranked valid partner. $\blacksquare$

---

### Proof 8: Telescoping Potential Reweighting in Johnson's Algorithm
> **Theorem:** For any potential function $h: V \to \mathbb{R}$, edge reweighting $w'(u, v) = w(u, v) + h(u) - h(v)$ preserves the shortest path between any pair of vertices $u$ and $v$.

#### Verbatim Exam Proof:
1. **Weight of a Path under $w'$:**  
   Let $p = \langle v_0, v_1, v_2, \dots, v_k \rangle$ be any path from $u = v_0$ to $v = v_k$.  
   The reweighted path length is:
   $$w'(p) = \sum_{i=1}^k w'(v_{i-1}, v_i) = \sum_{i=1}^k \big(w(v_{i-1}, v_i) + h(v_{i-1}) - h(v_i)\big)$$
2. **Telescoping Sum Expansion:**  
   $$\begin{aligned}
   w'(p) &= \sum_{i=1}^k w(v_{i-1}, v_i) + \sum_{i=1}^k \big(h(v_{i-1}) - h(v_i)\big) \\
   &= w(p) + \big(h(v_0) - h(v_1)\big) + \big(h(v_1) - h(v_2)\big) + \dots + \big(h(v_{k-1}) - h(v_k)\big) \\
   &= w(p) + h(v_0) - h(v_k) \\
   &= w(p) + h(u) - h(v)
   \end{aligned}$$
3. **Preservation of Shortest Paths:**  
   Let $p_1$ and $p_2$ be any two paths from $u$ to $v$.  
   $$w'(p_1) - w'(p_2) = \big(w(p_1) + h(u) - h(v)\big) - \big(w(p_2) + h(u) - h(v)\big) = w(p_1) - w(p_2)$$
   The added difference $h(u) - h(v)$ depends **only on the endpoints $u$ and $v$**, not on the path chosen.  
   Therefore, $w(p_1) < w(p_2) \iff w'(p_1) < w'(p_2)$.  
   Shortest paths are strictly preserved. $\blacksquare$

---

### Proof 9: Optimal Substructure for 0/1 Knapsack (Cut-and-Paste)
> **Theorem:** If an optimal subset of items for capacity $W$ contains item $n$ (with weight $w_n$ and value $v_n$), then the remaining subset must be an optimal solution for the subproblem of the first $n-1$ items with capacity $W - w_n$.

#### Verbatim Exam Proof:
1. **Notation:**  
   Let $S \subseteq \{1, \dots, n\}$ be an optimal subset of items for capacity $W$ with total value $V^* = \sum_{i \in S} v_i$.  
   Suppose item $n \in S$. Then the remaining items $S' = S \setminus \{n\}$ have weight $\sum_{i \in S'} w_i \le W - w_n$ and value $V' = V^* - v_n$.
2. **Assumption for Contradiction:**  
   Suppose $S'$ is NOT optimal for capacity $W - w_n$ using items $\{1, \dots, n-1\}$.  
   Then there exists another subset $S'' \subseteq \{1, \dots, n-1\}$ such that:
   $$\sum_{i \in S''} w_i \le W - w_n \quad \text{AND} \quad \sum_{i \in S''} v_i > V'$$
3. **The Cut-and-Paste Construction:**  
   Construct a candidate solution for capacity $W$: $S_{\text{new}} = S'' \cup \{n\}$.
   - *Feasibility:* $\sum_{i \in S_{\text{new}}} w_i = \sum_{i \in S''} w_i + w_n \le (W - w_n) + w_n = W$. Feasible!
   - *Total Value:*
     $$\sum_{i \in S_{\text{new}}} v_i = \sum_{i \in S''} v_i + v_n > V' + v_n = (V^* - v_n) + v_n = V^*$$
4. **Contradiction:** $S_{\text{new}}$ achieves a value strictly greater than $V^*$, contradicting the optimality of $S$.  
   Hence, $S'$ must be optimal for $W - w_n$. $\blacksquare$

---

### Proof 10: Exchange Argument for Fractional Knapsack
> **Theorem:** In the Fractional Knapsack problem, greedily selecting items in decreasing order of value-to-weight ratio $r_i = v_i / w_i$ yields an optimal solution.

#### Verbatim Exam Proof:
1. **Setup:** Let $G = \langle x_1^G, \dots, x_n^G \rangle$ be the greedy solution, where items are ordered such that $r_1 \ge r_2 \ge \dots \ge r_n$.  
   Let $O = \langle x_1^O, \dots, x_n^O \rangle$ be any optimal solution.
2. **First Difference:** If $G = O$, greedy is optimal. Otherwise, let $i$ be the first item where $x_i^G \ne x_i^O$.  
   By greedy choice, greedy took as much of item $i$ as possible, so $x_i^G > x_i^O$.
3. **Finding a Compensating Item:**  
   Since both solutions fill capacity $W$, $O$ must contain more of some subsequent item $j > i$ ($x_j^O > x_j^G$).  
   Because items are sorted by ratio: $r_i \ge r_j$.
4. **The Exchange:**  
   Replace an amount of weight $\Delta w = \min(w_i(x_i^G - x_i^O), w_j(x_j^O - x_j^G)) > 0$ of item $j$ with an equal weight of item $i$ in solution $O$.
   $$\Delta \text{Value} = \Delta w \cdot r_i - \Delta w \cdot r_j = \Delta w (r_i - r_j) \ge 0$$
   The new solution is feasible and its value is $\ge w(O)$.
5. **Conclusion:** Finite repeated exchanges convert $O$ into $G$ without decreasing total value. Greedy is optimal. $\blacksquare$

---

### Proof 11: Monotonic Knapsack Greedy Optimality ($w \uparrow, v \downarrow$)
> **Theorem:** In 0/1 Knapsack, if sorting items by increasing weight also sorts them by decreasing value ($w_1 \le w_2 \le \dots \le w_n$ AND $v_1 \ge v_2 \ge \dots \ge v_n$), the greedy algorithm by ratio is strictly optimal.

#### Verbatim Exam Proof:
1. **Ratio Ordering:**  
   Since $v_1 \ge v_2 \ge \dots \ge v_n$ and $w_1 \le w_2 \le \dots \le w_n$:
   $$\frac{v_1}{w_1} \ge \frac{v_2}{w_2} \ge \dots \ge \frac{v_n}{w_n}$$
   The greedy choice always considers items in order $1, 2, 3, \dots, n$.
2. **Dominance of Item 1:**  
   For any other item $j > 1$, $w_1 \le w_j$ and $v_1 \ge v_j$. Item 1 provides more (or equal) value for less (or equal) weight.
3. **Exchange Argument:**  
   Suppose optimal solution $S^*$ does not pick Item 1, but picks some subset containing item $j$.  
   Replacing $j$ with Item 1 leaves total weight $\le W$ (since $w_1 \le w_j$) and value at least as high ($v_1 \ge v_j$).  
   Furthermore, because Item 1 is the **lightest item in the entire set**, no combination of multiple items can fit into $w_1$.  
   By induction on capacity $W - w_1$, packing greedily from Item 1 onwards is strictly optimal. $\blacksquare$

---

### Proof 12: Exponential Value Knapsack Greedy Optimality ($v_{i+1} = 2v_i$)
> **Theorem:** If $v_{i+1} = 2 v_i$ for all items, greedily taking the item with the highest value whenever it fits is strictly optimal.

#### Verbatim Exam Proof:
1. **Sum of Smaller Items:**  
   Consider item $k$. The sum of values of all items smaller than $k$ is:
   $$\sum_{j=1}^{k-1} v_j = v_1 \sum_{j=0}^{k-2} 2^j = v_1 (2^{k-1} - 1) < v_1 \cdot 2^{k-1} = v_k$$
2. **Strict Dominance:**  
   $$\sum_{j=1}^{k-1} v_j < v_k$$
   Even packing **every single smaller item combined** produces a total value strictly less than $v_k$!
3. **Optimality of Highest Value First:**  
   If item $k$ can physically fit into the remaining capacity, omitting item $k$ can never be compensated for by any subset of smaller items. Therefore, scanning from largest value to smallest value and greedily taking each item if it fits is strictly optimal. $\blacksquare$

---

### Proof 13: Pseudo-Polynomial Complexity Proof of Coin Change & Knapsack
> **Theorem:** The $O(n \cdot A)$ Coin Change and $O(n \cdot W)$ Knapsack DP algorithms are **pseudo-polynomial**, not polynomial.

#### Verbatim Exam Proof:
1. **Definition of Polynomial Time:** An algorithm runs in polynomial time if and only if its number of operations is bounded by a polynomial in the **length of the input in bits ($L$)**.
2. **Input Representation:**  
   The capacity $W$ (or amount $A$) is an integer encoded in standard binary.  
   The number of bits required to write $W$ is:
   $$L = \lceil \log_2(W + 1) \rceil$$
3. **Magnitude in Terms of Bit-Length:**  
   $$W \approx 2^L$$
4. **Substituting into Complexity:**  
   $$\text{Running Time} = O(n \cdot W) = O(n \cdot 2^L)$$
5. **Conclusion:** The running time is **exponential in the input bit-length $L$**. Therefore, the algorithm is **pseudo-polynomial**. (It is polynomial only if $W$ is given in unary notation, where $L = W$). $\blacksquare$

---

### Proof 14: Equivalence of LPS and LCS ($\text{LPS}(S) = \text{LCS}(S, S^R)$)
> **Theorem:** The Longest Palindromic Subsequence of string $S$ has length exactly equal to $\text{LCS}(S, S^R)$, where $S^R$ is the reverse of $S$.

#### Verbatim Exam Proof:
1. **Inequality 1 ($|\text{LPS}(S)| \le |\text{LCS}(S, S^R)|$):**  
   Let $P$ be any palindromic subsequence of $S$. By definition, $P = P^R$.  
   Since $P$ is a subsequence of $S$, $P^R$ is a subsequence of $S^R$.  
   Because $P = P^R$, $P$ is simultaneously a subsequence of $S$ and $S^R$.  
   Thus, $P$ is a common subsequence of $S$ and $S^R$, which implies:
   $$|P| \le |\text{LCS}(S, S^R)| \implies |\text{LPS}(S)| \le |\text{LCS}(S, S^R)|$$
2. **Inequality 2 ($|\text{LCS}(S, S^R)| \le |\text{LPS}(S)|$):**  
   Let $C$ be an optimal LCS between $S$ and $S^R$. By the symmetry of the string and its reverse, the reversed subsequence $C^R$ is also a valid common subsequence. We can always construct a palindromic subsequence of length $|C|$ in $S$. Thus:
   $$|\text{LCS}(S, S^R)| \le |\text{LPS}(S)|$$
3. **Conclusion:** Both bounds force $|\text{LPS}(S)| = |\text{LCS}(S, S^R)|$. $\blacksquare$

---

### Proof 15: Exact Arithmetic Operation Count for Strassen ($7n^{\log_2 7} - 6n^2$)
> **Theorem:** Strassen’s algorithm on two $n \times n$ matrices performs exactly $\mathbf{7n^{\log_2 7} - 6n^2}$ total arithmetic operations $(\times, +, -)$.

#### Verbatim Exam Proof:
1. **Multiplications $M(n)$:**
   $$M(n) = 7 M(n/2), \quad M(1) = 1 \implies M(n) = 7^{\log_2 n} = \mathbf{n^{\log_2 7}}$$
2. **Additions/Subtractions $A(n)$:**  
   At each step, 10 submatrix additions prepare the inputs for $m_1 \dots m_7$, and 8 submatrix additions combine them into $c_{11}, c_{12}, c_{21}, c_{22}$.  
   Total submatrix operations $= 18 \times (n/2)^2 = \frac{18}{4} n^2 = \frac{9}{2} n^2$.
   $$A(n) = 7 A(n/2) + \frac{9}{2} n^2, \quad A(1) = 0$$
3. **Solving the Addition Recurrence ($n = 2^k$):**
   $$A(2^k) = 18 \sum_{j=0}^{k-1} 7^j \cdot 4^{k-1-j} = 18 \cdot 4^{k-1} \sum_{j=0}^{k-1} \left(\frac{7}{4}\right)^j = 18 \cdot \frac{4^k}{4} \cdot \frac{4}{3} \left[ \left(\frac{7}{4}\right)^k - 1 \right] = 6 (7^k - 4^k)$$
   Since $7^k = n^{\log_2 7}$ and $4^k = n^2$:
   $$\mathbf{A(n) = 6 n^{\log_2 7} - 6 n^2}$$
4. **Total Arithmetic Operations:**
   $$\text{Total Ops} = M(n) + A(n) = n^{\log_2 7} + (6 n^{\log_2 7} - 6 n^2) = \mathbf{7 n^{\log_2 7} - 6 n^2} \quad \blacksquare$$

---

# Part II: The Master Counterexamples Compendium

---

### Counterexample 1: Adding a Constant $C$ to All Edges Fails Shortest Paths
- **Flawed Idea:** To eliminate negative weights, find $w_{\min} < 0$ and set $w'(e) = w(e) + |w_{\min}|$.
- **Why It Fails:** Paths have different edge counts. A path with $k$ edges is penalized by $+k \cdot C$.
- **Exact Counterexample:**
  - Graph with source $u$ and target $v$:
    - **Path 1:** 1 edge of weight **$5$**.
    - **Path 2:** 3 edges of weights **$1, 1, 1$** (Total $= 3$).
  - In $G$: Path 2 is strictly shorter ($3 < 5$).
  - Add $C = 2$ to all edges:
    - $w'(\text{Path 1}) = 5 + 1(2) = \mathbf{7}$.
    - $w'(\text{Path 2}) = (1+2) + (1+2) + (1+2) = 3 + 3(2) = \mathbf{9}$.
  - In $G'$: Path 1 is shorter ($7 < 9$). Shortest path inverted!

---

### Counterexample 2: Squaring Edge Weights Fails Shortest Paths ($w'(e) = w(e)^2$)
- **Flawed Idea:** "Squaring is strictly increasing for $x > 0$, so shortest paths should be preserved."
- **Why It Fails:** Shortest paths depend on the **sum of weights**, and $(a + b)^2 \ne a^2 + b^2$.
- **Exact Counterexample:**
  - **Path 1:** 1 edge of weight **$5$**. Length in $G = 5$.
  - **Path 2:** 2 edges of weights **$3, 3$**. Length in $G = 3 + 3 = 6$.
  - In $G$: Path 1 is shorter ($5 < 6$).
  - Square all weights:
    - Length of Path 1 $= 5^2 = \mathbf{25}$.
    - Length of Path 2 $= 3^2 + 3^2 = 9 + 9 = \mathbf{18}$.
  - In $G'$: Path 2 is strictly shorter ($18 < 25$). Shortest path inverted!

---

### Counterexample 3: Adding 0-Weight Edges from an Existing Vertex in Johnson's
- **Flawed Idea:** Instead of introducing an external dummy node $s \notin V$, pick existing node $A \in V$ and add 0-weight edges from $A$ to all other nodes.
- **Why It Fails:** If $A$ has incoming edges, it introduces **artificial negative cycles**!
- **Exact Counterexample:**
  - Let $G$ have vertices $A, B$ and a single directed edge $B \xrightarrow{-2} A$.
  - $G$ has zero cycles (it is a DAG). Shortest path $B \to A$ is $-2$.
  - Add 0-weight edge $A \xrightarrow{0} B$.
  - Now cycle $A \to B \to A$ exists with weight $0 + (-2) = \mathbf{-2 < 0}$!
  - Bellman-Ford detects a negative cycle and aborts, falsely claiming the graph is invalid!

---

### Counterexample 4: Non-Monotonic Transformations on MST ($f(x) = (x-5)^2$)
- **Flawed Idea:** "Applying any mathematical function to edge weights keeps the MST."
- **Why It Fails:** Non-monotonic functions reverse the relative ordering of edges.
- **Exact Counterexample:**
  - Edge weights $w(e_1) = 2$ and $w(e_2) = 5$.
  - In $G$: $w(e_1) < w(e_2)$ ($2 < 5$). Kruskal prefers $e_1$.
  - Apply $f(x) = (x - 5)^2$:
    - $f(w(e_1)) = (2 - 5)^2 = \mathbf{9}$.
    - $f(w(e_2)) = (5 - 5)^2 = \mathbf{0}$.
  - Now $e_2$ is strictly lighter ($0 < 9$). The MST changes.

---

### Counterexample 5: MBST is NOT Always an MST
- **Flawed Idea:** "Because every MST minimizes the bottleneck edge, any tree that minimizes the bottleneck edge must be an MST."
- **Why It Fails:** An MBST only cares about the single largest edge; other edges can be unnecessarily heavy.
- **Exact Counterexample:**
  - Triangle graph with vertices $A, B, C$:
    - $w(A, B) = 1, \quad w(B, C) = 10, \quad w(A, C) = 10$.
  - **Tree 1 (MST):** Edges $(A, B)$ [1] and $(B, C)$ [10].  
    Total weight $= \mathbf{11}$, Bottleneck edge $= \mathbf{10}$.
  - **Tree 2:** Edges $(A, C)$ [10] and $(B, C)$ [10].  
    Total weight $= \mathbf{20}$, Bottleneck edge $= \mathbf{10}$.
  - Tree 2 is an MBST (bottleneck 10 is minimal), but it is NOT an MST ($20 > 11$)!

---

### Counterexample 6: Deleting Heaviest Incident Edge (`MAYBE-MST-X`) Fails
- **Flawed Idea:** "Iterate through each vertex $v$ and delete its heaviest incident edge if it doesn't disconnect the graph."
- **Why It Fails:** The Cycle Property applies to the heaviest edge of an **entire cycle**, not a local vertex neighborhood.
- **Exact Counterexample:**
  - Diamond graph on vertices $A, B, C, D$ with 4 cycle edges:
    - $(A, B) = 10, \quad (B, C) = 1, \quad (C, D) = 10, \quad (D, A) = 2$.
  - Visiting vertex $A$: Incident edges are 10 and 2. Deletes $(A, B) = 10$.
  - Visiting vertex $C$: Incident edges are 10 and 1. Deletes $(C, D) = 10$.
  - Graph now contains only $(D, A) = 2$ and $(B, C) = 1$. Vertices $\{B, C\}$ are completely disconnected from $\{A, D\}$!

---

### Counterexample 7: Interval Scheduling — Earliest Start Time Fails (Alg 1)
- **Strategy:** Greedily select the interval that starts earliest.
- **Why It Fails:** An early-starting interval can have an enormous duration, blocking many non-clashing short jobs.
- **Exact Counterexample:**
  - $I_1 = [1, 100]$ (Starts earliest at $t = 1$).
  - $I_2 = [2, 5], \quad I_3 = [6, 10], \quad I_4 = [11, 15]$.
  - **Greedy picks:** $I_1 = [1, 100] \implies \mathbf{1 \text{ interval}}$.
  - **Optimal picks:** $\{I_2, I_3, I_4\} \implies \mathbf{3 \text{ intervals}}$.

---

### Counterexample 8: Interval Scheduling — Shortest Duration Fails (Alg 2)
- **Strategy:** Greedily select the interval with the smallest length $e_i - s_i$.
- **Why It Fails:** A tiny interval in the center can conflict with two long intervals on either side.
- **Exact Counterexample:**
  - $I_1 = [1, 10]$ (Length 9)
  - $I_2 = [9, 11]$ (Length 2 — Shortest!)
  - $I_3 = [10, 20]$ (Length 10)
  - **Greedy picks:** $I_2 = [9, 11] \implies \mathbf{1 \text{ interval}}$ (discards $I_1$ and $I_3$).
  - **Optimal picks:** $\{I_1, I_3\} \implies \mathbf{2 \text{ intervals}}$ (assuming half-open intervals).

---

### Counterexample 9: Interval Scheduling — Delete Dominating Fails Feasibility (Alg 4)
- **Strategy:** Delete every interval that completely contains another interval.
- **Why It Fails:** Does NOT resolve partial overlaps! The output contains direct clashes.
- **Exact Counterexample:**
  - $I_1 = [1, 5], \quad I_2 = [3, 8]$.
  - Does $I_1$ dominate $I_2$? No ($e_1 = 5 < 8$).
  - Does $I_2$ dominate $I_1$? No ($s_2 = 3 > 1$).
  - Algorithm deletes **nothing** and returns $\{[1, 5], [3, 8]\}$.
  - Clashes over $[3, 5] \implies$ **NOT FEASIBLE**.

---

### Counterexample 10: Interval Scheduling — Minimum Conflicts Fails
- **Strategy:** Greedily pick the interval with the fewest conflicting neighbors.
- **Why It Fails:** Local conflict count fails to capture chain structure.
- **Exact Counterexample:**
  - Set of intervals where choosing a 2-conflict interval destroys two independent 3-conflict intervals that together anchor a larger independent set.

---

### Counterexample 11: Weighted Interval Scheduling Fails Greedy
- **Strategy:** Apply Earliest Finish Time to weighted intervals.
- **Why It Fails:** An early-finishing interval can carry negligible value, blocking an enormously valuable job.
- **Exact Counterexample:**
  - $I_1 = [1, 2]$ with Value $= \mathbf{1}$.
  - $I_2 = [1, 10]$ with Value $= \mathbf{1000}$.
  - Earliest finish picks $I_1$ (finishes at 2) $\implies$ total profit **1**.
  - Optimal picks $I_2 \implies$ total profit **1000**. Dynamic programming is required!

---

### Counterexample 12: Coin Change — Non-Canonical Systems ($\{1, 3, 4\}$, $\{1, 5, 7\}$, $\{1, 6, 10\}$)
- **Counterexample A ($\{1, 3, 4\}$):** Target $A = 6$.
  - Greedy: $4 + 1 + 1 \implies \mathbf{3 \text{ coins}}$.
  - Optimal: $3 + 3 \implies \mathbf{2 \text{ coins}}$.
- **Counterexample B ($\{1, 5, 7\}$):** Target $A = 10$.
  - Greedy: $7 + 1 + 1 + 1 \implies \mathbf{4 \text{ coins}}$.
  - Optimal: $5 + 5 \implies \mathbf{2 \text{ coins}}$.
- **Counterexample C ($\{1, 6, 10\}$):** Target $A = 12$.
  - Greedy: $10 + 1 + 1 \implies \mathbf{3 \text{ coins}}$.
  - Optimal: $6 + 6 \implies \mathbf{2 \text{ coins}}$.

---

### Counterexample 13: 0/1 Knapsack — Greedy by Ratio Fails
- **Strategy:** Pick items with largest value-to-weight ratio $v_i / w_i$.
- **Why It Fails:** Knapsack capacity is discrete; greedy ratio leaves unfilled gaps.
- **Exact Counterexample:**
  - Capacity $W = 50$.
  - Item 1: wt $10$, val $60$ (ratio $6.0$)
  - Item 2: wt $20$, val $100$ (ratio $5.0$)
  - Item 3: wt $30$, val $120$ (ratio $4.0$)
  - **Greedy picks:** Item 1 (wt 10) + Item 2 (wt 20) $\implies$ Total wt $= 30 \le 50$, Total Val $= \mathbf{160}$.
  - **Optimal picks:** Item 2 (wt 20) + Item 3 (wt 30) $\implies$ Total wt $= 50 \le 50$, Total Val $= \mathbf{220}$!

---

### Counterexample 14: 0/1 Knapsack — Identical Ratio $v_i/w_i = r$ Fails Greedy
- **Strategy:** When all ratios are equal, greedily pick the smallest weight item.
- **Why It Fails:** Packing small items can block a single item that fills the entire capacity perfectly.
- **Exact Counterexample:**
  - Capacity $W = 10$, ratio $r = 1$.
  - Items: $(4, 4)$, $(4, 4)$, $(10, 10)$.
  - **Greedy picks:** $(4, 4) + (4, 4) \implies$ Total Val $= \mathbf{8}$.
  - **Optimal picks:** $(10, 10) \implies$ Total Val $= \mathbf{10}$.

---

### Counterexample 15: Strassen is Slower Than Schoolbook for Small $n$ ($n \le 32$)
- **Why It Fails:** Strassen trades 1 multiplication for 18 matrix additions. The constant factor is enormous.
- **Exact Calculation for $n = 4$:**
  - Strassen total operations: $7(4^{\log_2 7}) - 6(4^2) = 7(49) - 96 = \mathbf{247 \text{ operations}}$.
  - Schoolbook total operations: $4^3 \text{ mults} + 4^2(3) \text{ adds} = 64 + 48 = \mathbf{112 \text{ operations}}$.
  - Strassen performs more than double the operations of standard multiplication for $n = 4$!

---

# Part III: The "Does It Change?" Invariant Matrix

| Transformation / Modification | MST Changes? | Shortest Path Tree (SPT) Changes? | Why? (Core Invariant) |
|:---|:---:|:---:|:---|
| **$f(x) = x + c$** ($c > 0$) | **NO** | **YES!** | MST shifts by $(V-1)c$. SPT penalizes paths with more edges ($+k \cdot c$). |
| **$f(x) = c \cdot x$** ($c > 0$) | **NO** | **NO** | Strictly scales all path and cut comparisons identically. |
| **$f(x) = \sqrt{x}$** ($x \ge 0$) | **NO** | **YES!** | Strictly increasing $\implies$ cut orders preserved. But $\sqrt{a} + \sqrt{b} \ne \sqrt{a+b}$. |
| **$f(x) = x^3$** | **NO** | **YES!** | Strictly increasing over $\mathbb{R}$. But $(a+b)^3 \ne a^3 + b^3$. |
| **$f(x) = x^2$** (with neg weights) | **YES** | **YES** | Non-monotonic: $-5 < 2$, but $(-5)^2 = 25 > 2^2 = 4$. Order inverts. |
| **$f(x) = -x$** | **YES (Inverts!)**| **YES (Inverts!)**| Minimization becomes Maximization. MST becomes MaxST. |
| **$f(x) = 1/x$** ($x > 0$) | **YES (Inverts!)**| **YES** | Strictly decreasing function inverts relative order. |
| **$f(x) = \lfloor x \rfloor$** | **Original Valid**| **YES** | Creates ties. Original MST remains valid, but uniqueness is lost. |
| **$w'(e) = w(e) + h(u) - h(v)$** | **YES** | **NO!** | Johnson reweighting telescopes on paths, but distorts cuts. |

---

# Part IV: The 10 Fatal Exam Assumptions That Lose Marks

1. **Fatal Assumption 1:** *"Because an algorithm runs in $O(n \cdot W)$, it is polynomial time."*  
   **Correction:** It is **PSEUDO-POLYNOMIAL**. $W$ is encoded in binary, so its input size is $L = \log_2 W$ bits. The runtime is $O(n \cdot 2^L)$, which is exponential in input size!

2. **Fatal Assumption 2:** *"Since $\sqrt{x}$ is strictly increasing, it doesn't change shortest paths."*  
   **Correction:** It does NOT change MST, but it **DOES change Shortest Paths**! Shortest path depends on path *sums*, and $\sqrt{4} + \sqrt{4} = 4 = \sqrt{1} + \sqrt{9} = 4$, whereas originally $4+4=8 < 1+9=10$.

3. **Fatal Assumption 3:** *"Every Minimum Bottleneck Spanning Tree is an MST."*  
   **Correction:** **FALSE.** Every MST is an MBST, but an MBST can have non-bottleneck edges that are arbitrarily heavy.

4. **Fatal Assumption 4:** *"Gale-Shapley is fair to both men and women."*  
   **Correction:** Gale-Shapley is strictly **asymmetric**. Proposers receive their best valid partner; receivers receive their **worst valid partner**.

5. **Fatal Assumption 5:** *"Deleting dominating intervals (Algorithm 4) produces a valid schedule."*  
   **Correction:** **NOT FEASIBLE.** It leaves partial overlaps (e.g., $[1, 5]$ and $[3, 8]$) completely untouched.

6. **Fatal Assumption 6:** *"In 0/1 Knapsack, the capacity loop can run in either direction."*  
   **Correction:** Running forward ($w_i \to W$) degenerates into **Unbounded Knapsack**. Running backward ($W \to w_i$) is mandatory to enforce $0/1$.

7. **Fatal Assumption 7:** *"Coin Change combinations vs permutations uses different DP formulas."*  
   **Correction:** The recurrence is identical; **only the loop nesting order changes**! Coin outer $\implies$ Combinations. Amount outer $\implies$ Permutations.

8. **Fatal Assumption 8:** *"We can reweight graphs by adding $|w_{\min}|$ to all edges."*  
   **Correction:** Strictly fails because paths with more edges are penalized more heavily ($+k \cdot |w_{\min}|$).

9. **Fatal Assumption 9:** *"In DSU, Path Compression alone gives $O(\alpha(n))$."*  
   **Correction:** Path Compression alone gives $\mathbf{O(m \log n)}$. **Both** Path Compression and Union by Rank are required to achieve $\mathbf{O(m \cdot \alpha(n))}$.

10. **Fatal Assumption 10:** *"Longest Simple Path can be solved by running Dijkstra on negated edge weights."*  
    **Correction:** General Longest Path is **NP-Hard**. Negating edge weights creates negative cycles, causing Dijkstra to loop or fail. It works only on DAGs and trees!
