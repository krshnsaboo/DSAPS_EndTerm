# DSAPS EndTerm Mastery & Exam Solutions

A comprehensive preparation and reference repository for **Data Structures and Algorithms for Problem Solving (CS1.304)** at **IIIT Hyderabad**.

This repository contains full, mathematically rigorous, and step-by-step solutions to the **End-Semester Examination (29 Nov 2025)**, alongside in-depth, self-contained **Mastery Guides** for core algorithmic topics, complete with C++ implementations, counterexamples, and advanced exam question banks.

---

## 📚 Repository Contents

### 1. 🛡️ [Master Proofs & Counterexamples Compendium](DSAPS_Last_Minute_Proofs_and_Counterexamples.md) *(Essential Exam Weapon)*
The definitive last-minute revision document focused exclusively on the two highest-weight exam requirements:
- **15 Verbatim Proofs of Optimality:** Exact, complete proofs for Cut Property, Cycle Property, Monotonic MST Invariance, Greedy Stays Ahead Induction, Interval Partitioning Depth Theorem, Gale-Shapley Proposer-Optimality, Johnson's Telescoping Potentials, 0/1 Knapsack Cut-and-Paste, Fractional Knapsack Exchange, Monotonic Knapsack, Exponential Knapsack, Pseudo-Polynomial Complexity, LPS $\equiv$ LCS, and Strassen $7n^{\log_2 7} - 6n^2$.
- **15 Classic Counterexamples with Exact Graphs & Numbers:** Detailed failure traces for constant weight shifts ($w(e)+C$), squared weights ($w(e)^2$), adding 0-weight edges from existing nodes in Johnson's, non-monotonic functions ($(x-5)^2$), MBST $\ne$ MST, `MAYBE-MST-X` failure, interval scheduling heuristics (Algs 1, 2, 4, minimum conflicts), weighted interval greedy failure, non-canonical coin systems ($\{1, 3, 4\}, \{1, 5, 7\}, \{1, 6, 10\}$), 0/1 knapsack ratio failures, and Strassen operation overhead for $n \le 32$.
- **The "Does It Change?" Invariant Matrix:** Quick-lookup table showing exact effects of 9 transformations on MST vs SPT.
- **The 10 Fatal Exam Assumptions:** Common pitfalls where students lose marks.

---

### 2. ⚡ [Ultimate Last-Day Revision Notes](DSAPS_Last_Day_Revision_Notes.md) *(Exam-Eve Master Cheat Sheet)*
A high-density distillation of every topic, theorem, recurrence, and Slip Test solution.

---

### 3. 🎯 [Master Question Bank & Exam Variations Guide](DSAPS_Master_Question_Bank_and_Variations.md) *(Comprehensive Problem Bank)*
An exhaustive compilation of every single PYQ, Slip Test, class puzzle, and theoretical variation discussed across the course, organized by topic with detailed mathematical solutions and proofs:
- **Topic 1: Johnson's Algorithm & Shortest Path Variations:** EndSem 2025 Puzzle 1 (adding edges from existing node $A$), constant shift failure, Dijkstra on longest paths.
- **Topic 2: Minimum Spanning Trees (MST):** EndSem 2025 Puzzle 2 (`MAYBE-MST-A` Reverse-Delete, `MAYBE-MST-C` Cycle-Breaking), 8 monotonic function transformations ($f(x) = \sqrt{x}, x^3, x+c, -x, 1/x, \dots$), MBST vs MST.
- **Topic 3: Gale-Shapley Stable Marriage:** EndSem 2025 Puzzle 6, proposal bounds ($n^2 - n + 1$), $O(n^2)$ uniqueness testing, receiver strategic manipulation.
- **Topic 4: Interval Scheduling & Partitioning:** The 6 heuristics (feasibility & optimality), Slide 71-72 Homework (8 classes $\implies$ 3 classrooms), Greedy Stays Ahead proof.
- **Topic 5: Dynamic Programming — Coin Change:** Slip Test 1 (10 Aug 2026) bounded 13s ($A = 45, 59, 1000$) and general $O(n A + k)$ algorithm; Slip Test 25 Aug 2025 Puzzle 4 (pseudo-polynomial proof); combinations vs permutations loop orders.
- **Topic 6: Dynamic Programming — LCS & Edit Distance:** Slip Test 2 (17 Aug 2026) Levenshtein distance (`sleeveless` vs `lessens`, `intelligent` vs `belligerent`, `melanin` vs `millennial`); Lecture Slide 31 Table Trace (`bafdecafdc` vs `abcdef`).
- **Topic 7: Dynamic Programming — Knapsack & Multiple Knapsacks:** Slip Test 3 (31 Aug 2026) Two Thieves Knapsack ($W_1 = 15, W_2 = 17$, 7 items: optimal stolen value 105); Slip Test 25 Aug 2025 Puzzles 1–3 (monotonic knapsack, equal ratios, exponential values).
- **Topic 8: Divide-and-Conquer — Integer Multiplication:** Handwritten notes from `Multiplication.pdf`, Karatsuba recurrence derivation, change of base $\log_2 3$, and timeline to Harvey-Hoeven.
- **Topic 9: Divide-and-Conquer — Matrix Multiplication & MCM:** Slip Test 4 (3 Sept 2026) Strassen terms ($c_{11}, c_{21}, c_{22}$), exact arithmetic ops ($7n^{\log_2 7} - 6n^2$), DeepMind AlphaTensor ($n^{2.7773}$), MCM chain DP.
- **Topic 10: Recurrences & Matrix Exponentiation:** Slip Test 4 Aug 2025 Amoeba population growth (Tribonacci sequence $F(t) = F(t-1) + F(t-2) + F(t-3)$) and $O(\log t)$ matrix exponentiation.
- **Topic 11: Disjoint Set Union (DSU) & Grid BFS:** EndSem 2025 Puzzle 5 (DSU path compression + rank) and Puzzle 7 (Grid BFS pathfinding).

---

### 4. 🧪 [Fresh Exam Practice Questions & Model Solutions](DSAPS_Exam_Practice_Questions_and_Solutions.md) *(50-Question Master Challenge)*
A brand-new, comprehensive mock challenge featuring **exactly 5 master-level questions per topic (50 questions total)**, designed to test deep conceptual understanding and boundary cases, with complete examiner-style model solutions:
- **Topic 1 (Johnson & Shortest Paths):** Arbitrary constant shift in potential $h(v) + C$, negative-weight dummy edges, zero-weight cycles, quadratic weight transforms, and single negative edge in $O((V+E)\log V)$.
- **Topic 2 (MST & Graph Properties):** Second-best MST single-edge swap proof & $O(E \log V)$ algorithm, `MAYBE-MST-X` failure proof, non-monotonic transformations $(x-5)^2$, $O(V)$ edge decrease update, and MSF size formula $V - k$.
- **Topic 3 (Gale-Shapley Matching):** Complete 5-proposal execution trace, non-GS matching stability audit & blocking pairs, Rural Hospital Theorem, weak stability with ties, and $O(n^2)$ stability auditor.
- **Topic 4 (Interval Scheduling & Partitioning):** Minimum Conflicts heuristic counterexample, endpoint-touching interval depth calculation ($d = 4$), machine setup time $\Delta$ reduction, weighted DP with traceback, and pigeonhole lower bound proof.
- **Topic 5 (Coin Change):** Smallest counterexample for non-canonical system $\{1, 5, 7\}$, exact-count bounded coin change trace, $O(A)$ modulo counting C++ implementation, unary encoding complexity proof, and lexicographical largest reconstruction.
- **Topic 6 (LCS & Edit Distance):** Full Levenshtein diagram and transition script (`kitten` $\to$ `sitting`), Hirschberg's D&C split recurrence, formal proof of $\text{LPS}(S) = \text{LCS}(S, S^R)$, SCS length formula proof, and $O(n \log n)$ distinct-character LCS.
- **Topic 7 (Knapsack & Multiple Knapsacks):** Counterexample proving greedy fails for equal ratio $v_i/w_i = r$, Two Knapsacks with capacities $8$ and $10$ assignment, Dual DP for $W = 10^9$ with runtime proof, forward loop unbounded degeneration explanation, and fractional knapsack exchange proof.
- **Topic 8 (Integer Multiplication):** Step-by-step Karatsuba trace for $2345 \times 6789 = 15,920,205$, Master Theorem for 3-split 6-multiplication recurrence ($O(n^{1.631})$), Toom-3 recurrence and practical overhead, bit-complexity of addition vs multiplication, and Harvey-Hoeven theoretical horizon.
- **Topic 9 (Matrix Multiplication & MCM):** Strassen $c_{21} = m_2 + m_4$ algebraic proof, exact operation count for $4 \times 4$ (247 operations), AlphaTensor real field extension, 4-matrix MCM full DP table with optimal parentheses, and Catalan recurrence proof.
- **Topic 10 (Recurrences & DSU):** Quadranacci 4-hour amoeba recurrence and $4 \times 4$ companion matrix, DSU single-optimization vs dual-optimization bounds ($\Theta(m \log n)$ vs $\Theta(m \alpha(n))$), bipartite detection with DSU, Extended Master Theorem Case 2b ($T(n) = 2T(n/2) + n \log n \implies \Theta(n \log^2 n)$), and state-space augmented grid BFS with fuel battery.

---

### 5. 📝 [End-Semester 2025 Examination Solutions](DSAPS_EndSem_Solutions.md)
Complete, verified solutions and proofs for all 7 puzzles (70 Marks Total):
- **Puzzle 1:** Johnson's Algorithm Modifications (Case 1 & Case 2 analysis, artificial negative cycles).
- **Puzzle 2:** Minimum Spanning Tree Algorithms (`MAYBE-MST-A` Reverse-Delete & `MAYBE-MST-C` Cycle-Breaking).
- **Puzzle 3:** Modifying Dijkstra for Longest Simple Paths (Acyclic forests vs Cyclic graphs).
- **Puzzle 4:** Properties of MSTs & Shortest Paths (Cycle property, weight shift invariance).
- **Puzzle 5:** Friendship Queries & Equivalence Classes (Disjoint Set Union / DSU with path compression & union by rank).
- **Puzzle 6:** Gale-Shapley Stable Marriage (Adversarial proposal cascades, stability auditing, $O(n^2)$ uniqueness test).
- **Puzzle 7:** Grid Navigation & Route Finding ($6 \times 6$ step-by-step solution, general $O(n^2)$ BFS algorithm).

---

### 6. 🚀 Topic-Wise Algorithmic Mastery Guides

Each guide is designed for deep conceptual mastery and covers intuition, mathematical proofs, edge cases, C++ code, and curated LeetCode/Codeforces problems:

- **[Johnson's Algorithm Mastery Guide](Johnsons_Algorithm_Mastery_Guide.md)**
  - All-Pairs Shortest Path on sparse graphs with negative edge weights ($O(VE + V^2 \log V)$).
  - The elevation analogy, telescoping potentials, and why the source $s$ must be an external vertex with $\text{in-degree}(s) = 0$.

- **[Minimum Spanning Tree (MST) Mastery Guide](MST_Algorithms_Mastery_Guide.md)**
  - The twin pillars: The Cut Property (inclusion) and The Cycle Property (exclusion).
  - Kruskal, Prim, Borůvka, and Reverse-Delete algorithms.
  - Invariance under monotonic transformations, Minimum Bottleneck Spanning Trees (MBST), and 12 twisted exam challenges.

- **[Gale-Shapley Stable Marriage Mastery Guide](Gale_Shapley_Stable_Marriage_Mastery_Guide.md)**
  - Deferred Acceptance, proposer-optimality, receiver-pessimality, and order invariance.
  - Complete modern C++ implementation with $O(1)$ ranking matrix, automated stability verifier, and uniqueness checker.
  - 9 advanced exam variations (Rural Hospital Theorem, Roommates problem, manipulability).

- **[Interval Scheduling & Classroom Partitioning Mastery Guide](Interval_Scheduling_Mastery_Guide.md)**
  - Comprehensive guide based on the 72-page `Interval Scheduling.pdf` and Midsem Classroom Scheduling.
  - The 6 greedy heuristics evaluated for feasibility and optimality: Alg 1 (Earliest Start), Alg 2 (Shortest Duration), Alg 3 (Delete Largest), Alg 4 (Delete Dominating - not even feasible!), Alg 5 (Delete Dominating + Earliest Start - optimal!), and Alg 6 (Earliest End Time).
  - Formal **"Greedy Stays Ahead"** induction proof.
  - Interval Partitioning: Depth theorem ($\text{Classrooms} \ge \text{Depth}$), min-heap $O(n \log n)$ room allocation, and exact solution to the 8-class homework ($d = 3$ classrooms).
  - Weighted Interval Scheduling with $O(n \log n)$ binary-search DP, and practice problems (LeetCode 435, 253, 1235).

- **[Coin Change Problem Mastery Guide](Coin_Change_Mastery_Guide.md)**
  - Canonical coin systems and counterexamples to greedy ($\{1, 3, 4\}$ for 6).
  - Proof of pseudo-polynomial time complexity ($O(n \cdot A) = O(n \cdot 2^L)$).
  - **Slip Test 1 (10 Aug 2026) Deep Dive:** Bounded denomination $13$ (at most 2) with unlimited $\{1, 6, 8\}$ for amounts $45$ (5 coins), $59$ (7 coins), and $1000$ (124 coins), plus general $O(n A + k)$ algorithm.
  - The Combinations vs Permutations loop-order invariant (LeetCode 518 vs LeetCode 377, CF 189A Cut Ribbon, LC 1449).

- **[Longest Common Subsequence (LCS) Mastery Guide](LCS_Mastery_Guide.md)**
  - Prefix property proofs, 2D DP, space optimization to $O(\min(m, n))$, and **Hirschberg's Algorithm** ($O(m + n)$ space reconstruction).
  - Step-by-step lecture walkthrough for $X = \text{"bafdecafdc"}$ vs $Y = \text{"abcdef"}$ from `Longest common subsequence.pdf`.
  - **Slip Test 2 (17 Aug 2026) Deep Dive:** Levenshtein edit distance evaluations with exact alignment & transformation notation (`dist("sleeveless", "lessens") = 6`, `dist("intelligent", "belligerent") = 5`, `dist("melanin", "millennial") = 6`), and the complete $O(n_1 \cdot n_2)$ DP algorithm.
  - Reductions to Shortest Common Supersequence (SCS), Edit Distance, Palindromic Subsequences (LPS), and LeetCode/Codeforces problems (LC 1092, LC 115, CF 1446B).

- **[Knapsack Problem Mastery Guide](Knapsack_Mastery_Guide.md)**
  - 0/1 vs Fractional vs Unbounded Knapsack.
  - Slip Test 2 analysis: monotonic weights/values, identical ratios (Subset Sum), and exponential values ($v_{i+1} = 2v_i$).
  - **Slip Test 3 (31 Aug 2026) Deep Dive:** The Two Thieves Knapsack Problem ($W_1 = 15, W_2 = 17$, 7 items: optimal stolen value $105$ leaving only Item 3), and general Two-Knapsack 3D/2D space-optimized DP ($O(n W_1 W_2)$ time, $O(W_1 W_2)$ space).
  - Dual DP for huge capacities ($W \le 10^9$), and LeetCode/Codeforces problems (LC 416, Knapsack 2, CF 864E Fire with deadlines, LC 474 2D Knapsack).

- **[Integer Multiplication Mastery Guide](Integer_Multiplication_Mastery_Guide.md)**
  - Baseline addition complexity $O(\max(\log_2 a, \log_2 b))$ vs repeated addition ($O(b \log a)$ exponential in bits).
  - From Schoolbook $\Theta(n^2)$ to Karatsuba's $O(n^{\log_2 3}) \approx O(n^{1.585})$ via Gauss's algebraic trick.
  - Instructor's visual architecture from `Multiplication.pdf` (horizontal additions/shifts vs vertical recursive multiplications).
  - Master Theorem proofs, Toom-Cook, Schönhage–Strassen FFT, and Harvey–Hoeven $O(n \log n)$.

- **[Matrix Multiplication Mastery Guide](Matrix_Multiplication_Mastery_Guide.md)**
  - Strassen's Algorithm: reducing 8 submultiplications to 7 $\implies O(n^{2.807})$.
  - **Slip Test 4 (3 Sept 2026) Deep Dive:** Exact algebraic derivations of $c_{11}, c_{21}, c_{22}$ from $m_1 \dots m_7$, exact total arithmetic operations count ($7n^{\log_2 7} - 6n^2$), and DeepMind AlphaTensor ($T(n) = 47 T(n/4) \implies O(n^{2.7773})$).
  - Matrix Chain Multiplication (MCM): why parenthesization matters, Catalan number explosion, and $O(n^3)$ dynamic programming.

---

## 🛠️ Tech Stack & Language
- **C++ (C++17 / C++20)**
- **Markdown with KaTeX LaTeX formatting**
