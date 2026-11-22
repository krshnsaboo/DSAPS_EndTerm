# Mastering Interval Scheduling & Classroom Partitioning: From Intuition to Exam Mastery

> **Target Audience:** Students preparing for rigorous algorithms examinations (e.g., IIIT-H DSAPS, GATE, advanced greedy algorithms, dynamic programming).  
> **Source Material:** Fully aligned with Instructor Kshitij Gajjar's lecture slides (`Interval Scheduling.pdf`, 72 pages) and Midsem exam problems.  
> **Prerequisites:** None assumed! Built from scratch covering the 6 greedy heuristics, feasibility vs optimality proofs, the Greedy Stays Ahead induction technique, Interval Partitioning (Classroom Scheduling), weighted DP extensions, and complete C++ implementations.

---

## Table of Contents
1. [The Two Fundamental Scheduling Problems](#1-the-two-fundamental-scheduling-problems)
   - [Problem A: Interval Scheduling (Single Resource - Maximization)](#problem-a-interval-scheduling-single-resource---maximization)
   - [Problem B: Interval Partitioning / Classroom Scheduling (Multiple Resources - Minimization)](#problem-b-interval-partitioning--classroom-scheduling-multiple-resources---minimization)
   - [Real-World Applications Across Science & Engineering](#real-world-applications-across-science--engineering)
2. [The 6 Interval Scheduling Algorithms (Lecture Analysis)](#2-the-6-interval-scheduling-algorithms-lecture-analysis)
   - [Algorithm 1: EARLIEST-START-TIME (Feasible: YES, Optimal: NO)](#algorithm-1-earliest-start-time-feasible-yes-optimal-no)
   - [Algorithm 2: SELECT-SMALLEST-INTERVAL (Feasible: YES, Optimal: NO)](#algorithm-2-select-smallest-interval-feasible-yes-optimal-no)
   - [Algorithm 3: DELETE-LARGEST-INTERVAL (Feasible: YES, Optimal: NO)](#algorithm-3-delete-largest-interval-feasible-yes-optimal-no)
   - [Algorithm 4: DELETE-DOMINATING-INTERVALS (Feasible: NO!)](#algorithm-4-delete-dominating-intervals-feasible-no)
   - [Algorithm 5: Algorithm 4 + Algorithm 1 (Feasible: YES, Optimal: YES!)](#algorithm-5-algorithm-4--algorithm-1-feasible-yes-optimal-yes)
   - [Algorithm 6: EARLIEST-END-TIME (Feasible: YES, Optimal: YES!)](#algorithm-6-earliest-end-time-feasible-yes-optimal-yes)
   - [The Definitive Comparison Table (Slide 68)](#the-definitive-comparison-table-slide-68)
3. [The "Greedy Stays Ahead" Formal Induction Proof](#3-the-greedy-stays-ahead-formal-induction-proof)
   - [Setting up the Mathematical Framework](#setting-up-the-mathematical-framework)
   - [Lemma: Greedy Stays Ahead at Every Step $r$](#lemma-greedy-stays-ahead-at-every-step-r)
   - [Theorem: Greedy Finds the Globally Maximum Number of Intervals](#theorem-greedy-finds-the-globally-maximum-number-of-intervals)
4. [Interval Partitioning / Classroom Scheduling (Slide 71-72 Homework)](#4-interval-partitioning--classroom-scheduling-slide-71-72-homework)
   - [The Concept of Interval Depth](#the-concept-of-interval-depth)
   - [The Depth Lower Bound Theorem](#the-depth-lower-bound-theorem)
   - [The Greedy Earliest-Start-Time Room Allocation Algorithm](#the-greedy-earliest-start-time-room-allocation-algorithm)
   - [Step-by-Step Homework Solution (The 8 Classes Problem)](#step-by-step-homework-solution-the-8-classes-problem)
5. [Weighted Interval Scheduling: When Greedy Fails $\implies$ DP](#5-weighted-interval-scheduling-when-greedy-fails-implies-dp)
   - [Why Greedy Fails When Intervals Have Arbitrary Values](#why-greedy-fails-when-intervals-have-arbitrary-values)
   - [The $O(n \log n)$ Dynamic Programming Recurrence with Binary Search](#the-on-log-n-dynamic-programming-recurrence-with-binary-search)
6. [Complete Production-Grade C++ Implementations](#6-complete-production-grade-c-implementations)
   - [Implementation 1: Interval Scheduling (Earliest Finish Time - $O(n \log n)$)](#implementation-1-interval-scheduling-earliest-finish-time---on-log-n)
   - [Implementation 2: Classroom Scheduling / Interval Partitioning (Min-Heap - $O(n \log n)$)](#implementation-2-classroom-scheduling--interval-partitioning-min-heap---on-log-n)
   - [Implementation 3: Weighted Interval Scheduling (DP + Binary Search - $O(n \log n)$)](#implementation-3-weighted-interval-scheduling-dp--binary-search---on-log-n)
7. [Curated Exam & LeetCode Practice Problems](#7-curated-exam--leetcode-practice-problems)
   - [Problem 1: Non-overlapping Intervals (LeetCode 435 - Medium)](#problem-1-non-overlapping-intervals-leetcode-435---medium)
   - [Problem 2: Meeting Rooms II (LeetCode 253 - Medium)](#problem-2-meeting-rooms-ii-leetcode-253---medium)
   - [Problem 3: Maximum Profit in Job Scheduling (LeetCode 1235 - Hard)](#problem-3-maximum-profit-in-job-scheduling-leetcode-1235---hard)
8. [Exam Day Summary Checklist](#8-exam-day-summary-checklist)

---

## 1. The Two Fundamental Scheduling Problems

Scheduling problems are among the most celebrated algorithmic challenges in computer science. They divide cleanly into two dual paradigms:

### Problem A: Interval Scheduling (Single Resource - Maximization)
- **Input:** A single resource (e.g., one classroom, one CPU core, one telescope) and a set of $n$ requests / intervals $I = \{[s_1, e_1], [s_2, e_2], \dots, [s_n, e_n]\}$, where $s_i < e_i$.
- **Constraint:** Two intervals $[s_i, e_i]$ and $[s_j, e_j]$ clash if they overlap in time.
- **Objective:** Select the **maximum possible number of mutually compatible (non-overlapping) intervals** that can share this single resource.

---

### Problem B: Interval Partitioning / Classroom Scheduling (Multiple Resources - Minimization)
- **Input:** A set of $n$ requests / intervals $I = \{[s_1, e_1], \dots, [s_n, e_n]\}$.
- **Constraint:** **Every single class/job must be scheduled**; no request can be rejected. Within any single room, no two classes can clash.
- **Objective:** Determine the **minimum number of classrooms (resources)** required to schedule all $n$ classes without any clashes.

---

### Real-World Applications Across Science & Engineering
As highlighted in Instructor Kshitij Gajjar's lecture slides (Slide 69), interval scheduling models a massive spectrum of phenomena across wildly differing time scales:
- **Server Task Scheduling:** Managing non-overlapping thread execution on CPU cores (Scale: *microseconds / seconds*).
- **Airport Runway & Flight Planning:** Connecting flight planning and runway gate allocations (Scale: *minutes / hours*).
- **Maritime Port Logistics:** Cargo container transfers between shipping vessels and dock cranes (Scale: *days / weeks*).
- **Linguistics & Etymology:** Origin, divergence, and historical lifetimes of words across languages (Scale: *hundreds of years*).
- **Evolutionary Biology & Genealogy:** Phylogeny, speciation, and coexistence periods of species on Earth (Scale: *thousands / millions of years*).

---

## 2. The 6 Interval Scheduling Algorithms (Lecture Analysis)

In `Interval Scheduling.pdf`, Instructor Kshitij Gajjar systematically investigates six natural algorithmic ideas for the Single-Resource Interval Scheduling problem. For each algorithm, two fundamental questions must be answered:
1. **Is it FEASIBLE (Correct)?** Does it guarantee that the returned set of intervals contains **zero clashes**?
2. **Is it OPTIMAL?** Does it always return the **maximum possible number** of non-clashing intervals?

Let us inspect all six algorithms, their mechanics, and their counterexamples:

---

### Algorithm 1: EARLIEST-START-TIME
- **Strategy:** Sort all intervals in increasing order of their start time $s_i$. Greedily pick the first interval, discard all intervals that clash with it, and repeat.
- **Is it Feasible?** **YES.** By definition, any interval that clashes with an already selected interval is discarded. The output contains zero clashes.
- **Is it Optimal?** **NO!**
- **Deadly Counterexample:**
  - Suppose we have intervals:
    - $I_1 = [1, 100]$ (Starts very early, but is extremely long!)
    - $I_2 = [2, 5], \quad I_3 = [6, 10], \quad I_4 = [11, 15], \dots, I_{20} = [90, 95]$
  - **Greedy Execution:** Picks $I_1 = [1, 100]$ because it starts earliest at $t = 1$. It then discards all subsequent 19 intervals because they clash with $[1, 100]$!
  - **Greedy Output:** 1 interval.
  - **Optimal Output:** 19 intervals ($\{I_2, I_3, \dots, I_{20}\}$).
  - Greedy ratio: $\frac{1}{19}$. Arbitrarily bad!

---

### Algorithm 2: SELECT-SMALLEST-INTERVAL
- **Strategy:** Sort all intervals by their duration $(e_i - s_i)$. Greedily pick the shortest interval, discard all intervals that clash with it, and repeat.
- **Is it Feasible?** **YES.** Discarding conflicting intervals guarantees a valid, non-overlapping schedule.
- **Is it Optimal?** **NO!**
- **Deadly Counterexample (The Center Trap):**
  - Consider three intervals:
    - $I_1 = [1, 10]$ (Length 9)
    - $I_2 = [9, 11]$ (Length 2 — Shortest!)
    - $I_3 = [10, 20]$ (Length 10)
  - **Greedy Execution:** Greedily picks $I_2 = [9, 11]$ because its duration ($2$) is smallest.
    - $I_1$ overlaps with $I_2$ at $[9, 10]$ $\implies$ discarded!
    - $I_3$ overlaps with $I_2$ at $[10, 11]$ $\implies$ discarded!
  - **Greedy Output:** $\{I_2\} \implies \mathbf{1 \text{ interval}}$.
  - **Optimal Output:** $\{I_1, I_3\} \implies \mathbf{2 \text{ intervals}}$ (since $[1, 10]$ and $[10, 20]$ do not clash when treated as half-open $[1, 10)$ and $[10, 20)$, or $[1, 9]$ and $[11, 20]$)!
  - Choosing a short interval in the center can eliminate two long, mutually compatible intervals on either side.

---

### Algorithm 3: DELETE-LARGEST-INTERVAL
- **Strategy:** Instead of selecting items to keep, we iteratively find the interval with the longest duration $(e_i - s_i)$ and delete it until no clashes remain.
- **Is it Feasible?** **YES.** When the algorithm stops, no two remaining intervals clash.
- **Is it Optimal?** **NO!**
- **Counterexample:**
  - Consider four intervals:
    - $I_1 = [1, 10]$ (Length 9)
    - $I_2 = [2, 4]$ (Length 2)
    - $I_3 = [5, 7]$ (Length 2)
    - $I_4 = [8, 9]$ (Length 1)
  - In this case, deleting $I_1$ works, but consider:
    - $A = [1, 8]$ (Length 7)
    - $B_1 = [2, 5], B_2 = [4, 7]$ (Length 3 each)
    - Deleting the largest can eliminate the wrong candidate, leading to sub-optimal independent sets when multiple conflicts overlap asymmetrically.

---

### Algorithm 4: DELETE-DOMINATING-INTERVALS
- **Definition of Domination (Slide 39):**  
  An interval $A = [s_A, e_A]$ is said to **dominate** an interval $B = [s_B, e_B]$ if:
  $$s_A \le s_B \quad \text{AND} \quad e_A \ge e_B$$
  That is, interval $A$ completely contains interval $B$ ($B \subseteq A$).
- **Strategy:** Delete every interval that dominates any other interval.
- **Is it Feasible?** **NO! IT IS NOT EVEN FEASIBLE!**
- **Why Feasibility Fails:**
  - Deleting dominating intervals only eliminates complete containment. It does **NOT** resolve partial overlaps!
  - **Concrete Example:**  
    Consider $I_1 = [1, 5]$ and $I_2 = [3, 8]$.
    - Does $I_1$ dominate $I_2$? No ($e_1 = 5 < 8$).
    - Does $I_2$ dominate $I_1$? No ($s_2 = 3 > 1$).
    - Neither interval dominates the other!
    - Algorithm 4 deletes **neither interval** and stops!
    - The output $\{[1, 5], [3, 8]\}$ contains a direct clash over $[3, 5]$!
  - Hence, Algorithm 4 does not even produce a valid schedule.

---

### Algorithm 5: Algorithm 4 + Algorithm 1
- **Strategy:**
  1. **Phase 1:** Run Algorithm 4: DELETE-DOMINATING-INTERVALS.
  2. **Phase 2:** On the surviving subset of intervals, run Algorithm 1: EARLIEST-START-TIME.
- **Is it Feasible?** **YES.** Phase 2 discards any overlapping intervals, ensuring feasibility.
- **Is it Optimal?** **YES! IT IS PROVABLY OPTIMAL!**
- **The Profound Structural Insight (Why Algorithm 5 Works):**
  - Suppose two intervals $A$ and $B$ survive Phase 1, and suppose $s_A < s_B$.
  - What can we say about their end times $e_A$ and $e_B$?
  - **Claim:** It MUST be that $e_A < e_B$.
  - **Proof by Contradiction:**  
    Suppose $e_A \ge e_B$. Then $s_A \le s_B$ and $e_A \ge e_B$, which means interval $A$ dominates interval $B$! But Phase 1 deleted all dominating intervals! Contradiction!
  - Therefore, in the surviving set:
    $$s_A < s_B \iff e_A < e_B$$
  - **Sorting by earliest start time is mathematically identical to sorting by earliest end time!**
  - Since Earliest End Time is optimal, running Earliest Start Time on non-dominating intervals inherits the exact same optimality!

---

### Algorithm 6: EARLIEST-END-TIME
- **Strategy:** Sort all intervals by their end time (finish time) $e_i$. Greedily select the interval that finishes earliest. Discard all subsequent intervals that clash with it. Repeat until no intervals remain.
- **Is it Feasible?** **YES.** Clashing intervals are discarded.
- **Is it Optimal?** **YES! GLOBALLY OPTIMAL!**
- **Core Intuition:** Finishing as early as possible frees up the resource as early as possible, maximizing the remaining time available to accommodate future intervals!

---

### The Definitive Comparison Table (Slide 68)

| Algorithm Number | Algorithm Name | Feasible (Correct)? | Optimal? | Exam Status |
|:---:|:---|:---:|:---:|:---|
| **Algorithm 1** | EARLIEST-START-TIME | **YES** | **NO** | Counterexample: $[1, 100]$ vs $[2, 5], [6, 10]$ |
| **Algorithm 2** | SELECT-SMALLEST-INTERVAL | **YES** | **NO** | Counterexample: Center trap $[9, 11]$ vs $[1, 10], [10, 20]$ |
| **Algorithm 3** | DELETE-LARGEST-INTERVAL | **YES** | **NO** | Counterexample: Asymmetric overlap chains |
| **Algorithm 4** | DELETE-DOMINATING-INTERVALS | **NO** | **—** | Output still contains partial overlaps! |
| **Algorithm 5** | Algorithm 4 + Algorithm 1 | **YES** | **YES** | Non-dominance implies $s_i < s_j \iff e_i < e_j$ |
| **Algorithm 6** | EARLIEST-END-TIME | **YES** | **YES** | Proved via **Greedy Stays Ahead** |

---

## 3. The "Greedy Stays Ahead" Formal Induction Proof

The standard proof technique for greedy scheduling algorithms is known as **Greedy Stays Ahead**.

### Setting up the Mathematical Framework
Let $G = \{g_1, g_2, \dots, g_k\}$ be the set of intervals chosen by the greedy algorithm (Algorithm 6: Earliest End Time), ordered chronologically by finish time.  
Let $O = \{o_1, o_2, \dots, o_m\}$ be any arbitrary **optimal solution**, also ordered chronologically by finish time.  
Our goal is to prove that $k = m$ (Greedy selects the exact same number of intervals as optimal).

---

### Lemma: Greedy Stays Ahead at Every Step $r$
For every $r \in \{1, 2, \dots, k\}$:
$$e(g_r) \le e(o_r)$$
*(The finish time of the $r$-th greedy interval is at or before the finish time of the $r$-th optimal interval).*

#### Proof by Induction on $r$:
1. **Base Case ($r = 1$):**
   - Greedy chooses $g_1$ as the interval with the **globally earliest finish time** among all available intervals.
   - Therefore, by definition:
     $$e(g_1) \le e(o_1)$$
   - The base case holds.

2. **Inductive Hypothesis:**
   - Assume the statement holds for step $r - 1$:
     $$e(g_{r-1}) \le e(o_{r-1})$$

3. **Inductive Step (Proving for step $r$):**
   - In the optimal solution $O$, interval $o_r$ comes after $o_{r-1}$ without clashing.
   - Therefore:
     $$s(o_r) \ge e(o_{r-1})$$
   - Applying the inductive hypothesis $e(o_{r-1}) \ge e(g_{r-1})$, we get:
     $$s(o_r) \ge e(o_{r-1}) \ge e(g_{r-1})$$
   - This means that when the greedy algorithm finished choosing $g_{r-1}$, the interval $o_r$ had a start time after $e(g_{r-1})$, making $o_r$ a **valid, compatible candidate** available for greedy to choose at step $r$!
   - But greedy chose $g_r$, which is the interval with the **minimum finish time** among all available compatible candidates.
   - Therefore:
     $$e(g_r) \le e(o_r)$$
   - The induction step is complete.

---

### Theorem: Greedy Finds the Globally Maximum Number of Intervals
- Suppose for contradiction that greedy is not optimal. Since $O$ is an optimal solution, it must pick strictly more intervals than greedy:
  $$m > k \implies m \ge k + 1$$
- By the Lemma, for $r = k$, we have:
  $$e(g_k) \le e(o_k)$$
- Since $m \ge k + 1$, there exists an interval $o_{k+1}$ in the optimal solution.
- Because $O$ has no clashes:
  $$s(o_{k+1}) \ge e(o_k) \ge e(g_k)$$
- This means $o_{k+1}$ starts after the last greedy interval $g_k$ finishes!
- Thus, $o_{k+1}$ was compatible and available for greedy to select as a $(k+1)$-th interval!
- But greedy stopped at $k$ intervals only when **no compatible intervals remained**!
- Contradiction! Hence, $k = m$, and the greedy algorithm is strictly optimal. $\blacksquare$

---

## 4. Interval Partitioning / Classroom Scheduling (Slide 71-72 Homework)

In Slide 71 and 72 of `Interval Scheduling.pdf`, Instructor Kshitij Gajjar assigns the following problem as Homework:

> **Homework Problem Statement:**  
> The time periods for 8 classes are given below:  
> $$[1, 4], \quad [7, 9], \quad [3, 8], \quad [11, 15], \quad [2, 13], \quad [10, 18], \quad [14, 19], \quad [5, 6]$$  
> **What is the minimum number of classrooms required to schedule the classes so that there are no clashes?**

---

### The Concept of Interval Depth
- **Definition:** The **depth** of a set of intervals is the maximum number of intervals that overlap at any single point in time $t$:
  $$\text{Depth } d = \max_{t} \Big| \{ I_i \mid s_i \le t < e_i \} \Big|$$

---

### The Depth Lower Bound Theorem
- **Theorem:** Any valid schedule requires **at least $d$ classrooms**, where $d$ is the depth.
- **Proof:** At the specific time $t^*$ where depth $d$ is attained, $d$ distinct classes are running simultaneously. Since no two classes can share the same classroom at time $t^*$, each of these $d$ classes must be placed in a distinct classroom. Thus, $\text{Classrooms} \ge d$.

---

### The Greedy Earliest-Start-Time Room Allocation Algorithm
Can we always schedule all classes in **exactly $d$ classrooms**? **YES!**

#### The Algorithm:
1. Sort all $n$ classes by **increasing start time** $s_i$.
2. Maintain a collection of available classrooms (or track the finish time of the last class assigned to each room using a Min-Heap).
3. For each class $I_i = [s_i, e_i]$:
   - Check if any classroom is free at time $s_i$ (i.e., its last class finished at or before $s_i$).
   - If a room is free, assign class $I_i$ to that room and update the room's free time to $e_i$.
   - If no existing room is free, open a **brand new classroom** and assign class $I_i$ to it.

#### Proof that Greedy Uses At Most $d$ Classrooms:
- Suppose the algorithm is about to open classroom number $k$.
- This only happens when class $I_i$ cannot fit into any of the existing $k - 1$ classrooms.
- This implies that at time $s_i$, all $k - 1$ existing classrooms are currently hosting classes that overlap with $I_i$.
- Including class $I_i$, there are $k$ intervals simultaneously overlapping at time $s_i$.
- Therefore, the depth at time $s_i$ is at least $k$:
  $$k \le \text{depth} \le d$$
- Hence, the algorithm never opens more than $d$ classrooms!
- **Conclusion:** $\mathbf{\text{Minimum Classrooms Required} = d}$.

---

### Step-by-Step Homework Solution (The 8 Classes Problem)

Let us index the 8 given intervals:
- $I_1 = [1, 4]$
- $I_2 = [2, 13]$
- $I_3 = [3, 8]$
- $I_4 = [5, 6]$
- $I_5 = [7, 9]$
- $I_6 = [10, 18]$
- $I_7 = [11, 15]$
- $I_8 = [14, 19]$

#### 1. Timeline Chronology & Active Interval Tracking:
Let us trace the active intervals as time advances:
- At $t = 1$: $I_1$ starts $\implies$ Active: $\{I_1\}$ (Count: 1)
- At $t = 2$: $I_2$ starts $\implies$ Active: $\{I_1, I_2\}$ (Count: 2)
- At $t = 3$: $I_3$ starts $\implies$ Active: $\{I_1, I_2, I_3\}$ (Count: **3**)
- At $t = 4$: $I_1$ finishes $\implies$ Active: $\{I_2, I_3\}$ (Count: 2)
- At $t = 5$: $I_4$ starts $\implies$ Active: $\{I_2, I_3, I_4\}$ (Count: **3**)
- At $t = 6$: $I_4$ finishes $\implies$ Active: $\{I_2, I_3\}$ (Count: 2)
- At $t = 7$: $I_5$ starts $\implies$ Active: $\{I_2, I_3, I_5\}$ (Count: **3**)
- At $t = 8$: $I_3$ finishes $\implies$ Active: $\{I_2, I_5\}$ (Count: 2)
- At $t = 9$: $I_5$ finishes $\implies$ Active: $\{I_2\}$ (Count: 1)
- At $t = 10$: $I_6$ starts $\implies$ Active: $\{I_2, I_6\}$ (Count: 2)
- At $t = 11$: $I_7$ starts $\implies$ Active: $\{I_2, I_6, I_7\}$ (Count: **3**)
- At $t = 13$: $I_2$ finishes $\implies$ Active: $\{I_6, I_7\}$ (Count: 2)
- At $t = 14$: $I_8$ starts $\implies$ Active: $\{I_6, I_7, I_8\}$ (Count: **3**)
- At $t = 15$: $I_7$ finishes $\implies$ Active: $\{I_6, I_8\}$ (Count: 2)
- At $t = 18$: $I_6$ finishes $\implies$ Active: $\{I_8\}$ (Count: 1)
- At $t = 19$: $I_8$ finishes $\implies$ Active: $\emptyset$ (Count: 0)

Notice that at times $t = 3, 5, 7, 11, 14$, exactly 3 intervals overlap simultaneously, and **at no time do 4 intervals overlap**!
$$\mathbf{\text{Maximum Depth } d = 3}$$

#### 2. The Classroom Assignment:
Applying the greedy room allocation algorithm produces an exact schedule using **3 classrooms**:

| Classroom | Assigned Classes | Timeline Verification | Any Clashes? |
|:---|:---|:---|:---:|
| **Classroom 1** | $I_1 [1, 4] \longrightarrow I_4 [5, 6] \longrightarrow I_5 [7, 9] \longrightarrow I_7 [11, 15]$ | $1-4$, then $5-6$, then $7-9$, then $11-15$ | **NO** |
| **Classroom 2** | $I_3 [3, 8] \longrightarrow I_6 [10, 18]$ | $3-8$, then $10-18$ | **NO** |
| **Classroom 3** | $I_2 [2, 13] \longrightarrow I_8 [14, 19]$ | $2-13$, then $14-19$ | **NO** |

**Final Answer to Slide 71-72 Homework:**
$$\mathbf{\text{Minimum Classrooms Required} = 3}$$

---

## 5. Weighted Interval Scheduling: When Greedy Fails $\implies$ DP

What happens if each interval $I_i = [s_i, e_i]$ has an associated **value / weight $v_i > 0$**, and our goal is to **maximize the total value** of non-overlapping intervals?

### Why Greedy Fails
- Consider:
  - $I_1 = [1, 5]$ with value $v_1 = 10$.
  - $I_2 = [2, 4]$ with value $v_2 = 1000$.
- Earliest End Time selects $I_2$ (finishes at 4), which works here, but consider:
  - $I_1 = [1, 10]$ with $v_1 = 1000$.
  - $I_2 = [1, 2]$ with $v_2 = 1$.
  - Earliest End Time greedily picks $I_2$ (finishes at 2) and achieves value $1$, completely missing $I_1$ with value $1000$!
- **Greedy fails completely for Weighted Interval Scheduling.** We must use **Dynamic Programming**!

---

### The $O(n \log n)$ Dynamic Programming Recurrence with Binary Search

1. **Sort intervals by finish time:** $e_1 \le e_2 \le \dots \le e_n$.
2. **Define Predecessor $p(i)$:**  
   For each interval $i$, let $p(i)$ be the index of the last compatible interval that finishes before interval $i$ starts:
   $$p(i) = \max \{ j < i \mid e_j \le s_i \}$$
   If no such interval exists, $p(i) = 0$.
   *(Because the intervals are sorted by finish time, $p(i)$ can be computed in $O(\log n)$ using binary search!)*
3. **State Definition:**  
   Let $DP[i]$ be the maximum weight achievable considering a subset of the first $i$ intervals.
4. **Recurrence Relation:**  
   For interval $i$, we make a binary choice:
   - **Do not include interval $i$:** Best value is $DP[i-1]$.
   - **Include interval $i$:** Receive value $v_i$, plus the optimal solution from compatible previous intervals $DP[p(i)]$.
   $$DP[i] = \max\Big( DP[i-1], \quad v_i + DP[p(i)] \Big)$$
5. **Complexity:** Sorting takes $O(n \log n)$. Binary search for all $n$ predecessors takes $n \times O(\log n) = O(n \log n)$. Table filling takes $O(n)$. Total time: $\mathbf{O(n \log n)}$.

---

## 6. Complete Production-Grade C++ Implementations

### Implementation 1: Interval Scheduling (Earliest Finish Time - $O(n \log n)$)

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

struct Interval {
    int id;
    int start;
    int end;
};

// Returns maximum set of non-overlapping intervals (Algorithm 6)
vector<Interval> maxIntervalScheduling(vector<Interval>& intervals) {
    // Sort primarily by earliest end time
    sort(intervals.begin(), intervals.end(), [](const Interval& a, const Interval& b) {
        if (a.end != b.end) return a.end < b.end;
        return a.start < b.start;
    });

    vector<Interval> selected;
    int last_finish = -1;

    for (const auto& iv : intervals) {
        if (iv.start >= last_finish) {
            selected.push_back(iv);
            last_finish = iv.end;
        }
    }

    return selected;
}
```

---

### Implementation 2: Classroom Scheduling / Interval Partitioning (Min-Heap - $O(n \log n)$)

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <algorithm>

using namespace std;

struct Interval {
    int id;
    int start;
    int end;
};

// Solves Slide 71-72 Homework: Minimum classrooms required
int minClassroomsRequired(vector<Interval>& intervals) {
    if (intervals.empty()) return 0;

    // Step 1: Sort all intervals by START time
    sort(intervals.begin(), intervals.end(), [](const Interval& a, const Interval& b) {
        if (a.start != b.start) return a.start < b.start;
        return a.end < b.end;
    });

    // Min-heap storing the finish time of the last class assigned to each classroom
    priority_queue<int, vector<int>, greater<int>> min_heap;

    for (const auto& iv : intervals) {
        // If the earliest finishing classroom is free before this class starts, reuse it
        if (!min_heap.empty() && min_heap.top() <= iv.start) {
            min_heap.pop();
        }
        // Allocate current class to the room (or a newly opened room)
        min_heap.push(iv.end);
    }

    // Size of the min-heap equals the minimum classrooms required
    return min_heap.size();
}
```

---

### Implementation 3: Weighted Interval Scheduling (DP + Binary Search - $O(n \log n)$)

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

struct Job {
    int start;
    int end;
    int value;
};

int weightedIntervalScheduling(vector<Job>& jobs) {
    int n = jobs.size();
    if (n == 0) return 0;

    // Sort jobs by finish time
    sort(jobs.begin(), jobs.end(), [](const Job& a, const Job& b) {
        return a.end < b.end;
    });

    vector<int> dp(n + 1, 0);

    for (int i = 1; i <= n; ++i) {
        // Find latest non-conflicting job j < i using binary search
        int low = 0, high = i - 2, pred = -1;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (jobs[mid].end <= jobs[i - 1].start) {
                pred = mid;
                low = mid + 1; // Try to find a later compatible job
            } else {
                high = mid - 1;
            }
        }

        int include_val = jobs[i - 1].value + (pred != -1 ? dp[pred + 1] : 0);
        int exclude_val = dp[i - 1];

        dp[i] = max(include_val, exclude_val);
    }

    return dp[n];
}
```

---

## 7. Curated Exam & LeetCode Practice Problems

### Problem 1: Non-overlapping Intervals (LeetCode 435 - Medium)
- **Problem Statement:** Given an array of intervals `intervals` where `intervals[i] = [start_i, end_i]`, return the minimum number of intervals you need to remove to make the rest of the intervals non-overlapping.
- **Reduction to Interval Scheduling:**  
  $$\text{Intervals to Remove} = \text{Total Intervals } n - \text{Max Compatible Intervals (Algorithm 6)}$$
- **C++ Solution:**
```cpp
class Solution435 {
public:
    int eraseOverlapIntervals(vector<vector<int>>& intervals) {
        if (intervals.empty()) return 0;
        sort(intervals.begin(), intervals.end(), [](const vector<int>& a, const vector<int>& b) {
            return a[1] < b[1]; // Sort by finish time
        });

        int kept = 0;
        int last_end = -2e9;
        for (const auto& iv : intervals) {
            if (iv[0] >= last_end) {
                kept++;
                last_end = iv[1];
            }
        }
        return intervals.size() - kept;
    }
};
```

---

### Problem 2: Meeting Rooms II (LeetCode 253 - Medium)
- **Problem Statement:** Given an array of meeting time intervals `intervals` where `intervals[i] = [start_i, end_i]`, return the minimum number of conference rooms required.
- **Equivalence:** Identical to Slide 71-72 Homework (Interval Partitioning depth)!
- **C++ Solution:** Uses the Min-Heap implementation provided in Section 6.

---

### Problem 3: Maximum Profit in Job Scheduling (LeetCode 1235 - Hard)
- **Problem Statement:** We have $n$ jobs, where every job is scheduled to be done from `startTime[i]` to `endTime[i]`, obtaining a profit of `profit[i]`. Find the maximum profit you can take such that no two jobs overlap.
- **Equivalence:** Identical to Weighted Interval Scheduling ($O(n \log n)$ DP + Binary Search) provided in Section 6.

---

## 8. Exam Day Summary Checklist

1. **Interval Scheduling vs Partitioning:**
   - **Scheduling:** 1 room $\implies$ **maximize classes** (Sort by **End Time**).
   - **Partitioning:** All classes must happen $\implies$ **minimize rooms** (Sort by **Start Time**, Rooms $=$ **Depth** $d$).
2. **The 6 Heuristics Recall:**
   - Alg 1 (Earliest Start): Feasible YES, Optimal NO.
   - Alg 2 (Smallest Duration): Feasible YES, Optimal NO.
   - Alg 3 (Delete Largest Duration): Feasible YES, Optimal NO.
   - Alg 4 (Delete Dominating): **Feasible NO** (leaves partial overlaps).
   - Alg 5 (Delete Dominating + Earliest Start): **Feasible YES, Optimal YES** ($s_i < s_j \iff e_i < e_j$).
   - Alg 6 (Earliest End Time): **Feasible YES, Optimal YES** (Greedy stays ahead).
3. **Classroom Homework Answer:** The 8 classes on Slide 71-72 achieve a peak depth of $d = 3$ at time $t = 3$. Hence, **3 classrooms** are both necessary and sufficient.
4. **Weights $\implies$ Dynamic Programming:** When intervals have values, greedy fails; use $O(n \log n)$ DP with predecessor binary search.
