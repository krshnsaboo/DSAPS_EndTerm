# Mastering Longest Common Subsequence (LCS): The Complete Algorithmic & Reasoning Guide

> **Target Audience:** Students preparing for rigorous dynamic programming and algorithmic exams (e.g., IIIT-H DSAPS, GATE, technical interviews).  
> **Prerequisites:** None assumed! Built from scratch with intuitive definitions, formal prefix proofs, Hirschberg's linear-space algorithm, C++ code, and classic reduction relationships.

---

## Table of Contents
1. [The Big Picture: Subsequence vs Substring](#1-the-big-picture-subsequence-vs-substring)
   - [Crucial Distinction](#crucial-distinction)
   - [Formal Problem Statement](#formal-problem-statement)
2. [Optimal Substructure & Recurrence Relation](#2-optimal-substructure--recurrence-relation)
   - [The Three Cases (Prefix Property)](#the-three-cases-prefix-property)
   - [Formal Proof of the Recurrence via Cut-and-Paste](#formal-proof-of-the-recurrence-via-cut-and-paste)
3. [The Standard DP Table & Backtracking Reconstruction](#3-the-standard-dp-table--backtracking-reconstruction)
   - [Table Filling Mechanics ($O(m \cdot n)$ Time and Space)](#table-filling-mechanics-om-cdot-n-time-and-space)
   - [Reconstructing the LCS String](#reconstructing-the-lcs-string)
   - [Can There Be Multiple LCS Strings?](#can-there-be-multiple-lcs-strings)
4. [Space Optimizations: From $O(m \cdot n)$ to $O(m + n)$](#4-space-optimizations-from-om-cdot-n-to-om--n)
   - [Two-Row / 1D Rolling Array for Length ($O(\min(m, n))$ Space)](#two-row--1d-rolling-array-for-length-ominm-n-space)
   - [Hirschberg’s Algorithm: Full String Reconstruction in $O(m + n)$ Space](#hirschbergs-algorithm-full-string-reconstruction-in-om--n-space)
5. [The Great Web of Reductions (Exam Goldmine)](#5-the-great-web-of-reductions-exam-goldmine)
   - [Shortest Common Supersequence (SCS)](#shortest-common-supersequence-scs)
   - [Minimum Edit Distance (Insertions & Deletions Only)](#minimum-edit-distance-insertions--deletions-only)
   - [Longest Palindromic Subsequence (LPS)](#longest-palindromic-subsequence-lps)
   - [Longest Increasing Subsequence (LIS)](#longest-increasing-subsequence-lis)
6. [Complete Production-Grade C++ Implementation](#6-complete-production-grade-c-implementation)
7. [Slip Test 2 Deep Dive: Edit Distance (Levenshtein Distance)](#7-slip-test-2-deep-dive-edit-distance-levenshtein-distance)
   - [Slip Test 2 (17 Aug 2026) - Puzzle 1: Evaluations with Exact Notation](#slip-test-2-17-aug-2026---puzzle-1-evaluations-with-exact-notation)
   - [Slip Test 2 (17 Aug 2026) - Puzzle 2: Full Algorithm, Proof & Complexity](#slip-test-2-17-aug-2026---puzzle-2-full-algorithm-proof--complexity)
8. [Advanced Exam Variations & Twisted Scenarios](#8-advanced-exam-variations--twisted-scenarios)
   - [Variation 1: LCS of $k$ Strings (Is It Polynomial?)](#variation-1-lcs-of-k-strings-is-it-polynomial)
   - [Variation 2: When Does Greedy Work for LCS?](#variation-2-when-does-greedy-work-for-lcs)
   - [Variation 3: Counting the Total Number of Distinct LCS Strings](#variation-3-counting-the-total-number-of-distinct-lcs-strings)
9. [Curated Medium-to-Hard Problems (LeetCode & Codeforces)](#9-curated-medium-to-hard-problems-leetcode--codeforces)
   - [Problem 1: Shortest Common Supersequence (LeetCode 1092 - Hard)](#problem-1-shortest-common-supersequence-leetcode-1092---hard)
   - [Problem 2: Distinct Subsequences Counting (LeetCode 115 - Hard)](#problem-2-distinct-subsequences-counting-leetcode-115---hard)
   - [Problem 3: Catching Cheaters (Codeforces 1446B - Medium-Hard)](#problem-3-catching-cheaters-codeforces-1446b---medium-hard)
   - [Problem 4: Levenshtein Edit Distance (LeetCode 72 - Medium-Hard)](#problem-4-levenshtein-edit-distance-leetcode-72---medium-hard)
10. [Exam Day Summary Checklist](#10-exam-day-summary-checklist)

---

## 1. The Big Picture: Subsequence vs Substring

### Crucial Distinction
Given a string $S = \text{"ABCDEFG"}$:
- A **substring** must be strictly **contiguous**:  
  $\text{"BCD"}$ and $\text{"EF"}$ are substrings. $\text{"ACD"}$ is **NOT** a substring.
- A **subsequence** is obtained by deleting zero or more characters **without changing the relative order** of the remaining characters (it does **not** need to be contiguous):  
  $\text{"ACD"}$, $\text{"BDF"}$, and $\text{"AG"}$ are valid subsequences!

### Formal Problem Statement
Given two sequences:
- $X = \langle x_1, x_2, \dots, x_m \rangle$
- $Y = \langle y_1, y_2, \dots, y_n \rangle$

Find the maximum length of a sequence $Z = \langle z_1, z_2, \dots, z_k \rangle$ that is a subsequence of both $X$ and $Y$.

---

## 2. Optimal Substructure & Recurrence Relation

Let $X_i = \langle x_1, \dots, x_i \rangle$ be the $i$-th prefix of $X$, and $Y_j = \langle y_1, \dots, y_j \rangle$ be the $j$-th prefix of $Y$.  
Let $dp[i][j]$ be the length of the LCS of $X_i$ and $Y_j$.

### The Three Cases (Prefix Property)
1. **Case 1 (Character Match):** If $x_i == y_j$:  
   The matching character **must be part of the optimal solution for this subproblem**:
   $$dp[i][j] = dp[i-1][j-1] + 1$$
2. **Case 2 (Character Mismatch):** If $x_i \ne y_j$:  
   The last characters cannot both be part of the same LCS. Therefore, either $x_i$ is discarded, or $y_j$ is discarded (or both):
   $$dp[i][j] = \max\Big(dp[i-1][j], \ dp[i][j-1]\Big)$$

#### Base Cases:
$$dp[i][0] = 0 \quad \forall i, \qquad dp[0][j] = 0 \quad \forall j$$

---

### Formal Proof of the Recurrence via Cut-and-Paste

#### Proof for Case 1 ($x_i == y_j$):
Let $Z = \langle z_1, \dots, z_k \rangle$ be an LCS of $X_i$ and $Y_j$.
- **Claim:** $z_k = x_i = y_j$.
- *Proof:* If $z_k \ne x_i$, we could append $x_i$ to $Z$, creating a common subsequence of length $k + 1$, contradicting that $Z$ was the longest common subsequence!
- Now, consider the prefix $Z_{k-1} = \langle z_1, \dots, z_{k-1} \rangle$.
- $Z_{k-1}$ is clearly a common subsequence of $X_{i-1}$ and $Y_{j-1}$ of length $k - 1$.
- Is $Z_{k-1}$ an LCS of $X_{i-1}$ and $Y_{j-1}$?
  - Suppose for contradiction that there existed a longer common subsequence $W$ of $X_{i-1}$ and $Y_{j-1}$ with $|W| \ge k$.
  - Appending $x_i$ to $W$ would yield a common subsequence of $X_i$ and $Y_j$ of length $\ge k + 1$, contradicting that $|Z| = k$ was maximal.
- Thus, $|Z_{k-1}| = dp[i-1][j-1] \implies dp[i][j] = dp[i-1][j-1] + 1$. $\blacksquare$

---

## 3. The Standard DP Table & Backtracking Reconstruction

### Table Filling Mechanics ($O(m \cdot n)$ Time and Space)
Let $X = \text{"LONGEST"}$ ($m = 7$) and $Y = \text{"STONE"}$ ($n = 5$):

```
       Ø   S   T   O   N   E
   Ø   0   0   0   0   0   0
   L   0   0   0   0   0   0
   O   0   0   0   1   1   1
   N   0   0   0   1   2   2
   G   0   0   0   1   2   2
   E   0   0   0   1   2   3
   S   0   1   1   1   2   3
   T   0   1   2   2   2   3
```
Length of LCS $= dp[7][5] = 3$ ($\text{"ONE"}$).

---

### Reconstructing the LCS String
Start at cell $(m, n)$ and backtrack to $(0, 0)$:
1. If $X[i-1] == Y[j-1]$, this character was matched! Prepend it to the result and move diagonally: $(i-1, j-1)$.
2. If $X[i-1] \ne Y[j-1]$:
   - If $dp[i-1][j] \ge dp[i][j-1]$, move **UP** to $(i-1, j)$.
   - Else, move **LEFT** to $(i, j-1)$.
3. Stop when $i = 0$ or $j = 0$.

### Can There Be Multiple LCS Strings?
**YES!** When $dp[i-1][j] == dp[i][j-1]$, branching both UP and LEFT yields different valid LCS strings.  
*Example:* $X = \text{"AB"}, Y = \text{"BA"}$. LCS length is 1, and both $\text{"A"}$ and $\text{"B"}$ are valid LCS strings!

---

### Step-by-Step Lecture Walkthrough: $X = \text{"bafdecafdc"}$ vs $Y = \text{"abcdef"}$

*(Directly from Instructor Kshitij Gajjar's lecture slides `Longest common subsequence.pdf`)*

Let $X = \text{"b a f d e c a f d c"}$ ($m = 10$) and $Y = \text{"a b c d e f"}$ ($n = 6$).  
The rows represent characters of $Y$, and the columns represent characters of $X$:

```
        Ø   b   a   f   d   e   c   a   f   d   c
    Ø   0   0   0   0   0   0   0   0   0   0   0
    a   0   0   1   1   1   1   1   1   1   1   1
    b   0   1   1   1   1   1   1   1   1   1   1
    c   0   1   1   1   1   1   2   2   2   2   2
    d   0   1   1   1   2   2   2   2   2   3   3
    e   0   1   1   1   2   3   3   3   3   3   3
    f   0   1   1   2   2   3   3   3   4   4   4
```

#### Key Exam Observations from the Slides:
1. **The "Pause: We need to compute cell $X$" Question (Slide 31):**
   - At cell row `e`, column `b` (prefix `b` of $X$ and prefix `abcde` of $Y$):
     - Character `b` does not match `e`.
     - The cell takes $\max(dp[\text{row } e-1, b], dp[\text{row } e, \emptyset]) = \max(1, 0) = 1$.
2. **Final LCS Length:**
   - Look at the bottom-right cell: $dp[6][10] = \mathbf{4}$.
3. **Multiple Optimal Solutions (Reconstruction):**
   - Tracing back from the bottom-right cell reveals multiple valid LCS strings of length 4:
     - $\text{"a d e f"}$ (using $X[2]=\text{'a'}, X[4]=\text{'d'}, X[5]=\text{'e'}, X[9]=\text{'f'}$)
     - $\text{"b d e f"}$ (using $X[1]=\text{'b'}, X[4]=\text{'d'}, X[5]=\text{'e'}, X[9]=\text{'f'}$)
     - $\text{"a c d c"}$ / $\text{"b c d c"}$
   - In an exam, if asked for **all** LCS strings, branch at every cell where $dp[i-1][j] == dp[i][j-1]$.

---

## 4. Space Optimizations: From $O(m \cdot n)$ to $O(m + n)$

### Two-Row / 1D Rolling Array for Length ($O(\min(m, n))$ Space)
Notice that computing row $i$ requires **only row $i-1$**.  
We can maintain two rows (`prev` and `curr`), reducing space to $O(\min(m, n))$.

---

### Hirschberg’s Algorithm: Full String Reconstruction in $O(m + n)$ Space
How can we reconstruct the **entire string** in $O(m + n)$ space while keeping running time $O(m \cdot n)$?  
**Hirschberg's Algorithm** (1975) combines Dynamic Programming with **Divide and Conquer**:

1. **Split $X$ in half:** Let $mid = \lfloor m/2 \rfloor$.
2. **Compute forward DP:** Run space-optimized DP on $X[1..mid]$ and $Y[1..n]$ to get the LCS lengths from the top.
3. **Compute backward DP:** Run space-optimized DP on the reversed strings of $X[mid+1..m]$ and $Y[1..n]$ to get the LCS lengths from the bottom.
4. **Find the Optimal Split Point $k^*$ in $Y$:**
   $$k^* = \arg\max_{0 \le k \le n} \Big(dp_{\text{forward}}[k] + dp_{\text{backward}}[n - k]\Big)$$
   This tells us: the optimal LCS passes through the character pair $(X[mid], Y[k^*])$!
5. **Recurse:**
   - Left subproblem: $X[1..mid]$ with $Y[1..k^*]$
   - Right subproblem: $X[mid+1..m]$ with $Y[k^*+1..n]$
6. **Total Time:**
   $$T(m, n) = O(mn) + T(m/2, k^*) + T(m/2, n - k^*) = O(mn) + O(mn/2) + O(mn/4) \dots = \mathbf{O(mn)}$$
7. **Total Space:** Only storing DP rows $\implies \mathbf{O(m + n)}$!

---

## 5. The Great Web of Reductions (Exam Goldmine)

Professors love testing your ability to reduce other problems to LCS!

---

### Shortest Common Supersequence (SCS)
A **supersequence** of $X$ and $Y$ is a string that contains both $X$ and $Y$ as subsequences.
> **The Golden Formula:**
> $$|SCS(X, Y)| = |X| + |Y| - |LCS(X, Y)|$$

**Intuition:** Characters present in the LCS can be shared once between $X$ and $Y$. Characters not in the LCS must each appear separately.

---

### Minimum Edit Distance (Insertions & Deletions Only)
If you can only **insert** and **delete** characters (cost 1 each):
$$\text{Deletions from } X = |X| - |LCS(X, Y)|$$
$$\text{Insertions into } X = |Y| - |LCS(X, Y)|$$
$$\text{Total Minimum Edit Distance} = |X| + |Y| - 2|LCS(X, Y)|$$

---

### Longest Palindromic Subsequence (LPS)
To find the longest subsequence of string $S$ that reads the same forwards and backwards:
$$LPS(S) \equiv LCS\Big(S, \ \text{reverse}(S)\Big)$$

---

### Longest Increasing Subsequence (LIS)
Given an array $A$ of $n$ numbers, let $B$ be a copy of $A$ sorted in strictly increasing order with duplicates removed. Then:
$$LIS(A) \equiv LCS(A, B)$$
*(Solves LIS in $O(n^2)$ time; patience sorting can optimize this to $O(n \log n)$).*

---

## 6. Complete Production-Grade C++ Implementation

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

using namespace std;

// 1. Standard 2D DP with Full Reconstruction
string findLCS(const string& X, const string& Y) {
    int m = X.size(), n = Y.size();
    vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));

    // Fill table
    for (int i = 1; i <= m; ++i) {
        for (int j = 1; j <= n; ++j) {
            if (X[i - 1] == Y[j - 1]) {
                dp[i][j] = dp[i - 1][j - 1] + 1;
            } else {
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
    }

    // Backtrack to reconstruct string
    string lcs = "";
    int i = m, j = n;
    while (i > 0 && j > 0) {
        if (X[i - 1] == Y[j - 1]) {
            lcs += X[i - 1];
            i--; j--;
        } else if (dp[i - 1][j] >= dp[i][j - 1]) {
            i--;
        } else {
            j--;
        }
    }
    reverse(lcs.begin(), lcs.end());
    return lcs;
}

// 2. Space-Optimized LCS Length (O(min(m, n)) space)
int lcsLengthOptimized(const string& X, const string& Y) {
    if (X.size() < Y.size()) return lcsLengthOptimized(Y, X);
    int m = X.size(), n = Y.size();

    vector<int> prev(n + 1, 0), curr(n + 1, 0);

    for (int i = 1; i <= m; ++i) {
        for (int j = 1; j <= n; ++j) {
            if (X[i - 1] == Y[j - 1]) {
                curr[j] = prev[j - 1] + 1;
            } else {
                curr[j] = max(prev[j], curr[j - 1]);
            }
        }
        prev = curr;
    }
    return prev[n];
}
```

---

## 7. Slip Test 2 Deep Dive: Edit Distance (Levenshtein Distance)

---

### Slip Test 2 (17 Aug 2026) - Overview & Notation

In Slip Test 2, Instructor Kshitij Gajjar introduced **Levenshtein Distance**, defined as the minimum number of edit operations needed to transform string $A$ into string $B$.  
Permissible operations:
1. **Letter Insertions**
2. **Letter Deletions**
3. **Letter Substitutions / Replacements**

#### Instructor's Exact Evaluation Notation:
1. **Alignment Representation:** Align the two words in two parallel rows. Gaps (blank spaces) in the bottom row represent **deletions** from the first word; gaps in the top row represent **insertions** into the first word; mismatched characters aligned vertically represent **substitutions**.
2. **Step-by-Step Transformation Sequence:** Show the word progressively changing one character at a time from word $A$ to word $B$.
3. **Distance Declaration:** Conclude with $\text{dist}(A, B) = k$.

---

### Slip Test 2 - Puzzle 1 [3 Marks]: Evaluations

#### 1. $\text{dist}(\text{"sleeveless"}, \text{"lessens"})$

- **Length of $A$ ("sleeveless"):** 10
- **Length of $B$ ("lessens"):** 7
- **Alignment Representation:**
  ```
  s   l   e   e   v   e   l   e   s   s
      l       e       s   s   e   n   s
  ```
- **Step-by-Step Transformation:**
  1. Delete `s` $\to$ `leeveless`
  2. Delete `e` $\to$ `leveless`
  3. Delete `v` $\to$ `leless`
  4. Substitute `e` $\to$ `s` $\to$ `lsless` (or replace `l` $\to$ `s`)
  5. Substitute `l` $\to$ `e` $\to$ `lsens`
  6. Substitute `s` $\to$ `n` $\to$ `lessens`
- **Minimum Edit Operations:** 3 deletions + 3 substitutions = **6 operations**.
- **Verdict:**
  $$\mathbf{dist(\text{"sleeveless"}, \text{"lessens"}) = 6}$$

---

#### 2. $\text{dist}(\text{"intelligent"}, \text{"belligerent"})$

- **Length of $A$ ("intelligent"):** 11
- **Length of $B$ ("belligerent"):** 11
- **Key Observation:** The core substring `"ellig"` and suffix `"ent"` match across both words!
- **Alignment Representation:**
  ```
  i   n   t   e   l   l   i   g           e   n   t
          b   e   l   l   i   g   e   r   e   n   t
  ```
- **Step-by-Step Transformation:**
  1. Delete `i` $\to$ `ntelligent`
  2. Delete `n` $\to$ `telligent`
  3. Substitute `t` $\to$ `b` $\to$ `belligent`
  4. Insert `e` after `g` $\to$ `belligeent`
  5. Insert `r` after `e` $\to$ `belligerent`
- **Minimum Edit Operations:** 2 deletions + 1 substitution + 2 insertions = **5 operations**.
- **Verdict:**
  $$\mathbf{dist(\text{"intelligent"}, \text{"belligerent"}) = 5}$$

---

#### 3. $\text{dist}(\text{"melanin"}, \text{"millennial"})$

- **Length of $A$ ("melanin"):** 7
- **Length of $B$ ("millennial"):** 10
- **Key Insight:** Notice that in `"melanin"`, `e` comes before `l`, whereas in `"millennial"`, `l` comes before `e`. Thus, `e` and `l` cannot both be matched simultaneously without an edit.
- **Alignment Representation:**
  ```
  m       e   l       a   n   i       n
  m   i   l   l   e   n   n   i   a   l
  ```
- **Step-by-Step Transformation:**
  1. Insert `i` after `m` $\to$ `mielanin`
  2. Substitute `e` $\to$ `l` $\to$ `millanin`
  3. Insert `e` after second `l` $\to$ `milleanin`
  4. Substitute `a` $\to$ `n` $\to$ `millennin`
  5. Insert `a` after `i` $\to$ `millennian`
  6. Substitute final `n` $\to$ `l` $\to$ `millennial`
- **Minimum Edit Operations:** 3 insertions + 3 substitutions = **6 operations**.
- **Verdict:**
  $$\mathbf{dist(\text{"melanin"}, \text{"millennial"}) = 6}$$

---

### Slip Test 2 - Puzzle 2 [7 Marks]: The Full Edit Distance Algorithm

> **Problem Statement:**  
> Devise an algorithm to find the distance between two strings $A$ and $B$ of lengths $n_1$ and $n_2$, respectively. Grade parameters:
> - Code / pseudocode.
> - Explanation of its working.
> - Analysis of its runtime.

#### 1. Mathematical Formulation & Recurrence
Let $DP[i][j]$ denote the minimum edit distance between the prefix $A[1..i]$ (of length $i$) and prefix $B[1..j]$ (of length $j$).

- **Base Cases:**
  - $DP[0][j] = j$: Transforming an empty string $\emptyset$ into prefix $B[1..j]$ requires $j$ insertions.
  - $DP[i][0] = i$: Transforming prefix $A[1..i]$ into empty string $\emptyset$ requires $i$ deletions.
  - $DP[0][0] = 0$: Both strings empty, zero cost.

- **Recurrence Relation:**
  For $1 \le i \le n_1$ and $1 \le j \le n_2$:
  $$DP[i][j] = \begin{cases}
  DP[i-1][j-1] & \text{if } A[i] = B[j] \text{ (Free Match)} \\
  1 + \min \begin{cases}
  DP[i-1][j] & \text{(Deletion of } A[i] \text{)} \\
  DP[i][j-1] & \text{(Insertion of } B[j] \text{)} \\
  DP[i-1][j-1] & \text{(Substitution of } A[i] \to B[j] \text{)}
  \end{cases} & \text{if } A[i] \ne B[j]
  \end{cases}$$

---

#### 2. Explanation of Working & Correctness
- **Optimal Substructure:** Any optimal sequence of edits transforming $A[1..i]$ to $B[1..j]$ must end in one of three operations on the last characters:
  1. **Delete $A[i]$:** We already transformed $A[1..i-1]$ to $B[1..j]$ at cost $DP[i-1][j]$, and now delete $A[i]$ ($+1$).
  2. **Insert $B[j]$:** We already transformed $A[1..i]$ to $B[1..j-1]$ at cost $DP[i][j-1]$, and now insert $B[j]$ at the end ($+1$).
  3. **Substitute $A[i] \to B[j]$:** We already transformed $A[1..i-1]$ to $B[1..j-1]$ at cost $DP[i-1][j-1]$, and substitute $A[i]$ with $B[j]$ ($+1$). If $A[i] == B[j]$, no substitution cost is incurred ($+0$).
- By taking the minimum over all three locally exhaustive choices, dynamic programming guarantees the global minimum edit distance.

---

#### 3. Pseudocode
```
Algorithm EditDistance(A[1..n1], B[1..n2]):
1. Create 2D array DP[0..n1][0..n2]
2. For i = 0 to n1: DP[i][0] = i
3. For j = 0 to n2: DP[0][j] = j
4.
5. For i = 1 to n1:
6.     For j = 1 to n2:
7.         If A[i] == B[j]:
8.             DP[i][j] = DP[i-1][j-1]
9.         Else:
10.            DP[i][j] = 1 + min({ DP[i-1][j],      // Deletion
11.                                 DP[i][j-1],      // Insertion
12.                                 DP[i-1][j-1] })  // Substitution
13.
14. Return DP[n1][n2]
```

---

#### 4. C++ Implementation
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

using namespace std;

int editDistance(const string& A, const string& B) {
    int n1 = A.size();
    int n2 = B.size();

    // DP table of size (n1 + 1) x (n2 + 1)
    vector<vector<int>> dp(n1 + 1, vector<int>(n2 + 1, 0));

    // Base cases
    for (int i = 0; i <= n1; ++i) dp[i][0] = i;
    for (int j = 0; j <= n2; ++j) dp[0][j] = j;

    // Fill the DP table
    for (int i = 1; i <= n1; ++i) {
        for (int j = 1; j <= n2; ++j) {
            if (A[i - 1] == B[j - 1]) {
                dp[i][j] = dp[i - 1][j - 1]; // Match
            } else {
                dp[i][j] = 1 + min({
                    dp[i - 1][j],     // Deletion
                    dp[i][j - 1],     // Insertion
                    dp[i - 1][j - 1]  // Substitution
                });
            }
        }
    }

    return dp[n1][n2];
}
```

---

#### 5. Complexity Analysis
- **Time Complexity:**
  - The DP table contains $(n_1 + 1) \times (n_2 + 1)$ cells.
  - Computing each cell involves a constant number of comparisons ($O(1)$ arithmetic operations).
  - **Total Time Complexity:** $\mathbf{O(n_1 \cdot n_2)}$.
- **Space Complexity:**
  - Storing the 2D DP matrix requires $\mathbf{O(n_1 \cdot n_2)}$ memory.
  - *Space Optimization Note:* If only the distance is required (without reconstructing the edit script), we only need the current and previous rows, reducing space to $\mathbf{O(\min(n_1, n_2))}$.

---

## 8. Advanced Exam Variations & Twisted Scenarios

### Variation 1: LCS of $k$ Strings (Is It Polynomial?)
- For $k = 3$ strings of length $n$: $dp[i][j][k]$ runs in $O(n^3)$ time, which is polynomial.
- For **arbitrary $k$** (where $k$ is part of the input): **LCS is NP-Hard!**

### Variation 2: When Does Greedy Work for LCS?
- If all characters in string $X$ are **distinct**, every character in $Y$ that appears in $X$ can be mapped to its unique index in $X$.
- The problem then reduces to finding the **Longest Increasing Subsequence (LIS)** of indices, solvable in **$O(n \log n)$** time using binary search!

---

## 9. Curated Medium-to-Hard Problems (LeetCode & Codeforces)

Mastering LCS requires excelling at supersequence interleaving, distinct subsequence counting, and substring-penalized DP variants.

---

### Problem 1: Shortest Common Supersequence (LeetCode 1092 - Hard)

#### Problem Statement:
Given two strings `str1` and `str2`, return the **shortest string** that has both `str1` and `str2` as subsequences. If there are multiple valid answers, you may return any of them.

#### Deep Algorithmic Intuition:
1. First, compute the standard 2D LCS table between `str1` ($m$) and `str2` ($n$).
2. The length of the SCS is $|str1| + |str2| - |LCS|$.
3. **Reconstruction Strategy:** We backtrack from $(m, n)$ to $(0, 0)$:
   - If `str1[i-1] == str2[j-1]`, this character is in the LCS! We add it **once** to our result and move diagonally $(i-1, j-1)$.
   - If `str1[i-1] != str2[j-1]`:
     - If $dp[i-1][j] > dp[i][j-1]$, moving UP was optimal. This means character `str1[i-1]` was NOT part of the LCS. We **must still include it in the supersequence**! Add `str1[i-1]` and move UP $(i-1, j)$.
     - Else, moving LEFT was optimal. Character `str2[j-1]` was not part of the LCS. Add `str2[j-1]` and move LEFT $(i, j-1)$.
4. Append any remaining characters from whichever string has not reached index $0$, and reverse the result.

#### C++ Solution:

```cpp
#include <string>
#include <vector>
#include <algorithm>

using namespace std;

class Solution1092 {
public:
    string shortestCommonSupersequence(string str1, string str2) {
        int m = str1.size(), n = str2.size();
        vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));

        // Step 1: Compute LCS table
        for (int i = 1; i <= m; ++i) {
            for (int j = 1; j <= n; ++j) {
                if (str1[i - 1] == str2[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                } else {
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }

        // Step 2: Backtrack and reconstruct SCS
        string scs = "";
        int i = m, j = n;
        while (i > 0 && j > 0) {
            if (str1[i - 1] == str2[j - 1]) {
                scs += str1[i - 1]; // Common character included once
                i--; j--;
            } else if (dp[i - 1][j] > dp[i][j - 1]) {
                scs += str1[i - 1]; // str1 char not in LCS, must still be included
                i--;
            } else {
                scs += str2[j - 1]; // str2 char not in LCS, must still be included
                j--;
            }
        }

        // Add remaining characters
        while (i > 0) scs += str1[--i];
        while (j > 0) scs += str2[--j];

        reverse(scs.begin(), scs.end());
        return scs;
    }
};
```

---

### Problem 2: Distinct Subsequences Counting (LeetCode 115 - Hard)

#### Problem Statement:
Given two strings `s` and `t`, return the **number of distinct subsequences** of `s` which equals `t`.

#### Algorithmic Formulation:
Let $dp[i][j]$ be the number of distinct subsequences of $s[0..i-1]$ that equal $t[0..j-1]$.
- **Base Case:** $dp[i][0] = 1$ for all $i$ (an empty target $t$ can always be formed in exactly 1 way: by deleting all characters).
- **Recurrence:**
  - If $s[i-1] == t[j-1]$: We can either match $s[i-1]$ with $t[j-1]$, or skip $s[i-1]$:
    $$dp[i][j] = dp[i-1][j-1] + dp[i-1][j]$$
  - If $s[i-1] \ne t[j-1]$: We cannot match, so we must skip $s[i-1]$:
    $$dp[i][j] = dp[i-1][j]$$

#### Space-Optimized 1D C++ Solution:

```cpp
#include <string>
#include <vector>

using namespace std;

class Solution115 {
public:
    int numDistinct(string s, string t) {
        int m = s.size(), n = t.size();
        // Use unsigned long long to handle intermediate large counts
        vector<unsigned long long> dp(n + 1, 0);
        dp[0] = 1; // Empty string t is matched by 1 empty subsequence

        for (int i = 1; i <= m; ++i) {
            // Loop backwards to use previous row values
            for (int j = n; j >= 1; --j) {
                if (s[i - 1] == t[j - 1]) {
                    dp[j] += dp[j - 1];
                }
            }
        }

        return (int)dp[n];
    }
};
```

---

### Problem 3: Catching Cheaters (Codeforces 1446B - Medium-Hard)

#### Problem Statement:
You are given two strings $A$ and $B$ of lengths $n$ and $m$ ($n, m \le 5000$).  
Choose a contiguous non-empty substring $A'$ of $A$ and a contiguous non-empty substring $B'$ of $B$ to maximize the score:
$$\text{Score}(A', B') = 4 \cdot LCS(A', B') - |A'| - |B'|$$
Find the maximum possible score (or $0$ if all scores are negative).

#### Algorithmic Insights (Kadane's Reset + LCS):
Notice the scoring formula:
- Matching character pair: adds $4$ to $4 \cdot LCS$, but adds $1$ to $|A'|$ and $1$ to $|B'|$. Net gain: $+4 - 1 - 1 = \mathbf{+2}$.
- Extending $A'$ with a mismatch: penalty $-1$.
- Extending $B'$ with a mismatch: penalty $-1$.
- Because $A'$ and $B'$ can start **anywhere** (they are substrings), if our score drops below $0$, we can discard the previous prefix and start fresh at $0$! **(Kadane's Algorithm principle)**.

#### Recurrence:
$$dp[i][j] = \max\begin{cases}
0 \\
dp[i-1][j-1] + 2 & \text{if } A[i-1] == B[j-1] \\
dp[i-1][j] - 1 \\
dp[i][j-1] - 1
\end{cases}$$

#### C++ Solution:

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>

using namespace std;

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    int n, m;
    if (!(cin >> n >> m)) return 0;
    string A, B;
    cin >> A >> B;

    vector<vector<int>> dp(n + 1, vector<int>(m + 1, 0));
    int max_score = 0;

    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= m; ++j) {
            if (A[i - 1] == B[j - 1]) {
                dp[i][j] = max(dp[i][j], dp[i - 1][j - 1] + 2);
            } else {
                dp[i][j] = max({0, dp[i - 1][j] - 1, dp[i][j - 1] - 1});
            }
            max_score = max(max_score, dp[i][j]);
        }
    }

    cout << max_score << "\n";
    return 0;
}
```

---

### Problem 4: Levenshtein Edit Distance (LeetCode 72 - Medium-Hard)

#### Problem Statement:
Given two strings `word1` and `word2`, return the minimum number of operations required to convert `word1` to `word2`.  
Permitted operations:
1. **Insert** a character (cost 1)
2. **Delete** a character (cost 1)
3. **Replace** a character (cost 1)

#### Recurrence:
$$dp[i][j] = \begin{cases}
dp[i-1][j-1] & \text{if } word1[i-1] == word2[j-1] \\
1 + \min\begin{cases}
dp[i-1][j] & \text{(Delete)} \\
dp[i][j-1] & \text{(Insert)} \\
dp[i-1][j-1] & \text{(Replace)}
\end{cases} & \text{if } word1[i-1] \ne word2[j-1]
\end{cases}$$

#### Space-Optimized C++ Solution:

```cpp
#include <string>
#include <vector>
#include <algorithm>

using namespace std;

class Solution72 {
public:
    int minDistance(string word1, string word2) {
        int m = word1.size(), n = word2.size();
        vector<int> prev(n + 1, 0), curr(n + 1, 0);

        for (int j = 0; j <= n; ++j) prev[j] = j; // Base case: inserting all chars of word2

        for (int i = 1; i <= m; ++i) {
            curr[0] = i; // Base case: deleting all chars of word1
            for (int j = 1; j <= n; ++j) {
                if (word1[i - 1] == word2[j - 1]) {
                    curr[j] = prev[j - 1];
                } else {
                    curr[j] = 1 + min({prev[j], curr[j - 1], prev[j - 1]});
                }
            }
            prev = curr;
        }

        return prev[n];
    }
};
```

---

## 10. Exam Day Summary Checklist

1. **Subsequence $\ne$ Substring** (Subsequence allows skips).
2. **$dp[i][j] = dp[i-1][j-1] + 1$ on match; $\max(dp[i-1][j], dp[i][j-1])$ on mismatch.**
3. **Space:** Length needs only $O(\min(m, n))$ space; string reconstruction needs $O(m + n)$ space via **Hirschberg's Algorithm**.
4. **Key Reductions:**
   - $|SCS| = |X| + |Y| - |LCS|$
   - $LPS(S) = LCS(S, \text{reverse}(S))$
   - Edit Distance $= |X| + |Y| - 2|LCS|$
