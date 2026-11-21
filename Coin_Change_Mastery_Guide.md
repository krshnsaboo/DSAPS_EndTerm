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
3. [Deep Analysis of the Slip Test PYQ (Puzzle 4)](#3-deep-analysis-of-the-slip-test-pyq-puzzle-4)
   - [Is $O(n \cdot A)$ a Polynomial-Time Algorithm?](#is-on-cdot-a-a-polynomial-time-algorithm)
   - [The Exact Proof of Pseudo-Polynomial Complexity](#the-exact-proof-of-pseudo-polynomial-complexity)
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

## 3. Deep Analysis of the Slip Test PYQ (Puzzle 4)

> **Exam Question (Slip Test 2, Puzzle 4):**  
> *"In the coin denominations problem, if there are $n$ different denominations, and the total amount is $A$, then the running time of the algorithm which we studied in class is $O(n \cdot A)$. Is this a polynomial-time algorithm?"*  
> **A. YES**  
> **B. NO**

**Verdict:** **B. NO! It is a PSEUDO-POLYNOMIAL time algorithm.**

### The Exact Proof:
1. In theoretical computer science, an algorithm is **polynomial** if its running time is bounded by a polynomial function of the **length of the input in bits**.
2. **What is the input to this problem?**
   - The array of $n$ coin denominations: $C = \{c_1, \dots, c_n\}$.
   - The integer target amount: $A$.
3. **How many bits does it take to write down $A$?**
   - Writing the number $A$ in binary requires:
     $$L = \lceil \log_2 A \rceil \text{ bits}$$
4. **What is the value of $A$ in terms of its bit-length $L$?**
   $$A \approx 2^L$$
5. **Substitute $A = 2^L$ into the running time:**
   $$\text{Running Time} = O(n \cdot A) = O(n \cdot 2^L)$$
6. Notice that $2^L$ is an **exponential function** of the input bit-length $L$!
7. Therefore, the algorithm's running time is **exponential in the input size**, making it **pseudo-polynomial**, not polynomial!

*(Note: If $A$ were written in unary, e.g., $A$ tally marks `111...1`, the input length would be $A$ bits, and only then would it be considered polynomial).*

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
