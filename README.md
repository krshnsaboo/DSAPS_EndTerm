# DSAPS EndTerm Mastery & Exam Solutions

A comprehensive preparation and reference repository for **Data Structures and Algorithms for Problem Solving (CS1.304)** at **IIIT Hyderabad**.

This repository contains full, mathematically rigorous, and step-by-step solutions to the **End-Semester Examination (29 Nov 2025)**, alongside in-depth, self-contained **Mastery Guides** for core algorithmic topics, complete with C++ implementations, counterexamples, and advanced exam question banks.

---

## 📚 Repository Contents

### 1. 📝 [End-Semester 2025 Examination Solutions](DSAPS_EndSem_Solutions.md)
Complete, verified solutions and proofs for all 7 puzzles (70 Marks Total):
- **Puzzle 1:** Johnson's Algorithm Modifications (Case 1 & Case 2 analysis, artificial negative cycles).
- **Puzzle 2:** Minimum Spanning Tree Algorithms (`MAYBE-MST-A` Reverse-Delete & `MAYBE-MST-C` Cycle-Breaking).
- **Puzzle 3:** Modifying Dijkstra for Longest Simple Paths (Acyclic forests vs Cyclic graphs).
- **Puzzle 4:** Properties of MSTs & Shortest Paths (Cycle property, weight shift invariance).
- **Puzzle 5:** Friendship Queries & Equivalence Classes (Disjoint Set Union / DSU with path compression & union by rank).
- **Puzzle 6:** Gale-Shapley Stable Marriage (Adversarial proposal cascades, stability auditing, $O(n^2)$ uniqueness test).
- **Puzzle 7:** Grid Navigation & Route Finding ($6 \times 6$ step-by-step solution, general $O(n^2)$ BFS algorithm).

---

### 2. 🚀 Topic-Wise Algorithmic Mastery Guides

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

- **[Knapsack Problem Mastery Guide](Knapsack_Mastery_Guide.md)**
  - 0/1 vs Fractional vs Unbounded Knapsack.
  - Slip Test 2 analysis: monotonic weights/values, identical ratios (Subset Sum), and exponential values ($v_{i+1} = 2v_i$).
  - 1D DP space optimizations, dual DP for huge capacities ($W \le 10^9$), and LeetCode/Codeforces problems (LC 416, Knapsack 2, CF 864E Fire with deadlines, LC 474 2D Knapsack).

- **[Coin Change Problem Mastery Guide](Coin_Change_Mastery_Guide.md)**
  - Canonical coin systems and counterexamples to greedy ($\{1, 3, 4\}$ for 6).
  - Proof of pseudo-polynomial time complexity ($O(n \cdot A)$).
  - The Combinations vs Permutations loop-order invariant (LeetCode 518 vs LeetCode 377, CF 189A Cut Ribbon, LC 1449).

- **[Longest Common Subsequence (LCS) Mastery Guide](LCS_Mastery_Guide.md)**
  - Prefix property proofs, 2D DP, space optimization to $O(\min(m, n))$, and **Hirschberg's Algorithm** ($O(m + n)$ space reconstruction).
  - Reductions to Shortest Common Supersequence (SCS), Edit Distance, Palindromic Subsequences (LPS), and LeetCode/Codeforces problems (LC 1092, LC 115, CF 1446B).

- **[Integer Multiplication Mastery Guide](Integer_Multiplication_Mastery_Guide.md)**
  - From Schoolbook $\Theta(n^2)$ to Karatsuba's $O(n^{\log_2 3}) \approx O(n^{1.585})$ via Gauss's algebraic trick.
  - Master Theorem proofs, Toom-Cook, Schönhage–Strassen FFT, and Harvey–Hoeven $O(n \log n)$.

- **[Matrix Multiplication Mastery Guide](Matrix_Multiplication_Mastery_Guide.md)**
  - Strassen's Algorithm: reducing 8 submultiplications to 7 $\implies O(n^{2.807})$.
  - Matrix Chain Multiplication (MCM): why parenthesization matters, Catalan number explosion, and $O(n^3)$ dynamic programming.

---

## 🛠️ Tech Stack & Language
- **C++ (C++17 / C++20)**
- **Markdown with KaTeX LaTeX formatting**
