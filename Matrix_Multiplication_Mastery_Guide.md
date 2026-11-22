# Mastering Matrix Multiplication: Strassen's Algorithm & Matrix Chain Multiplication (MCM)

> **Target Audience:** Students preparing for rigorous algorithmic exams (e.g., IIIT-H DSAPS, GATE, advanced design & analysis of algorithms).  
> **Prerequisites:** None assumed! Built from scratch with recursion trees, Master Theorem proofs, DP chain-length mechanics, C++ code, and classic exam variations.

---

## Table of Contents
1. [The Two Facets of Matrix Multiplication](#1-the-two-facets-of-matrix-multiplication)
   - [Facet 1: Fast Matrix Multiplication (Algorithmic Speedup - Strassen)](#facet-1-fast-matrix-multiplication-algorithmic-speedup---strassen)
   - [Facet 2: Matrix Chain Multiplication (Optimal Parenthesization - DP)](#facet-2-matrix-chain-multiplication-optimal-parenthesization---dp)
2. [Standard Multiplication vs Naive Block Divide-and-Conquer](#2-standard-multiplication-vs-naive-block-divide-and-conquer)
   - [The Standard 3-Loop Approach ($\Theta(n^3)$)](#the-standard-3-loop-approach-thetan3)
   - [Naive Block Matrix Partitioning ($T(n) = 8T(n/2) + \Theta(n^2)$)](#naive-block-matrix-partitioning-tn--8tn2--thetan2)
3. [Strassen’s Algorithm: Breaking the $\Theta(n^3)$ Barrier](#3-strassens-algorithm-breaking-the-thetan3-barrier)
   - [The Breakthrough: Reducing 8 Multiplications to 7](#the-breakthrough-reducing-8-multiplications-to-7)
   - [The 7 Magic Products ($M_1$ through $M_7$)](#the-7-magic-products-m_1-through-m_7)
   - [Reconstructing the Product Matrix $C$ (Slip Test 4, Puzzle 1)](#reconstructing-the-product-matrix-c-slip-test-4-puzzle-1)
   - [Master Theorem Proof of $O(n^{\log_2 7}) \approx O(n^{2.807})$ (Slip Test 4, Puzzle 2)](#master-theorem-proof-of-onlog_2-7-approx-on2807-slip-test-4-puzzle-2)
   - [Exact Count of Total Arithmetic Operations: $7n^{\log_2 7} - 6n^2$ (Slip Test 4 Bonus)](#exact-count-of-total-arithmetic-operations-7nlog_2-7---6n2-slip-test-4-bonus)
   - [Practical Considerations: Crossover Threshold & Numerical Stability](#practical-considerations-crossover-threshold--numerical-stability)
4. [The Theoretical Frontier of Matrix Multiplication & AlphaTensor](#4-the-theoretical-frontier-of-matrix-multiplication--alphatensor)
   - [DeepMind AlphaTensor: $4 \times 4$ Binary Matrices (Slip Test 4, Puzzle 3)](#deepmind-alphatensor-4-times-4-binary-matrices-slip-test-4-puzzle-3)
   - [The Timeline of Exponent $\omega$](#the-timeline-of-exponent-omega)
5. [Matrix Chain Multiplication (MCM): Dynamic Programming](#5-matrix-chain-multiplication-mcm-dynamic-programming)
   - [Why Order of Multiplication Matters (The 10x Cost Explosion)](#why-order-of-multiplication-matters-the-10x-cost-explosion)
   - [Why Brute Force Fails (The Catalan Number Explosion)](#why-brute-force-fails-the-catalan-number-explosion)
   - [Optimal Substructure & Recurrence Relation](#optimal-substructure--recurrence-relation)
   - [The Diagonal (Chain-Length) Table Filling Order](#the-diagonal-chain-length-table-filling-order)
   - [Reconstructing the Optimal Parenthesization](#reconstructing-the-optimal-parenthesization)
6. [Complete Production-Grade C++ Implementation](#6-complete-production-grade-c-implementation)
   - [Part 1: Strassen's Algorithm](#part-1-strassens-algorithm)
   - [Part 2: Matrix Chain Multiplication Solver](#part-2-matrix-chain-multiplication-solver)
7. [Advanced Exam Reasoning Questions & Traps](#7-advanced-exam-reasoning-questions--traps)
   - [Trap 1: What if Matrix Dimensions are Not Powers of 2?](#trap-1-what-if-matrix-dimensions-are-not-powers-of-2)
   - [Trap 2: MCM When All Matrices are Square ($n \times n$)](#trap-2-mcm-when-all-matrices-are-square-n-times-n)
   - [Trap 3: Memory Allocations in Strassen's Algorithm](#trap-3-memory-allocations-in-strassens-algorithm)
8. [Exam Day Summary Checklist](#8-exam-day-summary-checklist)

---

## 1. The Two Facets of Matrix Multiplication

When exams test "Matrix Multiplication", they almost always test one of two completely distinct problems:

1. **Multiplying TWO Matrices Fast (Strassen's Algorithm):**  
   Given two $n \times n$ matrices $A$ and $B$, how can we compute $C = A \times B$ in strictly less than $O(n^3)$ operations using Divide-and-Conquer?
2. **Multiplying a CHAIN of Matrices Efficiently (MCM):**  
   Given a sequence of $n$ matrices $A_1 \times A_2 \times \dots \times A_n$ with varying dimensions, what is the optimal **parenthesization** (order of multiplication) to minimize total scalar multiplications using Dynamic Programming?

---

## 2. Standard Multiplication vs Naive Block Divide-and-Conquer

### The Standard 3-Loop Approach ($\Theta(n^3)$)
To multiply two $n \times n$ matrices $A$ and $B$:
$$C[i][j] = \sum_{k=1}^n A[i][k] \times B[k][j]$$
- 3 nested loops: each runs $n$ times.
- Scalar multiplications: $n \times n \times n = n^3$.
- Scalar additions: $n^2(n - 1) = n^3 - n^2$.
- **Time Complexity:** $\mathbf{\Theta(n^3)}$.

---

### Naive Block Matrix Partitioning ($T(n) = 8T(n/2) + \Theta(n^2)$)
Divide each $n \times n$ matrix into four $(n/2) \times (n/2)$ submatrices:
$$\begin{pmatrix} A_{11} & A_{12} \\ A_{21} & A_{22} \end{pmatrix} \begin{pmatrix} B_{11} & B_{12} \\ B_{21} & B_{22} \end{pmatrix} = \begin{pmatrix} C_{11} & C_{12} \\ C_{21} & C_{22} \end{pmatrix}$$

Expanding the block matrix multiplication:
- $C_{11} = A_{11} B_{11} + A_{12} B_{21}$
- $C_{12} = A_{11} B_{12} + A_{12} B_{22}$
- $C_{21} = A_{21} B_{11} + A_{22} B_{21}$
- $C_{22} = A_{21} B_{12} + A_{22} B_{22}$

#### Analysis:
- Requires **8 multiplications** of $(n/2) \times (n/2)$ matrices.
- Requires **4 additions** of $(n/2) \times (n/2)$ matrices ($O(n^2)$ work).
- **Recurrence:**
  $$T(n) = 8 T(n/2) + \Theta(n^2)$$
- By Master Theorem: $a = 8, b = 2 \implies n^{\log_2 8} = \mathbf{n^3}$.
- Result: **$\Theta(n^3)$ (Zero improvement over the naive 3 loops!)**.

---

## 3. Strassen’s Algorithm: Breaking the $\Theta(n^3)$ Barrier

In 1969, **Volker Strassen** shocked the mathematical world by proving that matrix multiplication can be computed using **only 7 submatrix multiplications** instead of 8!

### The 7 Magic Products ($M_1$ through $M_7$)
Each $M_k$ is computed using **exactly ONE multiplication** of linear combinations of submatrices:
1. $M_1 = (A_{11} + A_{22}) \times (B_{11} + B_{22})$
2. $M_2 = (A_{21} + A_{22}) \times B_{11}$
3. $M_3 = A_{11} \times (B_{12} - B_{22})$
4. $M_4 = A_{22} \times (B_{21} - B_{11})$
5. $M_5 = (A_{11} + A_{12}) \times B_{22}$
6. $M_6 = (A_{21} - A_{11}) \times (B_{11} + B_{12})$
7. $M_7 = (A_{12} - A_{22}) \times (B_{21} + B_{22})$

---

### Reconstructing the Product Matrix $C$ (Slip Test 4, Puzzle 1)

In Slip Test 4 (3 Sept 2026), you are given the 7 Strassen variables $m_1 \dots m_7$ and the formula $c_{12} = m_3 + m_5$, and asked to evaluate $c_{11}, c_{21}, c_{22}$ using only addition and subtraction operations:

- **$c_{11} = m_1 + m_4 - m_5 + m_7$**
- **$c_{12} = m_3 + m_5$** *(given as reference)*
- **$c_{21} = m_2 + m_4$**
- **$c_{22} = m_1 - m_2 + m_3 + m_6$**

#### Rigorous Algebraic Verifications:

1. **Verification of $c_{11}$:**
   $$m_1 = a_{11}b_{11} + a_{11}b_{22} + a_{22}b_{11} + a_{22}b_{22}$$
   $$m_4 = a_{22}b_{21} - a_{22}b_{11}$$
   $$-m_5 = -a_{11}b_{22} - a_{12}b_{22}$$
   $$m_7 = a_{12}b_{21} + a_{12}b_{22} - a_{22}b_{21} - a_{22}b_{22}$$
   Summing:
   $$m_1 + m_4 - m_5 + m_7 = a_{11}b_{11} + a_{12}b_{21} \equiv \mathbf{c_{11}} \quad \checkmark$$

2. **Verification of $c_{21}$:**
   $$m_2 = a_{21}b_{11} + a_{22}b_{11}$$
   $$m_4 = a_{22}b_{21} - a_{22}b_{11}$$
   Summing:
   $$m_2 + m_4 = a_{21}b_{11} + (a_{22}b_{11} - a_{22}b_{11}) + a_{22}b_{21} = a_{21}b_{11} + a_{22}b_{21} \equiv \mathbf{c_{21}} \quad \checkmark$$

3. **Verification of $c_{22}$:**
   $$m_1 = a_{11}b_{11} + a_{11}b_{22} + a_{22}b_{11} + a_{22}b_{22}$$
   $$-m_2 = -a_{21}b_{11} - a_{22}b_{11}$$
   $$m_3 = a_{11}b_{12} - a_{11}b_{22}$$
   $$m_6 = a_{21}b_{11} + a_{21}b_{12} - a_{11}b_{11} - a_{11}b_{12}$$
   Summing:
   $$(a_{11}b_{11} - a_{11}b_{11}) + (a_{11}b_{22} - a_{11}b_{22}) + (a_{22}b_{11} - a_{22}b_{11}) + (-a_{21}b_{11} + a_{21}b_{11}) + (a_{11}b_{12} - a_{11}b_{12}) + a_{21}b_{12} + a_{22}b_{22}$$
   $$= a_{21}b_{12} + a_{22}b_{22} \equiv \mathbf{c_{22}} \quad \checkmark$$

---

### Master Theorem Proof of $O(n^{\log_2 7}) \approx O(n^{2.807})$ (Slip Test 4, Puzzle 2)

Let $T(n)$ be the number of multiplications required to multiply two $n \times n$ matrices.  
Dividing the matrices into four $(n/2) \times (n/2)$ submatrices requires computing the $7$ variables $m_1 \dots m_7$:
$$T(n) = 7 T(n/2), \quad T(1) = 1$$

- $a = 7$ (subproblems of size $n/2$)
- $b = 2$ (shrink factor)
- By the Master Theorem (or direct substitution):
  $$T(n) = 7^{\log_2 n} = n^{\log_2 7} \approx \mathbf{n^{2.8074}}$$

---

### Exact Count of Total Arithmetic Operations: $7n^{\log_2 7} - 6n^2$ (Slip Test 4 Bonus)

> **Bonus Question [1 Mark]:**  
> *"What is the total number of arithmetic operations $(\times, +, -)$ performed by Strassen’s algorithm to multiply two matrices $A$ and $B$ of size $n \times n$ each? Explain."*

#### 1. Multiplications Count $M(n)$:
At base case $n = 1$, $M(1) = 1$.  
At each level, we do 7 recursive matrix multiplications of half the dimension:
$$M(n) = 7 M(n/2) \implies \mathbf{M(n) = n^{\log_2 7}}$$

#### 2. Additions and Subtractions Count $A(n)$:
- **Base case:** For $1 \times 1$ matrices ($n = 1$), $A(1) = 0$ (no additions needed).
- **Recursive step:** Adding or subtracting two $(n/2) \times (n/2)$ matrices requires $(n/2)^2 = \frac{n^2}{4}$ operations.
  - Computing the inputs for $m_1 \dots m_7$ requires **10 submatrix additions/subtractions**:
    - $a_{11} + a_{22}$, $b_{11} + b_{22}$ (2 for $m_1$)
    - $a_{21} + a_{22}$ (1 for $m_2$)
    - $b_{12} - b_{22}$ (1 for $m_3$)
    - $b_{21} - b_{11}$ (1 for $m_4$)
    - $a_{11} + a_{12}$ (1 for $m_5$)
    - $a_{21} - a_{11}$, $b_{11} + b_{12}$ (2 for $m_6$)
    - $a_{12} - a_{22}$, $b_{21} + b_{22}$ (2 for $m_7$)
  - Combining $m_1 \dots m_7$ to produce $c_{11}, c_{12}, c_{21}, c_{22}$ requires **8 submatrix additions/subtractions**:
    - $c_{11}$: 3 operations ($m_1 + m_4 - m_5 + m_7$)
    - $c_{12}$: 1 operation ($m_3 + m_5$)
    - $c_{21}$: 1 operation ($m_2 + m_4$)
    - $c_{22}$: 3 operations ($m_1 - m_2 + m_3 + m_6$)
  - **Total submatrix additions/subtractions per step:** $10 + 8 = 18$.
  - Since each operates on $(n/2) \times (n/2)$ elements:
    $$\text{Additions per step} = 18 \times \left(\frac{n}{2}\right)^2 = \frac{18}{4} n^2 = \frac{9}{2} n^2$$

#### 3. Solving the Addition Recurrence:
$$A(n) = 7 A(n/2) + \frac{9}{2} n^2, \quad A(1) = 0$$

Let $n = 2^k$. The recurrence becomes:
$$A(2^k) = 7 A(2^{k-1}) + 18 \cdot 4^{k-1}$$
Unrolling the recurrence:
$$A(2^k) = 18 \sum_{j=0}^{k-1} 7^j \cdot 4^{k-1-j} = 18 \cdot 4^{k-1} \sum_{j=0}^{k-1} \left(\frac{7}{4}\right)^j$$
Using the geometric series formula $\sum_{j=0}^{k-1} r^j = \frac{r^k - 1}{r - 1}$ with $r = \frac{7}{4}$:
$$\sum_{j=0}^{k-1} \left(\frac{7}{4}\right)^j = \frac{\left(\frac{7}{4}\right)^k - 1}{\frac{7}{4} - 1} = \frac{\left(\frac{7}{4}\right)^k - 1}{\frac{3}{4}} = \frac{4}{3} \left[ \left(\frac{7}{4}\right)^k - 1 \right]$$
Substitute back:
$$A(2^k) = 18 \cdot 4^{k-1} \cdot \frac{4}{3} \left[ \frac{7^k}{4^k} - 1 \right] = 18 \cdot \frac{4^k}{4} \cdot \frac{4}{3} \left[ \frac{7^k - 4^k}{4^k} \right] = 6 (7^k - 4^k)$$
Since $7^k = n^{\log_2 7}$ and $4^k = (2^k)^2 = n^2$:
$$\mathbf{A(n) = 6 n^{\log_2 7} - 6 n^2}$$

#### 4. Total Arithmetic Operations:
$$\text{Total Ops}(n) = M(n) + A(n) = n^{\log_2 7} + \big(6 n^{\log_2 7} - 6 n^2\big) = \mathbf{7 n^{\log_2 7} - 6 n^2}$$

- **Verification for $n = 2$ ($k = 1$):**
  $$\text{Total Ops}(2) = 7(2^{\log_2 7}) - 6(2^2) = 7(7) - 6(4) = 49 - 24 = \mathbf{25}$$
  Matches exactly: 7 multiplications $+ 18$ additions/subtractions $= 25$ operations!

---

### Practical Considerations: Crossover Threshold & Numerical Stability
1. **The Crossover Threshold:** Strassen does 18 matrix additions/subtractions in each step, which adds overhead. In practice, Strassen is faster than standard multiplication only when $n \ge 64$ to $128$. For submatrices smaller than this threshold, libraries fall back to standard 3-loop multiplication.
2. **Numerical Stability:** Strassen is slightly less numerically stable than standard multiplication due to intermediate additions and subtractions causing loss of precision with floating-point numbers.

---

## 4. The Theoretical Frontier of Matrix Multiplication & AlphaTensor

---

### DeepMind AlphaTensor: $4 \times 4$ Binary Matrices (Slip Test 4, Puzzle 3)

> **Exam Question (Slip Test 4, Puzzle 3):**  
> *"In October 2022, Google DeepMind’s AI AlphaTensor presented an algorithm that multiplies two $4 \times 4$ binary matrices using just $47$ multiplications.*  
> *Write and explain a recurrence for $T(n)$ in terms of $T(n/4)$ for binary matrix multiplication. Solve it. (Take $\log_4 47 \approx 2.7773$.)"*

#### 1. Why is 47 Multiplications a Breakthrough?
- Two $n \times n$ matrices can be split into **16 submatrices of size $(n/4) \times (n/4)$** each.
- **Conventional algorithm** requires $4^3 = \mathbf{64}$ multiplications.
- Applying Strassen's algorithm twice (since $4 = 2 \times 2$) requires $7 \times 7 = \mathbf{49}$ multiplications.
- Google DeepMind's **AlphaTensor** formulated matrix multiplication as a 3D tensor game and used deep reinforcement learning (AlphaZero) to discover a scheme requiring only **47 multiplications** over $\mathbb{Z}_2$!

#### 2. The Recurrence Relation:
Let $T(n)$ denote the number of multiplications required to multiply two $n \times n$ binary matrices.  
Dividing the matrices into sixteen $(n/4) \times (n/4)$ blocks:
$$T(n) = 47 T(n/4), \quad T(1) = 1$$

#### 3. Solving the Recurrence:
- Subproblems: $a = 47$
- Subproblem size reduction factor: $b = 4$
- Critical exponent:
  $$\log_b a = \log_4 47 = \frac{\ln 47}{\ln 4} \approx \frac{3.85015}{1.38629} \approx \mathbf{2.7773}$$
- Including the $\Theta(n^2)$ matrix additions, Case 1 of the Master Theorem applies:
  $$T(n) = \mathbf{\Theta\left(n^{\log_4 47}\right) \approx \Theta\left(n^{2.7773}\right)}$$

#### Comparison of Exponents:
$$\text{Standard: } O(n^3) \quad > \quad \text{Strassen: } O(n^{2.8074}) \quad > \quad \text{AlphaTensor: } \mathbf{O(n^{2.7773})}$$
AlphaTensor established the fastest known practical algorithm for $4 \times 4$ matrices!

---

### The Timeline of Exponent $\omega$

The asymptotic exponent of matrix multiplication is denoted by $\omega$:
$$T(n) = O(n^\omega)$$

- Standard: $\omega = 3.0$
- Strassen (1969): $\omega \approx 2.807$
- AlphaTensor (2022, $4 \times 4$ binary): $\omega \approx 2.7773$
- Coppersmith–Winograd (1990): $\omega \approx 2.3755$
- Duan, Wu, Zhou (2023): $\omega \approx \mathbf{2.37155}$
- **Conjectured Lower Bound:** $\omega = 2$ (since writing down the $n^2$ output elements requires $\Omega(n^2)$ operations).

---

## 5. Matrix Chain Multiplication (MCM): Dynamic Programming

### Why Order of Multiplication Matters (The 10x Cost Explosion)
Matrix multiplication is **associative**:
$$(A \times B) \times C = A \times (B \times C)$$
However, the **number of scalar multiplications varies drastically** depending on where you put the parentheses!

#### Concrete Example:
Consider 3 matrices:
- $A_1$ of size $10 \times 100$
- $A_2$ of size $100 \times 5$
- $A_3$ of size $5 \times 50$

**Order 1: $(A_1 \times A_2) \times A_3$**
1. Multiply $A_1 \times A_2$: Cost $= 10 \times 100 \times 5 = 5,000$ operations. Result is $10 \times 5$.
2. Multiply $(A_1 A_2) \times A_3$: Cost $= 10 \times 5 \times 50 = 2,500$ operations.
- **Total Cost:** $5,000 + 2,500 = \mathbf{7,500 \text{ operations}}$.

**Order 2: $A_1 \times (A_2 \times A_3)$**
1. Multiply $A_2 \times A_3$: Cost $= 100 \times 5 \times 50 = 25,000$ operations. Result is $100 \times 50$.
2. Multiply $A_1 \times (A_2 A_3)$: Cost $= 10 \times 100 \times 50 = 50,000$ operations.
- **Total Cost:** $25,000 + 50,000 = \mathbf{75,000 \text{ operations}}$!

**Order 1 is 10 TIMES FASTER than Order 2!**

---

### Why Brute Force Fails (The Catalan Number Explosion)
The number of ways to parenthesize a sequence of $n$ matrices is given by the **Catalan number** $C_{n-1}$:
$$C_{n-1} = \frac{1}{n} \binom{2n - 2}{n - 1} = \mathbf{\Omega\left(\frac{4^n}{n^{3/2}}\right)}$$
This grows exponentially! For $n = 30$, there are over $10^{15}$ parenthesizations. Brute force is completely impossible.

---

### Optimal Substructure & Recurrence Relation
Let matrix $A_i$ have dimensions $p_{i-1} \times p_i$ for $i = 1, \dots, n$.  
Let $m[i, j]$ be the **minimum number of scalar multiplications** needed to compute the product $A_i \dots A_j$.

To compute $A_i \dots A_j$, we must split the product at some index $k$ ($i \le k < j$):
$$(A_i \dots A_k) \times (A_{k+1} \dots A_j)$$

- Cost of computing left product: $m[i, k]$
- Cost of computing right product: $m[k+1, j]$
- Cost of multiplying the two resulting matrices: $p_{i-1} \cdot p_k \cdot p_j$

#### The Recurrence:
$$m[i, j] = \begin{cases}
0 & \text{if } i = j \\
\min_{i \le k < j} \Big(m[i, k] + m[k+1, j] + p_{i-1} p_k p_j\Big) & \text{if } i < j
\end{cases}$$

---

### The Diagonal (Chain-Length) Table Filling Order
To compute $m[i, j]$, we need $m[i, k]$ and $m[k+1, j]$, which are **strictly shorter sub-chains** ($k - i < j - i$).  
Therefore, the DP table **MUST be filled in increasing order of chain length $L = j - i + 1$**:

```text
FOR L = 2 TO n:              // L is chain length
    FOR i = 1 TO n - L + 1:  // i is start index
        j = i + L - 1        // j is end index
        m[i, j] = INFINITY
        FOR k = i TO j - 1:  // k is split point
            q = m[i, k] + m[k+1, j] + p[i-1] * p[k] * p[j]
            IF q < m[i, j]:
                m[i, j] = q
                s[i, j] = k   // Record optimal split
```

- **Time Complexity:** $O(n^3)$ (3 nested loops).
- **Space Complexity:** $O(n^2)$ to store the $m$ and $s$ tables.

---

## 6. Complete Production-Grade C++ Implementation

```cpp
#include <iostream>
#include <vector>
#include <string>

using namespace std;

const long long INF = 1e18;

// Matrix Chain Multiplication Solver
class MatrixChainMultiplier {
private:
    int n;
    vector<int> p;
    vector<vector<long long>> m;
    vector<vector<int>> s;

    void printParenthesisHelper(int i, int j) {
        if (i == j) {
            cout << "A" << i;
            return;
        }
        cout << "(";
        printParenthesisHelper(i, s[i][j]);
        printParenthesisHelper(s[i][j] + 1, j);
        cout << ")";
    }

public:
    MatrixChainMultiplier(const vector<int>& dimensions) {
        p = dimensions;
        n = p.size() - 1; // Number of matrices
        m.assign(n + 1, vector<long long>(n + 1, 0));
        s.assign(n + 1, vector<int>(n + 1, 0));
    }

    long long solve() {
        // L is the chain length
        for (int L = 2; L <= n; ++L) {
            for (int i = 1; i <= n - L + 1; ++i) {
                int j = i + L - 1;
                m[i][j] = INF;

                for (int k = i; k < j; ++k) {
                    long long cost = m[i][k] + m[k + 1][j] + (long long)p[i - 1] * p[k] * p[j];
                    if (cost < m[i][j]) {
                        m[i][j] = cost;
                        s[i][j] = k;
                    }
                }
            }
        }
        return m[1][n];
    }

    void printOptimalParenthesization() {
        printParenthesisHelper(1, n);
        cout << endl;
    }
};

int main() {
    // Matrices: A1 (10x100), A2 (100x5), A3 (5x50)
    // Dimensions array p: [10, 100, 5, 50]
    vector<int> dims = {10, 100, 5, 50};
    MatrixChainMultiplier mcm(dims);

    cout << "Minimum scalar multiplications: " << mcm.solve() << endl;
    cout << "Optimal Parenthesization: ";
    mcm.printOptimalParenthesization();

    return 0;
}
```

---

## 7. Advanced Exam Reasoning Questions & Traps

### Trap 1: What if Matrix Dimensions are Not Powers of 2 in Strassen?
- **Padding:** Pad the matrices with extra rows and columns of zeros until dimension is a power of 2 ($2^{\lceil \log_2 n \rceil}$).
- Does padding ruin complexity?  
  No! The padded dimension $N < 2n$. Since $(2n)^{2.807} = 2^{2.807} n^{2.807} \approx 7 \cdot n^{2.807} = O(n^{2.807})$, the asymptotic complexity remains unchanged!

---

### Trap 2: MCM When All Matrices are Square ($n \times n$)
> **Question:** Suppose we have a chain of $k$ matrices, and **ALL of them are $n \times n$ square matrices**. Does the parenthesization order matter?

**Verdict:** **NO! Any parenthesization has the exact same cost!**  
- Multiplying any two $n \times n$ matrices costs $n \times n \times n = n^3$.
- In a chain of $k$ matrices, exactly $k - 1$ matrix multiplications must be performed.
- Total cost is always:
  $$(k - 1) \cdot n^3$$
- The order of parentheses has zero effect when all dimensions are identical!

---

## 8. Exam Day Summary Checklist

1. **Standard Matrix Multiplication $\implies \Theta(n^3)$.**
2. **Naive Divide-and-Conquer $\implies T(n) = 8T(n/2) + \Theta(n^2) = \Theta(n^3)$ (No gain!).**
3. **Strassen’s Algorithm $\implies 7$ multiplications $\implies T(n) = 7T(n/2) + \Theta(n^2) = \mathbf{\Theta(n^{\log_2 7}) \approx O(n^{2.807})}$.**
4. **Matrix Chain Multiplication (MCM) $\implies$ DP by chain length $L$; time $O(n^3)$, space $O(n^2)$.**
5. **Catalan Number $C_{n-1}$ proves why brute-force parenthesization is $\Omega(4^n / n^{3/2})$.**
6. **If all matrices are square $n \times n$, parenthesization cost is identical for all orders.**
