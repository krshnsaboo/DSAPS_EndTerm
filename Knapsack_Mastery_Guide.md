# Mastering the Knapsack Problem: From Foundations to Exam Dominance

> **Target Audience:** Students preparing for competitive, university (e.g., IIIT-H DSAPS), and advanced algorithmic exams.  
> **Prerequisites:** None assumed! Built from scratch with intuitive concepts, mathematical proofs, pseudo-polynomial time analysis, C++ code, and counterexamples.

---

## Table of Contents
1. [The Big Picture: Problem Formulation & Variations](#1-the-big-picture-problem-formulation--variations)
   - [0/1 Knapsack](#01-knapsack)
   - [Fractional Knapsack](#fractional-knapsack)
   - [Unbounded Knapsack](#unbounded-knapsack)
2. [The Greedy Dilemma: When Does Greedy Work, and When Does It Fail?](#2-the-greedy-dilemma-when-does-greedy-work-and-when-does-it-fail)
   - [Why Greedy by Value/Weight Ratio Works for Fractional Knapsack](#why-greedy-by-valueweight-ratio-works-for-fractional-knapsack)
   - [Why Greedy by Ratio Fails for 0/1 Knapsack (The Classic Gap Problem)](#why-greedy-by-ratio-fails-for-01-knapsack-the-classic-gap-problem)
3. [Deep Analysis of the PYQ Knapsack Puzzles](#3-deep-analysis-of-the-pyq-knapsack-puzzles)
   - [Puzzle 1: Monotonic Weights and Values (Does Greedy Work?)](#puzzle-1-monotonic-weights-and-values-does-greedy-work)
   - [Puzzle 2: All Items Share the Exact Same Value/Weight Ratio](#puzzle-2-all-items-share-the-exact-same-valueweight-ratio)
   - [Puzzle 3: Exponential Values ($v_{i+1} = 2 v_i$)](#puzzle-3-exponential-values-vi1--2-vi)
4. [Slip Test 3 Deep Dive: The Two Thieves / Multiple Knapsack Problem](#4-slip-test-3-deep-dive-the-two-thieves--multiple-knapsack-problem)
   - [Puzzle 1: Two Thieves ($W_1 = 15, W_2 = 17$, 7 Items)](#puzzle-1-two-thieves-w_1--15-w_2--17-7-items)
   - [Puzzle 2: General Two-Knapsack Algorithm & Complexity](#puzzle-2-general-two-knapsack-algorithm--complexity)
5. [Dynamic Programming Formulations & Optimizations](#5-dynamic-programming-formulations--optimizations)
   - [Standard DP: Weight as State ($O(n \cdot W)$)](#standard-dp-weight-as-state-on-cdot-w)
   - [1D Array Space Optimization (Why Reverse Traversal is Mandatory)](#1d-array-space-optimization-why-reverse-traversal-is-mandatory)
   - [Dual DP: Value as State ($O(n \cdot V_{\max})$ for Huge Capacities)](#dual-dp-value-as-state-on-cdot-v_max-for-huge-capacities)
6. [Complete C++ Implementation](#6-complete-c-implementation)
7. [Complexity & Complexity Class Analysis: Is Knapsack Polynomial?](#7-complexity--complexity-class-analysis-is-knapsack-polynomial)
   - [Pseudo-Polynomial vs True Polynomial Time](#pseudo-polynomial-vs-true-polynomial-time)
   - [Why 0/1 Knapsack is Weakly NP-Complete](#why-01-knapsack-is-weakly-np-complete)
8. [The Exam Reasoning Question Bank (Challenging Variations)](#8-the-exam-reasoning-question-bank-challenging-variations)
9. [Curated Medium-to-Hard Problems (LeetCode & Codeforces)](#9-curated-medium-to-hard-problems-leetcode--codeforces)
   - [Problem 1: Partition Equal Subset Sum (LeetCode 416 - Medium)](#problem-1-partition-equal-subset-sum-leetcode-416---medium)
   - [Problem 2: Knapsack 2 (AtCoder DP Contest / CF - Medium-Hard)](#problem-2-knapsack-2-atcoder-dp-contest--cf---medium-hard)
   - [Problem 3: Fire! Knapsack with Deadlines (Codeforces 864E - Hard)](#problem-3-fire-knapsack-with-deadlines-codeforces-864e---hard)
   - [Problem 4: Ones and Zeroes: 2D Knapsack (LeetCode 474 - Medium-Hard)](#problem-4-ones-and-zeroes-2d-knapsack-leetcode-474---medium-hard)
10. [Summary Checklist for Exam Day](#10-summary-checklist-for-exam-day)

---

## 1. The Big Picture: Problem Formulation & Variations

Suppose a thief breaks into a jewelry store with a knapsack of maximum weight capacity $W$. There are $n$ items available. Each item $i$ has:
- A weight $w_i > 0$
- A value (profit) $v_i > 0$

The goal is to select items to pack into the knapsack to **maximize total value** without exceeding capacity $W$.

### 0/1 Knapsack
- **Constraint:** Each item can either be taken completely ($x_i = 1$) or left behind ($x_i = 0$). You cannot take fractions, and you cannot take an item multiple times.
- **Formulation:**
  $$\max \sum_{i=1}^n v_i x_i \quad \text{subject to} \quad \sum_{i=1}^n w_i x_i \le W, \quad x_i \in \{0, 1\}$$
- **Complexity:** NP-Complete (weakly). Solvable in pseudo-polynomial time via Dynamic Programming.

### Fractional Knapsack
- **Constraint:** Items are divisible (like gold dust or grain). You can take any fraction $x_i \in [0, 1]$ of item $i$.
- **Complexity:** $O(n \log n)$ solvable greedily by sorting by value-to-weight ratio $r_i = v_i / w_i$.

### Unbounded Knapsack
- **Constraint:** An unlimited number of copies of each item are available ($x_i \in \{0, 1, 2, \dots\}$).
- **Complexity:** $O(nW)$ using forward 1D DP.

---

## 2. The Greedy Dilemma: When Does Greedy Work, and When Does It Fail?

### Why Greedy by Value/Weight Ratio Works for Fractional Knapsack
In Fractional Knapsack, every unit of weight you put into the knapsack should yield the maximum possible value.
- **Proof Sketch:** Suppose an optimal solution does not take the item with the highest ratio $r_{\max} = v_{\max} / w_{\max}$ to its fullest extent, but includes some other item with ratio $r_k < r_{\max}$.
- We can replace $\epsilon > 0$ weight of item $k$ with $\epsilon$ weight of the maximal item.
- The net change in total value is $\epsilon(r_{\max} - r_k) > 0$.
- This strictly increases total value without altering total weight, contradicting the optimality of the original choice. Thus, greedy is strictly optimal for fractional knapsack.

---

### Why Greedy by Ratio Fails for 0/1 Knapsack (The Classic Gap Problem)
In 0/1 Knapsack, taking an item with a high ratio might leave **unusable empty capacity** that prevents you from packing other valuable items.

#### Minimal Counterexample:
- Capacity $W = 50$.
- Item 1: $w_1 = 10, v_1 = 60 \implies \text{Ratio } r_1 = 6.0$.
- Item 2: $w_2 = 20, v_2 = 100 \implies \text{Ratio } r_2 = 5.0$.
- Item 3: $w_3 = 30, v_3 = 120 \implies \text{Ratio } r_3 = 4.0$.

**Execution of Greedy by Ratio:**
1. Pick Item 1 (highest ratio $6.0$): Weight used $= 10$, Value $= 60$. Remaining capacity $= 40$.
2. Pick Item 2 (next ratio $5.0$): Weight used $= 10 + 20 = 30$, Value $= 60 + 100 = 160$. Remaining capacity $= 20$.
3. Item 3 needs weight $30 > 20$, so it **cannot fit**!
4. **Greedy Total Value:** $\mathbf{160}$ (using weight 30, leaving 20 wasted).

**Optimal Solution (via DP):**
- Pick Item 2 and Item 3!
- Weight used: $20 + 30 = 50 \le 50$.
- **Optimal Total Value:** $100 + 120 = \mathbf{220}$!
- **Greedy achieved only $160$ vs the optimal $220$!**

---

## 3. Deep Analysis of the PYQ Knapsack Puzzles (Slip Test 2)

These three questions from the course slip test test the deepest structural properties of Knapsack.

---

### Puzzle 1: Monotonic Weights and Values (Does Greedy Work?)

> **Question:** In class, we saw that greedy by ratio $v_i / w_i$ may not give an optimal solution to the 0/1 knapsack problem.  
> Suppose the order of the items when sorted by **increasing weight** is the **same** as the order when sorted by **decreasing value**.  
> That is, $w_1 \le w_2 \le \dots \le w_n$ AND $v_1 \ge v_2 \ge \dots \ge v_n$.  
> **Does the greedy algorithm work?**

**Verdict:** **YES! The greedy algorithm always gives an optimal solution!**

#### Mathematical Proof:
1. **Ratio Ordering:**  
   Since $v_1 \ge v_2 \ge \dots \ge v_n$ and $w_1 \le w_2 \le \dots \le w_n$:
   $$\frac{v_1}{w_1} \ge \frac{v_2}{w_2} \ge \dots \ge \frac{v_n}{w_n}$$
   The greedy ratio order is strictly: Item 1, Item 2, Item 3, $\dots$, Item $n$.
2. **Item 1 Strictly Dominates All Other Items:**  
   For any other item $j > 1$, $w_1 \le w_j$ and $v_1 \ge v_j$. Item 1 provides more (or equal) value for less (or equal) weight!
3. **The Exchange Argument:**  
   Suppose an optimal solution $S^*$ does not pick Item 1, but picks some subset of items containing item $j$.  
   Since $w_1 \le w_j$, replacing $j$ with Item 1 leaves total weight $\le W$, while total value increases or stays equal ($v_1 \ge v_j$).  
   Thus, Item 1 can always be assumed to be in the optimal solution.
4. **Can greedy leave a gap that allows multiple smaller items to beat it?**  
   NO! Because Item 1 is the **lightest** item in the entire set! Any other combination of items weighs at least as much as Item 1.  
   By induction on capacity $W - w_1$, the greedy strategy of packing items $1, 2, 3 \dots$ until capacity runs out is **strictly optimal**.

---

### Puzzle 2: All Items Share the Exact Same Value/Weight Ratio

> **Question:** Suppose that all items have the exact same ratio: $\frac{v_i}{w_i} = r$ for all $i$.  
> Which greedy strategy is guaranteed to find an optimal solution?  
> A. Lowest weight  
> B. Lowest value  
> C. Highest value  
> D. None of the above

**Verdict:** **D. None of the above greedy strategies is guaranteed to always give an optimal solution.**

#### Proof & Counterexample:
When all items share the exact same ratio $r$:
$$\text{Total Value} = \sum v_i = \sum (r \cdot w_i) = r \cdot \sum w_i$$
Maximizing total value is mathematically identical to **maximizing the total weight packed into the knapsack without exceeding $W$**:
$$\max \sum w_i \quad \text{subject to} \quad \sum w_i \le W$$
This is the classic **Subset Sum Problem**, which is NP-complete!

#### Concrete Counterexample:
- Capacity $W = 10$. Ratio $r = 1$ ($v_i = w_i$).
- Items:
  - Item 1: $w_1 = 6, v_1 = 6$
  - Item 2: $w_2 = 5, v_2 = 5$
  - Item 3: $w_3 = 5, v_3 = 5$
- **Greedy by Highest Value / Highest Weight:**  
  Picks Item 1 (weight 6). Remaining capacity $= 4$. Neither Item 2 nor 3 fits.  
  Total value $= \mathbf{6}$.
- **Greedy by Lowest Value / Lowest Weight:**  
  Suppose we had weights $\{2, 3, 6\}$ with $W = 8$. Lowest weight picks $2 + 3 = 5$, missing $6 + 2 = 8$.
- **Optimal Choice:**  
  Pick Item 2 and Item 3! Total weight $= 5 + 5 = 10 \le 10$.  
  Total value $= \mathbf{10} > 6$.

---

### Puzzle 3: Exponential Values ($v_{i+1} = 2 v_i$)

> **Question:** Suppose there are 10 items. Each item's value is double the previous item's value:  
> $v_1, \ v_2 = 2v_1, \ v_3 = 4v_1, \ \dots, \ v_{10} = 512v_1$.  
> Does greedily choosing items by highest value give an optimal solution, regardless of their weights?

**Verdict:** **YES! Greedily choosing the item with the highest value (and packing it whenever it fits) always produces an optimal solution!**

#### Mathematical Proof:
Look at the sum of values of all items smaller than item $k$:
$$\sum_{j=1}^{k-1} v_j = v_1 \sum_{j=0}^{k-2} 2^j = v_1 (2^{k-1} - 1) < v_1 \cdot 2^{k-1} = v_k$$
**The value of item $k$ is strictly greater than the sum of all smaller items combined!**
- Even if you packed **every single item from $1$ to $k-1$**, their combined value is $v_k - 1 < v_k$.
- Therefore, if item $k$ can physically fit into the remaining capacity of the knapsack, **you must take it**. Rejecting item $k$ can never be compensated for by any combination of smaller items!
- Hence, scanning items from largest value to smallest value ($10 \to 1$) and greedily taking each item if it fits is **strictly optimal**.

---

## 4. Slip Test 3 Deep Dive: The Two Thieves / Multiple Knapsack Problem

---

### Puzzle 1: Two Thieves ($W_1 = 15, W_2 = 17$, 7 Items)

> **Exam Question (Slip Test 3, 31 Aug 2026):**  
> Suppose that two thieves want to rob an apartment. The first thief can carry a weight of up to $15$, and the second can carry a weight of up to $17$. The list of available items is below. How should they maximize the total value of the items they steal?  
>
> | Item | Item 1 | Item 2 | Item 3 | Item 4 | Item 5 | Item 6 | Item 7 |
> | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
> | **Value ($v_i$)** | 24 | 23 | 18 | 20 | 13 | 14 | 11 |
> | **Weight ($w_i$)** | 8 | 7 | 6 | 5 | 4 | 4 | 3 |

#### Mathematical & Deductive Solution:
1. **Total System Parameters:**
   - Total weight of all 7 items:
     $$\sum_{i=1}^7 w_i = 8 + 7 + 6 + 5 + 4 + 4 + 3 = 37$$
   - Total carrying capacity of both thieves:
     $$W_1 + W_2 = 15 + 17 = 32$$
   - Total value of all 7 items:
     $$\sum_{i=1}^7 v_i = 24 + 23 + 18 + 20 + 13 + 14 + 11 = 123$$

2. **The Minimum Weight to Leave Behind:**
   - To fit within the combined capacity of $32$, the thieves must leave behind items whose total weight is **at least**:
     $$37 - 32 = 5$$
   - To maximize total value stolen, they must **minimize the total value of items left behind**!

3. **Evaluating Candidates to Leave Behind:**
   - What single items have weight $\ge 5$?
     - **Item 4:** Weight $5$, Value $20$ $\implies$ leaves behind value $20$.
     - **Item 3:** Weight $6$, Value $18$ $\implies$ leaves behind value **$18$**!
     - **Item 2:** Weight $7$, Value $23$ $\implies$ leaves behind value $23$.
     - **Item 1:** Weight $8$, Value $24$ $\implies$ leaves behind value $24$.
   - Any combination of multiple items with total weight $\ge 5$ (e.g., Item 5 + Item 7 = wt $7$, val $24$) has value $\ge 24 > 18$.
   - **Crucial Deduction:** Leaving behind **Item 3 alone** sacrifices the smallest possible value (**18**) while reducing total weight from $37$ to $31 \le 32$!
   - Theoretical Upper Bound on Stolen Value $= 123 - 18 = \mathbf{105}$.

4. **Can the Remaining 6 Items Physical Fit into Capacities $15$ and $17$?**
   The 6 items to pack are:
   - Item 1 (wt 8, val 24)
   - Item 2 (wt 7, val 23)
   - Item 4 (wt 5, val 20)
   - Item 5 (wt 4, val 13)
   - Item 6 (wt 4, val 14)
   - Item 7 (wt 3, val 11)
   
   Let us partition them between Thief 1 ($W_1 = 15$) and Thief 2 ($W_2 = 17$):
   - **Thief 1 (Capacity 15):**  
     Assign **Item 1 (8) + Item 2 (7)**:
     $$\text{Weight} = 8 + 7 = 15 \le 15 \quad (\text{Exact fit!})$$
     $$\text{Value} = 24 + 23 = 47$$
   - **Thief 2 (Capacity 17):**  
     Assign **Item 4 (5) + Item 5 (4) + Item 6 (4) + Item 7 (3)**:
     $$\text{Weight} = 5 + 4 + 4 + 3 = 16 \le 17 \quad (\le 17)$$
     $$\text{Value} = 20 + 13 + 14 + 11 = 58$$

5. **Final Optimal Result:**
   $$\text{Total Stolen Value} = 47 + 58 = \mathbf{105}$$
   - **Thief 1 carries:** Items 1 and 2 (Total weight: 15, Total value: 47)
   - **Thief 2 carries:** Items 4, 5, 6, and 7 (Total weight: 16, Total value: 58)
   - **Left Behind:** Item 3 (Weight: 6, Value: 18)

---

### Puzzle 2: General Two-Knapsack Algorithm & Complexity

> **Problem Statement (Slip Test 3):**  
> Devise an algorithm to solve the knapsack problem with two knapsacks of capacities $W_1$ and $W_2$, respectively. The number of available items is $n$, their weights are $w_1, w_2, \dots, w_n$, and values are $v_1, v_2, \dots, v_n$ (all positive integers).  
> Provide:
> - Pseudocode / Code
> - Explanation of correctness
> - Runtime analysis

#### 1. Dynamic Programming Formulation

Let $DP(i, c_1, c_2)$ denote the maximum total value achievable using a subset of the first $i$ items such that knapsack 1 has consumed capacity at most $c_1$ and knapsack 2 has consumed capacity at most $c_2$.

- **Base Cases:**
  $$DP(0, c_1, c_2) = 0 \quad \forall 0 \le c_1 \le W_1, 0 \le c_2 \le W_2$$
  (With zero items, the maximum achievable value is 0).

- **Recurrence Relation:**
  For each item $i$ (with weight $w_i$ and value $v_i$), we have three mutually exclusive, exhaustive decisions:
  1. **Do not steal item $i$:** The capacities $c_1$ and $c_2$ remain unchanged:
     $$\text{Val}_0 = DP(i-1, c_1, c_2)$$
  2. **Place item $i$ into Knapsack 1** (valid if $c_1 \ge w_i$):
     $$\text{Val}_1 = DP(i-1, c_1 - w_i, c_2) + v_i$$
  3. **Place item $i$ into Knapsack 2** (valid if $c_2 \ge w_i$):
     $$\text{Val}_2 = DP(i-1, c_1, c_2 - w_i) + v_i$$

  Therefore:
  $$DP(i, c_1, c_2) = \max \begin{cases}
  DP(i-1, c_1, c_2) \\
  DP(i-1, c_1 - w_i, c_2) + v_i & \text{if } c_1 \ge w_i \\
  DP(i-1, c_1, c_2 - w_i) + v_i & \text{if } c_2 \ge w_i
  \end{cases}$$

---

#### 2. Space Optimization to 2D Array
Notice that state $DP(i, \cdot, \cdot)$ depends **only on the previous item layer** $i-1$.  
Furthermore, transitions only look at strictly smaller capacities $(c_1 - w_i)$ or $(c_2 - w_i)$.  
Thus, by looping both $c_1$ from $W_1$ **down to $0$** and $c_2$ from $W_2$ **down to $0$**, we can collapse the DP table into a 2D array `dp[c1][c2]`:

$$\text{dp}[c_1][c_2] = \max \begin{cases}
\text{dp}[c_1][c_2] \\
\text{dp}[c_1 - w_i][c_2] + v_i & (\text{if } c_1 \ge w_i) \\
\text{dp}[c_1][c_2 - w_i] + v_i & (\text{if } c_2 \ge w_i)
\end{cases}$$

---

#### 3. C++ Implementation
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int solveTwoKnapsacks(int W1, int W2, const vector<int>& weights, const vector<int>& values) {
    int n = weights.size();

    // 2D DP table initialized to 0
    vector<vector<int>> dp(W1 + 1, vector<int>(W2 + 1, 0));

    // Process each item one by one
    for (int i = 0; i < n; ++i) {
        int w = weights[i];
        int v = values[i];

        // Traverse capacities backwards to prevent using the same item twice
        for (int c1 = W1; c1 >= 0; --c1) {
            for (int c2 = W2; c2 >= 0; --c2) {
                int option1 = (c1 >= w) ? dp[c1 - w][c2] + v : 0;
                int option2 = (c2 >= w) ? dp[c1][c2 - w] + v : 0;

                dp[c1][c2] = max({dp[c1][c2], option1, option2});
            }
        }
    }

    return dp[W1][W2];
}
```

---

#### 4. Complexity Analysis
- **Time Complexity:**
  - There are $n$ outer iterations (one per item).
  - Inside, two nested loops iterate over all capacities $c_1 \in [0, W_1]$ and $c_2 \in [0, W_2]$.
  - Each cell transition takes $O(1)$ constant time.
  - **Total Time Complexity:** $\mathbf{O(n \cdot W_1 \cdot W_2)}$.
- **Space Complexity:**
  - With the space-optimized 2D table, memory used is $\mathbf{O(W_1 \cdot W_2)}$.
  - (The naive 3D table would require $O(n \cdot W_1 \cdot W_2)$ space).

---

## 5. Dynamic Programming Formulations & Optimizations

### Standard DP: Weight as State ($O(n \cdot W)$)
Let $dp[i][w]$ be the maximum value achievable using a subset of the first $i$ items with total weight at most $w$.

#### Base Cases:
$$dp[0][w] = 0 \quad \forall w \in [0, W]$$

#### Recurrence Relation:
For item $i$ with weight $w_i$ and value $v_i$:
$$dp[i][w] = \begin{cases} 
dp[i-1][w] & \text{if } w < w_i \ (\text{cannot fit}) \\
\max\Big(dp[i-1][w], \ dp[i-1][w - w_i] + v_i\Big) & \text{if } w \ge w_i \ (\text{exclude or include})
\end{cases}$$

---

### 1D Array Space Optimization (Why Reverse Traversal is Mandatory)

Notice that row $i$ depends **only** on row $i-1$. We can collapse the 2D table into a 1D array $dp[w]$.

#### The Critical Direction of Traversal:
```cpp
for (int w = W; w >= w_i; --w) {
    dp[w] = max(dp[w], dp[w - w_i] + v_i);
}
```

> **Exam Trap Alert: Why MUST the loop run backwards from $W$ down to $w_i$?**

- If you run **backwards** ($W \to w_i$):  
  When computing $dp[w]$, the state $dp[w - w_i]$ has **not yet been updated** in the current iteration; it still holds the value from the *previous* item ($i-1$). This guarantees each item is used **at most once** (0/1 Knapsack).
- If you run **forwards** ($w_i \to W$):  
  When computing $dp[w]$, $dp[w - w_i]$ has *already been updated* using item $i$. Thus, item $i$ can be picked multiple times! That solves **Unbounded Knapsack**, not 0/1 Knapsack!

---

### Dual DP: Value as State ($O(n \cdot V_{\max})$ for Huge Capacities)
What if capacity $W = 10^{12}$ (too large for an array), but values are small (e.g., $v_i \le 100$, so $\sum v_i \le 10^5$)?

Let $dp[v]$ = the **minimum weight** needed to achieve total value $v$.
- **Base Case:** $dp[0] = 0$, all other $dp[v] = \infty$.
- **Recurrence:**
  $$dp[v] = \min(dp[v], \ dp[v - v_i] + w_i)$$
- **Final Answer:** The largest $v$ such that $dp[v] \le W$.
- **Time Complexity:** $O(n \sum v_i) = O(n^2 V_{\max})$.

---

## 6. Complete C++ Implementation

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

// 1. Standard 0/1 Knapsack (Space-Optimized 1D DP)
int knapsack01(int W, const vector<int>& weights, const vector<int>& values) {
    int n = weights.size();
    vector<int> dp(W + 1, 0);

    for (int i = 0; i < n; ++i) {
        // Reverse traversal ensures each item is used at most once
        for (int w = W; w >= weights[i]; --w) {
            dp[w] = max(dp[w], dp[w - weights[i]] + values[i]);
        }
    }
    return dp[W];
}

// 2. Unbounded Knapsack (Forward Traversal)
int unboundedKnapsack(int W, const vector<int>& weights, const vector<int>& values) {
    int n = weights.size();
    vector<int> dp(W + 1, 0);

    for (int i = 0; i < n; ++i) {
        // Forward traversal allows reusing the same item multiple times
        for (int w = weights[i]; w <= W; ++w) {
            dp[w] = max(dp[w], dp[w - weights[i]] + values[i]);
        }
    }
    return dp[W];
}

// 3. Reconstructing the Items Chosen
vector<int> reconstructKnapsack(int W, const vector<int>& weights, const vector<int>& values) {
    int n = weights.size();
    vector<vector<int>> dp(n + 1, vector<int>(W + 1, 0));

    for (int i = 1; i <= n; ++i) {
        for (int w = 0; w <= W; ++w) {
            if (weights[i - 1] <= w) {
                dp[i][w] = max(dp[i - 1][w], dp[i - 1][w - weights[i - 1]] + values[i - 1]);
            } else {
                dp[i][w] = dp[i - 1][w];
            }
        }
    }

    // Backtrack to find items
    vector<int> chosen_items;
    int curr_w = W;
    for (int i = n; i > 0; --i) {
        if (dp[i][curr_w] != dp[i - 1][curr_w]) {
            chosen_items.push_back(i - 1);
            curr_w -= weights[i - 1];
        }
    }
    reverse(chosen_items.begin(), chosen_items.end());
    return chosen_items;
}
```

---

## 7. Complexity & Complexity Class Analysis: Is Knapsack Polynomial?

> **Exam Question:** The DP algorithm runs in $O(n \cdot W)$ time. Is this a polynomial-time algorithm?  
> **Answer:** **NO! It is a PSEUDO-POLYNOMIAL time algorithm.**

### Why is $O(n \cdot W)$ Pseudo-Polynomial?
- In computational complexity theory, running time is measured as a function of the **input size in bits**.
- To represent capacity $W$ in binary, we need $L = \lceil \log_2 W \rceil$ bits.
- Thus, the magnitude of $W$ is **exponential** in its input length:
  $$W = 2^L$$
- Therefore, the running time is:
  $$O(n \cdot 2^L)$$
- This is **exponential in the number of bits of the input**, making it pseudo-polynomial!
- If $W$ is encoded in unary (e.g., $W$ tally marks), the input size is $W$, and only then would it be polynomial.

---

## 8. The Exam Reasoning Question Bank (Challenging Variations)

### Q1: Can 0/1 Knapsack have multiple optimal solutions?
**Yes.** If different subsets of items sum to the exact same total value and both fit within capacity $W$.

### Q2: What happens if all weights $w_i = 1$?
Then capacity $W$ simply allows picking any $W$ items. To maximize value, greedily pick the $W$ items with the **largest values**. Solvable in $O(n \log n)$ (sorting) or $O(n)$ using Quickselect!

### Q3: What happens if all values $v_i = 1$?
Then every item has equal value. To maximize the number of items packed, greedily pick items with the **smallest weights**. Solvable in $O(n \log n)$ or $O(n)$.

---

## 9. Curated Medium-to-Hard Problems (LeetCode & Codeforces)

Mastering Knapsack requires moving beyond textbook examples to multi-dimensional constraints, dual state representations, and sorting-conditioned knapsacks.

---

### Problem 1: Partition Equal Subset Sum (LeetCode 416 - Medium)

#### Problem Statement:
Given an integer array `nums`, return `true` if you can partition the array into two subsets such that the sum of the elements in both subsets is equal, or `false` otherwise.

#### Deep Algorithmic Intuition & Reduction:
- Let $S = \sum_{x \in \text{nums}} x$.
- If $S$ is odd, it is impossible to partition into two equal integer halves $\implies$ return `false`.
- If $S$ is even, our target is:
  $$\text{Target} = \frac{S}{2}$$
- **Reduction:** This is identical to 0/1 Knapsack where each number has weight $w_i = \text{nums}[i]$ and value $v_i = \text{nums}[i]$, with knapsack capacity $W = \text{Target}$. We simply need to check if there exists a subset whose total weight equals exactly $\text{Target}$.

#### C++ Solution (1D DP with Bitset Optimization):

```cpp
#include <vector>
#include <numeric>
#include <bitset>

using namespace std;

class Solution {
public:
    // Method 1: Standard 1D Boolean Knapsack DP O(N * Target)
    bool canPartition(vector<int>& nums) {
        int totalSum = accumulate(nums.begin(), nums.end(), 0);
        if (totalSum % 2 != 0) return false;
        int target = totalSum / 2;

        // dp[w] indicates whether a subset sum of 'w' is possible
        vector<bool> dp(target + 1, false);
        dp[0] = true; // Base case: sum of 0 is always possible (empty subset)

        for (int num : nums) {
            // Loop backwards to ensure each number is used at most once
            for (int w = target; w >= num; --w) {
                dp[w] = dp[w] || dp[w - num];
            }
            if (dp[target]) return true; // Early exit
        }
        return dp[target];
    }

    // Method 2: Ultra-Fast Bitset Optimization (64x speedup, O(N * Target / 64))
    bool canPartitionBitset(vector<int>& nums) {
        int totalSum = accumulate(nums.begin(), nums.end(), 0);
        if (totalSum % 2 != 0) return false;
        int target = totalSum / 2;

        // Max sum constraint on LeetCode is 200 * 100 = 20000 -> target <= 10000
        bitset<10001> bits;
        bits[0] = 1;

        for (int num : nums) {
            bits |= (bits << num); // Shift bits and OR in O(Target/64) time
            if (bits[target]) return true;
        }
        return bits[target];
    }
};
```

---

### Problem 2: Knapsack 2 (AtCoder DP Contest / CF - Medium-Hard)

#### Problem Statement:
There are $N$ items ($N \le 100$). Each item has weight $w_i$ and value $v_i$.  
**The Twist:** The capacity $W$ is **HUGE**: $W \le 10^9$.  
However, item values are small: $v_i \le 1000$ (so maximum possible total value $V_{\max} = N \times 1000 = 10^5$).  
Find the maximum value achievable without exceeding weight $W$.

#### Why Standard DP Fails:
Standard 0/1 Knapsack runs in $O(N \cdot W)$ time and space.  
Here, $W = 10^9$, so allocating an array of size $10^9$ causes an immediate **Memory Limit Exceeded (MLE)** and $10^{11}$ operations causes a **Time Limit Exceeded (TLE)**!

#### The Dual DP Strategy: Inverting States
Instead of finding the maximum value for a given weight, we invert the question:
> **Let $dp[v]$ be the MINIMUM WEIGHT required to achieve an exact total value $v$.**

- Max value is at most $V_{\text{total}} = 100 \times 1000 = 100,000$.
- **Base Case:** $dp[0] = 0$, all other $dp[v] = \infty$.
- **Recurrence:**
  $$dp[v] = \min(dp[v], \ dp[v - v_i] + w_i)$$
  *(Iterate backwards from $V_{\text{total}}$ down to $v_i$).*
- **Final Answer:** The maximum value $v$ such that $dp[v] \le W$.
- **Complexity:** $O(N \sum v_i) = O(100 \times 10^5) = O(10^7)$ operations, executing easily within 0.1 seconds!

#### C++ Solution:

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

const long long INF = 1e18;

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    int n;
    long long W;
    if (!(cin >> n >> W)) return 0;

    vector<long long> weights(n);
    vector<int> values(n);
    int max_val = 0;

    for (int i = 0; i < n; ++i) {
        cin >> weights[i] >> values[i];
        max_val += values[i];
    }

    // dp[v] = minimum weight required to achieve total value v
    vector<long long> dp(max_val + 1, INF);
    dp[0] = 0;

    for (int i = 0; i < n; ++i) {
        for (int v = max_val; v >= values[i]; --v) {
            if (dp[v - values[i]] != INF) {
                dp[v] = min(dp[v], dp[v - values[i]] + weights[i]);
            }
        }
    }

    // Find the largest value v such that weight dp[v] <= W
    int ans = 0;
    for (int v = max_val; v >= 0; --v) {
        if (dp[v] <= W) {
            ans = v;
            break;
        }
    }

    cout << ans << "\n";
    return 0;
}
```

---

### Problem 3: Fire! Knapsack with Deadlines (Codeforces 864E - Hard)

#### Problem Statement:
Polycarp is in a burning house with $n$ items ($n \le 100$).  
For each item $i$:
- It takes $t_i$ time to save ($1 \le t_i \le 20$).
- It burns completely at time $d_i$ ($1 \le d_i \le 2000$)—meaning if he starts saving it and finishes at time $\ge d_i$, the item is lost!
- Its value is $p_i$ ($1 \le p_i \le 20$).

Find the maximum total value of items Polycarp can save, the count of items saved, and their exact original 1-based indices in the order saved.

#### Key Insight (Greedy Sorting + DP):
In standard Knapsack, the order in which items are processed does not matter.  
**Here, order matters crucially because items have deadlines!**

> **The Exchange Argument Theorem:**  
> If an optimal solution saves a subset of items, they **MUST be saved in strictly non-decreasing order of their deadlines $d_i$**.

*Proof:* Suppose in an optimal schedule, item $A$ is saved before item $B$, but $d_A > d_B$.  
If we swap the saving order so $B$ is saved first:
- $B$ finishes earlier, so it still finishes before its deadline $d_B$.
- $A$ finishes at the same total time as before, which was $\le d_B < d_A$. So $A$ also finishes well before $d_A$.
- Swapping never hurts feasibility!

Therefore, we first **sort items by deadline $d_i$**. Once sorted, it reduces to 0/1 Knapsack with deadline constraints:
$$dp[i][\text{time}] = \max\Big(dp[i-1][\text{time}], \ dp[i-1][\text{time} - t_i] + p_i\Big) \quad \text{valid only if } \text{time} < d_i$$

#### C++ Solution (with Backtracking Reconstruction):

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

struct Item {
    int id, t, d, p;
};

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    int n;
    if (!(cin >> n)) return 0;

    vector<Item> items(n);
    int max_d = 0;
    for (int i = 0; i < n; ++i) {
        items[i].id = i + 1;
        cin >> items[i].t >> items[i].d >> items[i].p;
        max_d = max(max_d, items[i].d);
    }

    // Step 1: Sort by deadline (Earliest Deadline First)
    sort(items.begin(), items.end(), [](const Item& a, const Item& b) {
        return a.d < b.d;
    });

    // dp[i][time] = max value using a subset of first i items finishing at exact time 'time'
    // max_d is at most 2000
    vector<vector<int>> dp(n + 1, vector<int>(max_d + 1, 0));
    // parent[i][time] = true if item i was included
    vector<vector<bool>> taken(n + 1, vector<bool>(max_d + 1, false));

    for (int i = 1; i <= n; ++i) {
        int t = items[i - 1].t;
        int d = items[i - 1].d;
        int p = items[i - 1].p;

        for (int time = 0; time < max_d; ++time) {
            // Option 1: Do not take item i
            dp[i][time] = dp[i - 1][time];

            // Option 2: Take item i (only if it finishes strictly before deadline d)
            if (time >= t && time < d) {
                if (dp[i - 1][time - t] + p > dp[i][time]) {
                    dp[i][time] = dp[i - 1][time - t] + p;
                    taken[i][time] = true;
                }
            }
        }
    }

    // Find best final time
    int best_val = 0, best_time = 0;
    for (int time = 0; time < max_d; ++time) {
        if (dp[n][time] > best_val) {
            best_val = dp[n][time];
            best_time = time;
        }
    }

    // Backtrack to find items taken
    vector<int> chosen;
    int curr_time = best_time;
    for (int i = n; i >= 1; --i) {
        if (taken[i][curr_time]) {
            chosen.push_back(items[i - 1].id);
            curr_time -= items[i - 1].t;
        }
    }
    reverse(chosen.begin(), chosen.end());

    cout << best_val << "\n";
    cout << chosen.size() << "\n";
    for (int i = 0; i < chosen.size(); ++i) {
        cout << chosen[i] << (i + 1 == chosen.size() ? "" : " ");
    }
    cout << "\n";

    return 0;
}
```

---

### Problem 4: Ones and Zeroes: 2D Knapsack (LeetCode 474 - Medium-Hard)

#### Problem Statement:
You are given an array of binary strings `strs` and two integers `m` and `n`.  
Return the size of the largest subset of `strs` such that there are **at most $m$ 0's and $n$ 1's** in the subset.

#### Core Concept: 2D Knapsack
Instead of one weight constraint, we have **two independent weight constraints**:
- Constraint 1: Total zeros $\le m$
- Constraint 2: Total ones $\le n$
- Value of each item: $1$ (maximize the count of strings).

We iterate backwards across **both dimensions** to prevent duplicate usage of any string:

```cpp
#include <vector>
#include <string>
#include <algorithm>

using namespace std;

class Solution {
public:
    int findMaxForm(vector<string>& strs, int m, int n) {
        // dp[z][o] = max strings using at most z zeros and o ones
        vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));

        for (const string& s : strs) {
            int zeros = 0, ones = 0;
            for (char c : s) {
                if (c == '0') zeros++;
                else ones++;
            }

            // 2D reverse traversal
            for (int z = m; z >= zeros; --z) {
                for (int o = n; o >= ones; --o) {
                    dp[z][o] = max(dp[z][o], dp[z - zeros][o - ones] + 1);
                }
            }
        }

        return dp[m][n];
    }
};
```

---

## 10. Summary Checklist for Exam Day

1. **Fractional $\implies$ Greedy by ratio $v_i/w_i$ is optimal ($O(n \log n)$).**
2. **0/1 Knapsack $\implies$ Greedy fails; use DP ($O(nW)$).**
3. **1D DP space optimization:**
   - 0/1 Knapsack $\implies$ loop **BACKWARDS** ($W \to w_i$).
   - Unbounded Knapsack $\implies$ loop **FORWARDS** ($w_i \to W$).
4. **$O(nW)$ is PSEUDO-POLYNOMIAL**, not polynomial.
5. **Special Cases where 0/1 Greedy works:**
   - Monotonic: $w_1 \le w_2 \dots$ and $v_1 \ge v_2 \dots$
   - Exponential values: $v_{i+1} \ge 2v_i$
