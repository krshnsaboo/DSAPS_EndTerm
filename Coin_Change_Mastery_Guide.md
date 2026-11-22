# Mastering the Coin Change Problem: From Intuition to Exam Mastery

> **Target Audience:** Students preparing for rigorous algorithms examinations (e.g., IIIT-H DSAPS, GATE, advanced dynamic programming).  
> **Prerequisites:** None assumed! Built from scratch covering the greedy vs DP paradigm, canonical coin systems, pseudo-polynomial complexity proofs, C++ code, and classic exam traps.

---

## Table of Contents
1. [The Two Fundamental Coin Change Problems](#1-the-two-fundamental-coin-change-problems)
   - [Problem 1: Minimum Coins to Make Amount $A$ (Optimization)](#problem-1-minimum-coins-to-make-amount-a-optimization)
   - [Problem 2: Number of Ways to Make Amount $A$ (Counting)](#problem-2-number-of-ways-to-make-amount-a-counting)
2. [When Does Greedy Work? (Canonical Coin Systems)](#2-when-does-greedy-work-canonical-coin-systems)
   - [The Greedy Strategy](#the-greedy-strategy)
   - [Classic Counterexamples Where Greedy Fails](#classic-counterexamples-where-greedy-fails)
   - [What Makes a Coin System Canonical? (Pearson's Test)](#what-makes-a-coin-system-canonical-pearsons-test)
3. [Slip Test 1 Deep Dive: Bounded Denominations & Complexity Analysis](#3-slip-test-1-deep-dive-bounded-denominations--complexity-analysis)
   - [Slip Test 1 (10 Aug 2026) - Puzzle 1: Amounts 45, 59, 1000](#slip-test-1-10-aug-2026---puzzle-1-amounts-45-59-1000)
   - [Slip Test 1 (10 Aug 2026) - Puzzle 2: General Bounded Algorithm & Complexity](#slip-test-1-10-aug-2026---puzzle-2-general-bounded-algorithm--complexity)
   - [Slip Test PYQ: Is $O(n \cdot A)$ a Polynomial-Time Algorithm?](#slip-test-pyq-is-on-cdot-a-a-polynomial-time-algorithm)
4. [Dynamic Programming Formulations & The Permutation vs Combination Trap](#4-dynamic-programming-formulations--the-permutation-vs-combination-trap)
   - [Min Coins Formulation ($O(n \cdot A)$)](#min-coins-formulation-on-cdot-a)
   - [The #1 Exam Trap: Combinations vs Permutations](#the-1-exam-trap-combinations-vs-permutations)
5. [Complete C++ Implementation](#5-complete-c-implementation)
6. [Advanced Exam Variations & Twisted Scenarios](#6-advanced-exam-variations--twisted-scenarios)
   - [Variation 1: Bounded Coin Change (Finite Counts)](#variation-1-bounded-coin-change-finite-counts)
   - [Variation 2: Negative Coin Values (Shortest Paths on Directed Graphs)](#variation-2-negative-coin-values-shortest-paths-on-directed-graphs)
   - [Variation 3: Can Greedy Ever Use More Than Double the Optimal Coins?](#variation-3-can-greedy-ever-use-more-than-double-the-optimal-coins)
7. [Curated Medium-to-Hard Problems (LeetCode & Codeforces)](#7-curated-medium-to-hard-problems-leetcode--codeforces)
   - [Problem 1: Coin Change Combinations vs Permutations (LeetCode 518 & 377)](#problem-1-coin-change-combinations-vs-permutations-leetcode-518--377)
   - [Problem 2: Cut Ribbon: Maximize Pieces (Codeforces 189A - Medium)](#problem-2-cut-ribbon-maximize-pieces-codeforces-189a---medium)
   - [Problem 3: Form Largest Integer With Digits (LeetCode 1449 - Hard)](#problem-3-form-largest-integer-with-digits-leetcode-1449---hard)
8. [Exam Day Summary Checklist](#8-exam-day-summary-checklist)

---

## 1. The Two Fundamental Coin Change Problems

Suppose you are given an array of $n$ coin denominations $C = \{c_1, c_2, \dots, c_n\}$ (typically including $1$ so any integer amount can be formed), and a target amount $A$.

### Problem 1: Minimum Coins to Make Amount $A$ (Optimization)
- **Goal:** Find the minimum number of coins needed to sum to $A$.
- **Formulation:**
  $$\min \sum_{i=1}^n x_i \quad \text{subject to} \quad \sum_{i=1}^n c_i x_i = A, \quad x_i \ge 0 \ (x_i \in \mathbb{Z})$$

### Problem 2: Number of Ways to Make Amount $A$ (Counting)
- **Goal:** Find the total number of distinct combinations of coins that sum to $A$.
- **Formulation:** Count the number of non-negative integer vectors $(x_1, \dots, x_n)$ satisfying $\sum c_i x_i = A$.

---

## 2. When Does Greedy Work? (Canonical Coin Systems)

### The Greedy Strategy
Start with the largest coin denomination $\le A$. Take as many as possible, reduce the remaining amount, and repeat with the next smaller denomination.

```text
While A > 0:
    Find largest coin c <= A
    Take floor(A / c) coins of denomination c
    A = A % c
```

---

### Classic Counterexamples Where Greedy Fails

Real-world currencies (like US coins: $\{1, 5, 10, 25\}$ or Indian coins: $\{1, 2, 5, 10, 20\}$) are specially designed so greedy **always works**. But in general, greedy fails miserably!

#### Counterexample 1: The $\{1, 3, 4\}$ System for Amount 6
- **Target Amount:** $A = 6$.
- **Greedy Execution:**
  - Largest coin $\le 6$ is **$4$**. Takes one coin of $4$. Remaining amount: $6 - 4 = 2$.
  - Next largest coin $\le 2$ is **$1$**. Takes two coins of $1$.
  - **Greedy Solution:** $\{4, 1, 1\} \implies \mathbf{3 \text{ coins}}$.
- **Optimal Solution:**
  - Take two coins of **$3$**: $3 + 3 = 6 \implies \mathbf{2 \text{ coins}}$!
- **Greedy is strictly suboptimal!**

#### Counterexample 2: The $\{1, 6, 10\}$ System for Amount 12
- **Target Amount:** $A = 12$.
- **Greedy Execution:**
  - Largest coin $\le 12$ is **$10$**. Takes one coin of $10$. Remaining: $2$.
  - Takes two coins of $1$.
  - **Greedy Solution:** $\{10, 1, 1\} \implies \mathbf{3 \text{ coins}}$.
- **Optimal Solution:**
  - Take two coins of **$6$**: $6 + 6 = 12 \implies \mathbf{2 \text{ coins}}$!

---

### What Makes a Coin System Canonical? (Pearson's Test)
A coin system is called **canonical** if the greedy algorithm yields the optimal number of coins for **every possible target amount $A$**.

- David Pearson (1994) proved an $O(n^3)$ algorithm to verify if a coin system is canonical.
- **The Core Insight:** A coin system fails to be canonical if and only if there exists a counterexample amount $A$ in a strictly bounded range between $c_3$ and $c_n + c_{n-1}$. If greedy works for all amounts up to $c_n + c_{n-1}$, it works for all integers up to infinity!

---

## 3. Slip Test 1 Deep Dive: Bounded Denominations & Complexity Analysis

---

### Slip Test 1 (10 Aug 2026) - Puzzle 1: Amounts 45, 59, 1000

> **Problem Statement:**  
> Suppose you are given coins of denomination $1, 6, 8,$ and $13$.  
> Denominations $1, 6,$ and $8$ are available in **unlimited** quantities, but denomination $13$ is available only in a **maximum quantity of 2** (i.e., at most two $13$-coins).  
> What is the minimum number of coins needed to make:  
> **(a) 45**  
> **(b) 59**  
> **(c) 1000**

#### Core Intuition & Methodology
Because $13$ is the only bounded denomination and can only be used $0, 1,$ or $2$ times, we evaluate each candidate count $m \in \{0, 1, 2\}$ for the coin $13$:
$$\text{Min Coins}(A) = \min_{m \in \{0, 1, 2\}, 13m \le A} \Big( m + \text{UnboundedMinCoins}_{\{1, 6, 8\}}(A - 13m) \Big)$$

For the unbounded coin set $\{1, 6, 8\}$, notice:
- $8$ is the largest greedy choice, but $6$ helps bridge remainders without dropping all the way to $1$.
- Any remainder modulo $8$ can be formed efficiently:
  - Remainder $0$: multiples of $8$.
  - Remainder $6$: one $6$ coin.
  - Remainder $4$: $8 \times 0 + 6 \times 0 + 4 \times 1$ (or $6 \times 2 = 12 \equiv 4 \pmod 8$ using $12 - 8 = 4$).
  - Two $6$s sum to $12 = 8 + 4$. Three $6$s sum to $18 = 8 \times 2 + 2$.

---

#### Detailed Solution for (a) $A = 45$:
1. **Case $m = 0$ (No 13s used):**
   - Amount needed: $45$ using $\{1, 6, 8\}$.
   - $45 = 5 \times 8 + 5 \times 1 \implies 10$ coins.
   - Or $45 = 4 \times 8 + 2 \times 6 + 1 \times 1 = 32 + 12 + 1 = 45 \implies 4 + 2 + 1 = 7$ coins.
2. **Case $m = 1$ (One 13 used):**
   - Remainder: $45 - 13 = 32$.
   - $32$ is an exact multiple of $8$: $32 = 4 \times 8$.
   - Coins used: $1$ (of 13) $+ 4$ (of 8) = **5 coins**.
   - Breakdown: $\{13, 8, 8, 8, 8\}$ (Sum: $13 + 32 = 45$).
3. **Case $m = 2$ (Two 13s used):**
   - Remainder: $45 - 26 = 19$.
   - Using $\{1, 6, 8\}$: $19 = 2 \times 8 + 3 \times 1 \implies 5$ coins, or $1 \times 8 + 1 \times 6 + 5 \times 1 \implies 7$ coins.
   - Total coins: $2 + 5 = 7$ coins.

**Answer for (a):** Minimum coins = **5** ($\mathbf{1 \times 13 + 4 \times 8}$).

---

#### Detailed Solution for (b) $A = 59$:
1. **Case $m = 0$ (No 13s used):**
   - Remainder: $59$.
   - $59 = 7 \times 8 + 3 \times 1 \implies 10$ coins.
   - $59 = 5 \times 8 + 3 \times 6 + 1 \times 1 = 40 + 18 + 1 = 59 \implies 5 + 3 + 1 = 9$ coins.
2. **Case $m = 1$ (One 13 used):**
   - Remainder: $59 - 13 = 46$.
   - Best combination for $46$ using $\{1, 6, 8\}$:
     $$46 = 5 \times 8 + 1 \times 6 = 40 + 6 = 46 \implies 6 \text{ coins}$$
   - Total coins: $1$ (of 13) $+ 6 = \mathbf{7 \text{ coins}}$.
   - Breakdown: $\{13, 8, 8, 8, 8, 8, 6\}$.
3. **Case $m = 2$ (Two 13s used):**
   - Remainder: $59 - 26 = 33$.
   - Best combination for $33$:
     $$33 = 4 \times 8 + 1 \times 1 = 32 + 1 = 33 \implies 5 \text{ coins}$$
   - Total coins: $2$ (of 13) $+ 5 = \mathbf{7 \text{ coins}}$.
   - Breakdown: $\{13, 13, 8, 8, 8, 8, 1\}$.

**Answer for (b):** Minimum coins = **7** ($\mathbf{1 \times 13 + 5 \times 8 + 1 \times 6}$ or $\mathbf{2 \times 13 + 4 \times 8 + 1 \times 1}$).

---

#### Detailed Solution for (c) $A = 1000$:
For large amounts, we maximize the usage of the largest available denominations:
1. **Case $m = 0$:**
   - $1000 = 125 \times 8 \implies 125$ coins.
2. **Case $m = 1$:**
   - Remainder: $1000 - 13 = 987$.
   - $987 = 123 \times 8 + 3 \times 1 \implies 123 + 3 = 126$ coins.
   - Or $987 = 122 \times 8 + 1 \times 6 + 5 \times 1 = 976 + 6 + 5 \implies 128$ coins.
   - (With 1 coin of 13, total is at least $1 + 124 = 125$ coins).
3. **Case $m = 2$ (Two 13s used):**
   - Total from two 13s: $2 \times 13 = 26$.
   - Remainder: $1000 - 26 = 974$.
   - Divide $974$ by $8$:
     $$974 = 121 \times 8 + 6$$
   - Remainder is exactly $6$, which matches the denomination $6$!
   - Thus, $974$ requires only $121$ coins of $8$ and $1$ coin of $6$, giving $121 + 1 = 122$ coins!
   - Adding the two $13$s:
     $$\text{Total coins} = 2 + 121 + 1 = \mathbf{124 \text{ coins}}$$

**Answer for (c):** Minimum coins = **124** ($\mathbf{2 \times 13 + 121 \times 8 + 1 \times 6}$).

---

### Slip Test 1 (10 Aug 2026) - Puzzle 2: General Bounded Algorithm & Complexity

> **Problem Statement:**  
> Write an algorithm for the general problem where there are $n$ coin denominations $c_1, c_2, \dots, c_n$, where denominations $c_1, c_2, \dots, c_{n-1}$ are available in **unlimited** quantities, but denomination $c_n$ is available in **at most $k$** quantity, to make an amount $A$. What is its time complexity?

#### Algorithmic Design: Two-Stage DP Approach

Rather than maintaining a full 2D state space over all combinations, we exploit the structure:
1. Only $c_n$ has a capacity constraint $k$.
2. The remaining denominations $c_1, \dots, c_{n-1}$ are standard unbounded coins.

```
Algorithm BoundedCoinChange(C[1..n], k, A):
1. Initialize an array DP[0..A] with infinity
2. DP[0] = 0
3. // Step 1: Unbounded DP using first n-1 denominations
4. For i = 1 to n - 1:
5.     coin = C[i]
6.     For x = coin to A:
7.         If DP[x - coin] != infinity:
8.             DP[x] = min(DP[x], DP[x - coin] + 1)
9.
10. // Step 2: Try each valid number of copies of c_n from 0 to k
11. min_total_coins = infinity
12. For m = 0 to min(k, floor(A / C[n])):
13.     rem = A - m * C[n]
14.     If DP[rem] != infinity:
15.         min_total_coins = min(min_total_coins, m + DP[rem])
16.
17. Return min_total_coins
```

#### C++ Implementation
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

const int INF = 1e9;

int minCoinsBoundedLast(const vector<int>& c, int k, int A) {
    int n = c.size();
    // DP array for unbounded coins c[0 ... n-2]
    vector<int> dp(A + 1, INF);
    dp[0] = 0;

    // Stage 1: Fill unbounded DP for first n - 1 coins
    for (int i = 0; i < n - 1; ++i) {
        for (int x = c[i]; x <= A; ++x) {
            if (dp[x - c[i]] != INF) {
                dp[x] = min(dp[x], dp[x - c[i]] + 1);
            }
        }
    }

    // Stage 2: Enumerate m copies of the bounded coin c[n-1]
    int best = INF;
    int max_m = min(k, A / c[n - 1]);
    for (int m = 0; m <= max_m; ++m) {
        int rem = A - m * c[n - 1];
        if (dp[rem] != INF) {
            best = min(best, m + dp[rem]);
        }
    }

    return (best >= INF) ? -1 : best;
}
```

#### Exact Complexity Analysis:
- **Time Complexity:**
  - Stage 1 (Unbounded DP on $n-1$ coins): $(n - 1)$ iterations over an array of size $A$, taking $O((n - 1) \cdot A)$ operations.
  - Stage 2 (Enumerate $m \in \{0, \dots, \min(k, \lfloor A / c_n \rfloor)\}$): takes $O(\min(k, A / c_n))$ operations.
  - **Total Time Complexity:**
    $$O\Big(n \cdot A + \min\big(k, \frac{A}{c_n}\big)\Big) = \mathbf{O(n \cdot A + k)}$$
  - Notice that if $k$ is small (like $k = 2$ in Puzzle 1), Stage 2 runs in $O(1)$, so the time complexity is strictly $O(n \cdot A)$!
- **Space Complexity:**
  - We only store a 1D array of size $A + 1$.
  - **Total Space Complexity:** $\mathbf{O(A)}$.

---

### Slip Test PYQ: Is $O(n \cdot A)$ a Polynomial-Time Algorithm?

> **Exam Question (Slip Test 2, Puzzle 4):**  
> *"In the coin denominations problem, if there are $n$ different denominations, and the total amount is $A$, then the running time of the algorithm which we studied in class is $O(n \cdot A)$. Is this a polynomial-time algorithm?"*  
> **A. YES**  
> **B. NO**

**Verdict:** **B. NO! It is a PSEUDO-POLYNOMIAL time algorithm.**

#### The Exact Mathematical Proof:
1. In theoretical computer science, an algorithm runs in **polynomial time** if and only if its running time is bounded by a polynomial function of the **size of the input in bits** (denoted as $L$).
2. **What constitutes the input?**
   - The list of $n$ coin values: $C = \{c_1, \dots, c_n\}$.
   - The target integer amount: $A$.
3. **How many bits are needed to encode $A$?**
   - Writing down an integer $A$ in standard binary requires:
     $$L = \lceil \log_2(A + 1) \rceil \text{ bits}$$
4. **What is the numerical magnitude of $A$ in terms of input length $L$?**
   $$A \approx 2^L$$
5. **Substitute $A = 2^L$ into the running time:**
   $$\text{Running Time} = O(n \cdot A) = O(n \cdot 2^L)$$
6. The running time is **exponential** in the number of bits required to represent $A$!
7. Therefore, the algorithm is **pseudo-polynomial**, not truly polynomial.
   - *Exam Distinction:* If $A$ were provided in **unary encoding** (e.g., $A$ tally marks `111...1`), the input size would be $L = A$ bits, and in that representation alone, the algorithm would be formally polynomial. But under standard binary encoding, it is strictly pseudo-polynomial!

---

## 4. Dynamic Programming Formulations & The Permutation vs Combination Trap

### Min Coins Formulation ($O(n \cdot A)$)
Let $dp[a]$ be the minimum number of coins needed to make amount $a$.
- **Base Case:** $dp[0] = 0$, and $dp[a] = \infty$ for all $a > 0$.
- **Recurrence:**
  $$dp[a] = \min_{\substack{c \in C \\ c \le a}} \Big(dp[a - c] + 1\Big)$$

---

### The #1 Exam Trap: Combinations vs Permutations

Suppose $C = \{1, 2\}$ and target $A = 3$.
- Possible ordered sequences: $(1, 1, 1), (1, 2), (2, 1)$.
- If order **does NOT matter** (Combinations): $2$ ways ($\{1, 1, 1\}$ and $\{1, 2\}$).
- If order **DOES matter** (Permutations): $3$ ways.

#### The Golden Rule of Nested Loops:

```cpp
// ==========================================
// 1. COMBINATIONS (Coins in Outer Loop)
// Order DOES NOT matter: {1, 2} is same as {2, 1}
// ==========================================
vector<int> dp(A + 1, 0);
dp[0] = 1;
for (int coin : coins) {          // COINS OUTSIDE
    for (int a = coin; a <= A; ++a) {
        dp[a] += dp[a - coin];
    }
}
```

```cpp
// ==========================================
// 2. PERMUTATIONS (Amount in Outer Loop)
// Order DOES matter: (1, 2) and (2, 1) are distinct!
// ==========================================
vector<int> dp(A + 1, 0);
dp[0] = 1;
for (int a = 1; a <= A; ++a) {    // AMOUNT OUTSIDE
    for (int coin : coins) {
        if (a >= coin) {
            dp[a] += dp[a - coin];
        }
    }
}
```

> **Why does this happen?**  
> - When the **coin loop is on the outside**, you process all contributions of coin 1 first, then coin 2, then coin 5. You can never add a coin 1 after a coin 2! The coins in the combination are forced to appear in non-decreasing order, preventing duplicate permutations.  
> - When the **amount loop is on the outside**, at every amount $a$, any coin can be placed as the final step, counting all permutations.

---

## 5. Complete C++ Implementation

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

const int INF = 1e9;

// 1. Minimum Coins to make Amount A
int minCoins(const vector<int>& coins, int A) {
    vector<int> dp(A + 1, INF);
    dp[0] = 0;

    for (int a = 1; a <= A; ++a) {
        for (int coin : coins) {
            if (a >= coin && dp[a - coin] != INF) {
                dp[a] = min(dp[a], dp[a - coin] + 1);
            }
        }
    }
    return (dp[A] == INF) ? -1 : dp[A];
}

// 2. Total COMBINATIONS (Distinct Sets of Coins)
int countCombinations(const vector<int>& coins, int A) {
    vector<int> dp(A + 1, 0);
    dp[0] = 1;

    for (int coin : coins) {
        for (int a = coin; a <= A; ++a) {
            dp[a] += dp[a - coin];
        }
    }
    return dp[A];
}

// 3. Reconstruct the Minimum Coins Used
vector<int> reconstructCoins(const vector<int>& coins, int A) {
    vector<int> dp(A + 1, INF);
    vector<int> parent(A + 1, -1);
    dp[0] = 0;

    for (int a = 1; a <= A; ++a) {
        for (int coin : coins) {
            if (a >= coin && dp[a - coin] != INF) {
                if (dp[a - coin] + 1 < dp[a]) {
                    dp[a] = dp[a - coin] + 1;
                    parent[a] = coin;
                }
            }
        }
    }

    if (dp[A] == INF) return {};

    vector<int> result;
    int curr = A;
    while (curr > 0) {
        result.push_back(parent[curr]);
        curr -= parent[curr];
    }
    return result;
}
```

---

## 6. Advanced Exam Variations & Twisted Scenarios

### Variation 1: Bounded Coin Change (Finite Counts)
Suppose coin $c_i$ is available in limited quantity $k_i$.
- This becomes **Bounded Knapsack**!
- Use binary splitting of counts: decompose $k_i$ into powers of two ($1, 2, 4, \dots, R$).
- Reduces the number of items from $\sum k_i$ to $\sum \log_2(k_i)$, running in $O(A \sum \log k_i)$ time.

### Variation 2: Negative Coin Values (Shortest Paths on Directed Graphs)
Suppose a "coin" can give you change or take change away (positive and negative denominations).
- The state transition graph can contain directed cycles!
- If a negative-weight cycle exists, you can make infinite value.
- Solved using **Bellman-Ford / Shortest Path Faster Algorithm (SPFA)** instead of standard DP!

---

## 7. Curated Medium-to-Hard Problems (LeetCode & Codeforces)

Exam questions frequently disguise Coin Change under ribbons, digit construction, and permutation counting.

---

### Problem 1: Coin Change Combinations vs Permutations (LeetCode 518 & 377)

#### LeetCode 518: Coin Change II (Medium - Combinations)
- **Goal:** Return the number of combinations that make up that amount. Order **does not matter** (e.g., $1+2$ is the same as $2+1$).
- **Rule:** Put the **COINS in the outer loop**.

```cpp
#include <vector>

using namespace std;

class Solution518 {
public:
    int change(int amount, vector<int>& coins) {
        // dp[a] = number of combinations to make amount a
        vector<unsigned long long> dp(amount + 1, 0);
        dp[0] = 1;

        // OUTER LOOP: Coins -> Guarantees combinations (no permutation duplicates)
        for (int coin : coins) {
            for (int a = coin; a <= amount; ++a) {
                dp[a] += dp[a - coin];
            }
        }
        return (int)dp[amount];
    }
};
```

#### LeetCode 377: Combination Sum IV (Medium - Permutations)
- **Goal:** Given an array of distinct integers `nums` and a target integer `target`, return the number of possible combinations. **The Catch:** Different sequences are counted as different combinations (e.g., $(1, 2)$ and $(2, 1)$ are distinct).
- **Rule:** This is **Permutations**! Put the **AMOUNT in the outer loop**.

```cpp
#include <vector>

using namespace std;

class Solution377 {
public:
    int combinationSum4(vector<int>& nums, int target) {
        // Use unsigned long long to prevent 32-bit integer overflow before final cast
        vector<unsigned long long> dp(target + 1, 0);
        dp[0] = 1;

        // OUTER LOOP: Amount -> Allows any coin to be placed at the current step
        for (int a = 1; a <= target; ++a) {
            for (int num : nums) {
                if (a >= num) {
                    dp[a] += dp[a - num];
                }
            }
        }
        return (int)dp[target];
    }
};
```

---

### Problem 2: Cut Ribbon: Maximize Pieces (Codeforces 189A - Medium)

#### Problem Statement:
Polycarp has a ribbon of length $n$ ($1 \le n \le 4000$). He wants to cut the ribbon into the **maximum possible number of pieces**, such that the length of each piece is equal to $a$, $b$, or $c$.

#### Algorithmic Reduction:
- This is the **exact inverse of standard Coin Change**:
  - Coin denominations: $\{a, b, c\}$.
  - Target amount: $n$.
  - Instead of *minimizing* coins, we want to **maximize** coins!
- **State:** $dp[i] =$ maximum number of pieces of length summing to exactly $i$.
- **Base Case:** $dp[0] = 0$, all other $dp[i] = -\infty$.
- **Transition:**
  $$dp[i] = \max\Big(dp[i - a] + 1, \ dp[i - b] + 1, \ dp[i - c] + 1\Big)$$

#### C++ Solution:

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

const int NEG_INF = -1e9;

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    int n, a, b, c;
    if (!(cin >> n >> a >> b >> c)) return 0;

    vector<int> pieces = {a, b, c};
    vector<int> dp(n + 1, NEG_INF);
    dp[0] = 0; // 0 length requires 0 pieces

    // Unbounded coin change maximization
    for (int len = 1; len <= n; ++len) {
        for (int p : pieces) {
            if (len >= p && dp[len - p] != NEG_INF) {
                dp[len] = max(dp[len], dp[len - p] + 1);
            }
        }
    }

    cout << dp[n] << "\n";
    return 0;
}
```

---

### Problem 3: Form Largest Integer With Digits (LeetCode 1449 - Hard)

#### Problem Statement:
Given an integer array `cost` of size 9, where `cost[i]` is the cost of digit $(i+1)$, and an integer `target`. Return the **largest integer** you can form whose total cost equals `target`. If you cannot form any integer, return `"0"`.

#### Algorithmic Insights:
1. **Length Dominates Everything:** A number with more digits is *always* larger than a number with fewer digits (e.g., $111 > 99$).
   - Therefore, our primary objective is to **maximize the number of digits** (exact Unbounded Coin Change maximization!).
2. **Tie-Breaking via Greedy Digit Placement:** If two numbers have the exact same maximum number of digits, we want larger digits at the higher place values (towards the left).
   - Once we know the maximum length achievable at each cost ($dp[\text{cost}]$), we greedily iterate from digit $9$ down to digit $1$. If using digit $d$ leads to an optimal length of $dp[\text{rem\_cost}] - 1$, we pick digit $d$ immediately!

#### C++ Solution:

```cpp
#include <vector>
#include <string>
#include <algorithm>

using namespace std;

class Solution1449 {
public:
    string largestNumber(vector<int>& cost, int target) {
        // dp[c] = maximum number of digits that can be formed with cost exactly c
        vector<int> dp(target + 1, -1);
        dp[0] = 0;

        for (int c = 1; c <= target; ++c) {
            for (int d = 0; d < 9; ++d) {
                int digit_cost = cost[d];
                if (c >= digit_cost && dp[c - digit_cost] != -1) {
                    dp[c] = max(dp[c], dp[c - digit_cost] + 1);
                }
            }
        }

        // If target cannot be reached exactly
        if (dp[target] == -1) return "0";

        // Greedy reconstruction: Pick the largest digit possible from 9 down to 1
        string result = "";
        int curr_cost = target;

        while (curr_cost > 0) {
            for (int d = 8; d >= 0; --d) {
                int digit_cost = cost[d];
                if (curr_cost >= digit_cost && dp[curr_cost - digit_cost] == dp[curr_cost] - 1) {
                    result += to_string(d + 1);
                    curr_cost -= digit_cost;
                    break;
                }
            }
        }

        return result;
    }
};
```

---

## 8. Exam Day Summary Checklist

1. **Greedy works ONLY for canonical coin systems; fails on general sets (e.g., $\{1, 3, 4\}$ for 6).**
2. **Time complexity $O(n \cdot A)$ is PSEUDO-POLYNOMIAL** because $A$ takes $\log_2 A$ bits.
3. **Loop order dictates Combinations vs Permutations:**
   - `coin` outer, `amount` inner $\implies$ **Combinations** (distinct coin sets).
   - `amount` outer, `coin` inner $\implies$ **Permutations** (order matters).
