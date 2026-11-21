# The Definitive Guide to Gale-Shapley & Stable Matching
## From Foundational Intuition to Extreme Exam Mastery

> **Target Audience:** Students preparing for rigorous theoretical computer science and algorithms exams (such as IIIT-H CS1.304 DSAPS End-Sem, GATE, and advanced algorithmic design).  
> **Prerequisites:** None assumed! Built from scratch with simple language, deep intuition, complete mathematical proofs, robust C++ code, and an exhaustive analysis of exam-style variations and traps.

---

## Table of Contents
1. [The Problem: What is a Stable Matching?](#1-the-problem-what-is-a-stable-matching)
   - [The Setup](#the-setup)
   - [What is a Blocking Pair (Instability)?](#what-is-a-blocking-pair-instability)
   - [Why Simple Greedy Matchings Fail](#why-simple-greedy-matchings-fail)
2. [The Gale-Shapley Algorithm (Deferred Acceptance)](#2-the-gale-shapley-algorithm-deferred-acceptance)
   - [The Core Philosophy](#the-core-philosophy)
   - [The High-Level Algorithm](#the-high-level-algorithm)
   - [Detailed Step-by-Step Trace (3 Boys & 3 Girls)](#detailed-step-by-step-trace-3-boys--3-girls)
3. [Algorithmic Pseudocode & Data Structures](#3-algorithmic-pseudocode--data-structures)
   - [The $O(1)$ Accepter Ranking Matrix Trick](#the-o1-accepter-ranking-matrix-trick)
   - [Clean Algorithmic Pseudocode](#clean-algorithmic-pseudocode)
4. [Complete Production-Grade C++ Implementation](#4-complete-production-grade-c-implementation)
   - [Modern C++ Implementation with Explanation](#modern-c-implementation-with-explanation)
   - [Automated Stability Verifier (Blocking Pair Detector)](#automated-stability-verifier-blocking-pair-detector)
   - [Uniqueness Verifier (Dual Proposer Matcher)](#uniqueness-verifier-dual-proposer-matcher)
5. [The Bedrock Mathematical Proofs (The "Why" Behind Everything)](#5-the-bedrock-mathematical-proofs-the-why-behind-everything)
   - [Proof 1: Termination in at most $n^2$ proposals](#proof-1-termination-in-at-most-n2-proposals)
   - [Proof 2: Perfection (Every person gets married)](#proof-2-perfection-every-person-gets-married)
   - [Proof 3: Guaranteed Stability (No blocking pairs exist)](#proof-3-guaranteed-stability-no-blocking-pairs-exist)
   - [Proof 4: Proposer-Optimality (Boys get their best valid partner)](#proof-4-proposer-optimality-boys-get-their-best-valid-partner)
   - [Proof 5: Accepter-Pessimality (Girls get their worst valid partner)](#proof-5-accepter-pessimality-girls-get-their-worst-valid-partner)
   - [Proof 6: Invariance to Proposal Order (The McVitie-Wilson Theorem)](#proof-6-invariance-to-proposal-order-the-mcvitie-wilson-theorem)
6. [Deep Deconstruction of Previous Year Exam Questions](#6-deep-deconstruction-of-previous-year-exam-questions)
   - [PYQ 1 (Puzzle 6a): Designing Worst-Case Proposal Cascades ($\ge \frac{n(n+1)}{2}$)](#pyq-1-puzzle-6a-designing-worst-case-proposal-cascades-ge-fracnn12)
   - [PYQ 2 (Puzzle 6b): Auditing Stability & Discovering Alternative Matchings](#pyq-2-puzzle-6b-auditing-stability--discovering-alternative-matchings)
   - [PYQ 3 (Puzzle 6c): The $O(n^2)$ Uniqueness Test via Proposer-Accepter Duality](#pyq-3-puzzle-6c-the-on2-uniqueness-test-via-proposer-accepter-duality)
7. [The Twisted Exam Question Bank (Advanced Variations & Traps)](#7-the-twisted-exam-question-bank-advanced-variations--traps)
   - [Variation 1: The Absolute Worst-Case Upper Bound ($n^2 - n + 1$ Proposals)](#variation-1-the-absolute-worst-case-upper-bound-n2---n--1-proposals)
   - [Variation 2: Strategic Behavior & Manipulation (Can Participants Lie?)](#variation-2-strategic-behavior--manipulation-can-participants-lie)
   - [Variation 3: Unbalanced Markets ($n \ne m$) & The Rural Hospital Theorem](#variation-3-unbalanced-markets-n-ne-m--the-rural-hospital-theorem)
   - [Variation 4: Incomplete Preference Lists (Unacceptable Partners)](#variation-4-incomplete-preference-lists-unacceptable-partners)
   - [Variation 5: Weak Preferences & Ties (Indifference)](#variation-5-weak-preferences--ties-indifference)
   - [Variation 6: The Stable Roommates Problem (Non-Bipartite Odd Cycles)](#variation-6-the-stable-roommates-problem-non-bipartite-odd-cycles)
   - [Variation 7: The Distributive Lattice of Stable Matchings](#variation-7-the-distributive-lattice-of-stable-matchings)
   - [Variation 8: Many-to-One Matching (Hospital-Resident / College Admissions)](#variation-8-many-to-one-matching-hospital-resident--college-admissions)
   - [Variation 9: Can There Be Exponentially Many Stable Matchings?](#variation-9-can-there-be-exponentially-many-stable-matchings)
8. [The Exam Checklist: Fast Mental Heuristics](#8-the-exam-checklist-fast-mental-heuristics)

---

## 1. The Problem: What is a Stable Matching?

### The Setup
Suppose we have two disjoint sets of equal size $n$:
- A set of $n$ **boys** (proposers): $B = \{b_1, b_2, \dots, b_n\}$
- A set of $n$ **girls** (receivers): $G = \{g_1, g_2, \dots, g_n\}$

Every participant has a **strict, complete, transitive preference ranking** over the members of the opposite sex:
- Each boy ranks all $n$ girls from $1$ (most favorite) to $n$ (least favorite).
- Each girl ranks all $n$ boys from $1$ (most favorite) to $n$ (least favorite).
- No ties or indifference are permitted (for now).

A **matching** $M$ is a bijective function (one-to-one and onto) pairing every boy with exactly one girl, and vice versa:
$$M = \{(b, g) \mid b \in B, g \in G\}$$

---

### What is a Blocking Pair (Instability)?
Suppose society is paired according to matching $M$. Let $M(b)$ denote the wife of boy $b$, and $M(g)$ denote the husband of girl $g$.

> **Definition of a Blocking Pair:**  
> A boy-girl pair $(b, g)$ is called a **blocking pair** (or an **unstable pair**) if they are **not** married to each other in $M$, but **both strictly prefer each other over their assigned spouses**:
> 1. Boy $b$ prefers $g$ over his wife $M(b)$ ($g >_b M(b)$), **AND**
> 2. Girl $g$ prefers $b$ over her husband $M(g)$ ($b >_g M(g)$).

```
   Boy b --- married to ---> Wife M(b)
     \
      \  (Secret mutual preference!)
       \
   Husband M(g) --- married to ---> Girl g
```

If such a pair exists, the marriage market collapses: boy $b$ and girl $g$ have strong incentive to elope together, abandoning their current partners!

> **Definition of a Stable Matching:**  
> A matching $M$ is called **STABLE** if and only if **no blocking pairs exist**.

---

### Why Simple Greedy Matchings Fail
A naive approach would be: *"Take boy 1, match him with his #1 choice. Take boy 2, match him with his best available choice, and so on."*

#### Why does this fail?
Because it completely ignores the girls' preferences!  
- Suppose Boy 1's #1 choice is Girl $A$. Naive matching marries $(1, A)$.
- But Girl $A$ might hate Boy 1 and rank Boy 2 as her #1 choice.
- When Boy 2 is later forced to marry Girl $B$, Boy 2 might prefer Girl $A$ over $B$.
- Now $(2, A)$ forms a **blocking pair**: Boy 2 prefers $A > B$, and Girl $A$ prefers $2 > 1$. The entire matching is unstable!

In 1962, mathematicians **David Gale and Lloyd Shapley** answered the fundamental question: *Does a stable matching always exist for ANY set of preferences? And can we find it efficiently?*  
Their answer was **YES**, earning Lloyd Shapley and Alvin Roth the 2012 Nobel Prize in Economics.

---

## 2. The Gale-Shapley Algorithm (Deferred Acceptance)

### The Core Philosophy
The algorithm is based on **Deferred Acceptance**:
- **Proposals are made optimistically:** Boys propose to girls in decreasing order of preference (best first).
- **Acceptances are tentative (deferred):** Girls never permanently commit right away. A girl holds on to the best proposal she has seen so far, but will immediately dump him if a more desirable boy proposes later!

---

### The High-Level Algorithm

```text
Initialize all boys and all girls as FREE (unmatched).

WHILE there exists a free boy b who still has a girl to propose to:
    g = the highest-ranked girl on b's list to whom b has not yet proposed
    
    b proposes to g:
    
    IF g is currently free:
        (b, g) become tentatively engaged.
    ELSE (g is already tentatively engaged to some boy b'):
        IF g prefers b over b':
            (b, g) become tentatively engaged.
            b' becomes FREE (dumped!).
        ELSE:
            g rejects b.
            b remains FREE.

OUTPUT the tentative engagements as the final matching M.
```

---

### Detailed Step-by-Step Trace (3 Boys & 3 Girls)

Let's trace the instance from **EndSem Puzzle 6(b)**:

**Preferences:**
- **Boys:**
  - Boy 1: $B > A > C$
  - Boy 2: $C > B > A$
  - Boy 3: $A > C > B$
- **Girls:**
  - Girl $A$: $2 > 1 > 3$
  - Girl $B$: $3 > 2 > 1$
  - Girl $C$: $1 > 3 > 2$

#### Execution Trace (Boys Propose):
- **Round 1:** Boy 1 is free. His #1 choice is $B$. Boy 1 proposes to $B$.  
  $B$ is free $\implies$ $(1, B)$ tentatively engaged.
- **Round 2:** Boy 2 is free. His #1 choice is $C$. Boy 2 proposes to $C$.  
  $C$ is free $\implies$ $(2, C)$ tentatively engaged.
- **Round 3:** Boy 3 is free. His #1 choice is $A$. Boy 3 proposes to $A$.  
  $A$ is free $\implies$ $(3, A)$ tentatively engaged.

All boys are matched! The algorithm terminates in **3 proposals**.  
**Final Matching:** $\{(1, B), (2, C), (3, A)\}$.

Notice that every boy got his #1 choice! As we will prove later, this is the **Boy-Optimal** stable matching.

---

## 3. Algorithmic Pseudocode & Data Structures

To achieve an optimal running time of $O(n^2)$, the algorithm requires smart data structures.

### The $O(1)$ Accepter Ranking Matrix Trick
In line 8 of Gale-Shapley, girl $g$ must answer: *"Do I prefer the new proposer $b$ over my current partner $b'$?"*
- If we scan girl $g$'s preference list of size $n$, this comparison takes $O(n)$ time.
- Across $O(n^2)$ proposals, this would yield an inefficient $O(n^3)$ algorithm!

#### The Preprocessing Optimization:
Create an inverse ranking 2D array:
$$\text{ranking}[g][b] = \text{the numerical rank (position) of boy } b \text{ on girl } g\text{'s list}$$
- A smaller rank means higher preference ($\text{rank } 1 > \text{rank } 2$).
- Comparison test in $O(1)$ time:
  $$\text{Girl } g \text{ prefers } b_{\text{new}} \text{ over } b_{\text{curr}} \iff \text{ranking}[g][b_{\text{new}}] < \text{ranking}[g][b_{\text{curr}}]$$

Creating this table takes $\Theta(n^2)$ time once at the start.

---

### Clean Algorithmic Pseudocode

```text
ALGORITHM GaleShapley(n, boy_prefs, girl_prefs):
    // Precompute O(1) ranking matrix for girls
    Initialize ranking[n][n]
    FOR g = 0 TO n - 1:
        FOR rank = 0 TO n - 1:
            b = girl_prefs[g][rank]
            ranking[g][b] = rank

    // State arrays
    Initialize wife[n] filled with -1        // wife[b] = girl currently engaged to b
    Initialize husband[n] filled with -1     // husband[g] = boy currently engaged to g
    Initialize next_proposal[n] filled with 0 // next_proposal[b] = index in boy_prefs[b]

    Initialize free_boys as a FIFO Queue (or Stack) containing all boys 0 to n - 1

    WHILE free_boys is not empty:
        b = free_boys.pop()

        // Get the next girl on b's list
        g = boy_prefs[b][next_proposal[b]]
        next_proposal[b] = next_proposal[b] + 1

        IF husband[g] == -1:
            // Girl g is free
            husband[g] = b
            wife[b] = g
        ELSE:
            current_partner = husband[g]
            IF ranking[g][b] < ranking[g][current_partner]:
                // g prefers b over current_partner
                husband[g] = b
                wife[b] = g
                wife[current_partner] = -1
                free_boys.push(current_partner)  // dumped boy is now free
            ELSE:
                // g rejects b
                free_boys.push(b)                // b remains free

    RETURN wife, husband
```

---

## 4. Complete Production-Grade C++ Implementation

Here is a clean, modern, fully functional C++ implementation. It contains the Gale-Shapley solver, an automated stability verification function (blocking pair detector), and a uniqueness tester.

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <string>
#include <cassert>

using namespace std;

// Structure representing the output of Gale-Shapley
struct MatchingResult {
    vector<int> match_proposer; // match_proposer[p] = assigned receiver
    vector<int> match_receiver; // match_receiver[r] = assigned proposer
    int total_proposals;
};

class StableMarriageSolver {
private:
    int n;
    vector<vector<int>> prop_prefs;  // prop_prefs[p] = list of receivers in order
    vector<vector<int>> recv_prefs;  // recv_prefs[r] = list of proposers in order
    vector<vector<int>> recv_rank;   // recv_rank[r][p] = rank of p in r's list

public:
    StableMarriageSolver(int n, 
                         const vector<vector<int>>& prop_prefs, 
                         const vector<vector<int>>& recv_prefs) 
        : n(n), prop_prefs(prop_prefs), recv_prefs(recv_prefs) {
        
        // Step 1: Precompute inverse ranking matrix for O(1) preference lookups
        recv_rank.assign(n, vector<int>(n, 0));
        for (int r = 0; r < n; ++r) {
            for (int rank = 0; rank < n; ++rank) {
                int p = recv_prefs[r][rank];
                recv_rank[r][p] = rank;
            }
        }
    }

    // Runs Gale-Shapley: Proposers propose to Receivers
    MatchingResult solve() {
        vector<int> match_prop(n, -1);
        vector<int> match_recv(n, -1);
        vector<int> next_prop_idx(n, 0);
        queue<int> free_proposers;

        for (int p = 0; p < n; ++p) {
            free_proposers.push(p);
        }

        int proposals_count = 0;

        while (!free_proposers.empty()) {
            int p = free_proposers.front();
            free_proposers.pop();

            // Next receiver on p's list
            int r = prop_prefs[p][next_prop_idx[p]++];
            proposals_count++;

            if (match_recv[r] == -1) {
                // Receiver r is free
                match_recv[r] = p;
                match_prop[p] = r;
            } else {
                int current_p = match_recv[r];
                // Check if r prefers new proposer 'p' over 'current_p'
                if (recv_rank[r][p] < recv_rank[r][current_p]) {
                    // Receiver switches to p; current_p is dumped
                    match_recv[r] = p;
                    match_prop[p] = r;
                    match_prop[current_p] = -1;
                    free_proposers.push(current_p);
                } else {
                    // Receiver rejects p; p remains free
                    free_proposers.push(p);
                }
            }
        }

        return {match_prop, match_recv, proposals_count};
    }

    // Audits any given matching to determine if it is stable
    // Returns pair<bool, pair<int, int>>: {is_stable, {blocking_p, blocking_r}}
    pair<bool, pair<int, int>> verify_stability(const vector<int>& match_prop) {
        // Build inverse for proposers
        vector<vector<int>> prop_rank(n, vector<int>(n, 0));
        for (int p = 0; p < n; ++p) {
            for (int rank = 0; rank < n; ++rank) {
                prop_rank[p][prop_prefs[p][rank]] = rank;
            }
        }

        // Test every possible pair (p, r)
        for (int p = 0; p < n; ++p) {
            int current_r = match_prop[p];
            for (int r = 0; r < n; ++r) {
                if (r == current_r) continue;

                // Check if p prefers r over current_r
                bool p_prefers_r = (prop_rank[p][r] < prop_rank[p][current_r]);

                // Find who r is married to
                int r_husband = -1;
                for (int i = 0; i < n; ++i) {
                    if (match_prop[i] == r) { r_husband = i; break; }
                }

                // Check if r prefers p over r_husband
                bool r_prefers_p = (recv_rank[r][p] < recv_rank[r][r_husband]);

                if (p_prefers_r && r_prefers_p) {
                    return {false, {p, r}}; // Found blocking pair!
                }
            }
        }
        return {true, {-1, -1}}; // Matching is strictly stable!
    }
};

// Tests whether the instance has EXACTLY ONE unique stable matching
bool has_unique_stable_matching(int n, 
                                const vector<vector<int>>& boy_prefs, 
                                const vector<vector<int>>& girl_prefs) {
    // Run 1: Boys propose to Girls
    StableMarriageSolver solver_boy_prop(n, boy_prefs, girl_prefs);
    MatchingResult M_boy = solver_boy_prop.solve();

    // Run 2: Girls propose to Boys (swap roles)
    StableMarriageSolver solver_girl_prop(n, girl_prefs, boy_prefs);
    MatchingResult M_girl = solver_girl_prop.solve();

    // Compare matchings: M_boy[b] should equal girl where M_girl[g] == b
    for (int b = 0; b < n; ++b) {
        int assigned_girl = M_boy.match_proposer[b];
        // In M_girl, the assigned boy for assigned_girl is M_girl.match_proposer[assigned_girl]
        if (M_girl.match_proposer[assigned_girl] != b) {
            return false; // Mismatch implies multiple stable matchings exist!
        }
    }
    return true; // Matchings are identical -> strictly unique!
}

int main() {
    // Demonstration on EndSem 2025 Puzzle 6(b)
    // 3 Boys (0, 1, 2) and 3 Girls (0=A, 1=B, 2=C)
    // Boy 0: B(1), A(0), C(2)
    // Boy 1: C(2), B(1), A(0)
    // Boy 2: A(0), C(2), B(1)
    vector<vector<int>> boy_prefs = {
        {1, 0, 2},
        {2, 1, 0},
        {0, 2, 1}
    };

    // Girl 0 (A): 1, 0, 2
    // Girl 1 (B): 2, 1, 0
    // Girl 2 (C): 0, 2, 1
    vector<vector<int>> girl_prefs = {
        {1, 0, 2},
        {2, 1, 0},
        {0, 2, 1}
    };

    StableMarriageSolver solver(3, boy_prefs, girl_prefs);
    MatchingResult result = solver.solve();

    cout << "=== Boy-Proposing Gale-Shapley Result ===" << endl;
    for (int b = 0; b < 3; ++b) {
        cout << "Boy " << (b + 1) << " marries Girl " 
             << (char)('A' + result.match_proposer[b]) << "\n";
    }
    cout << "Total proposals: " << result.total_proposals << "\n\n";

    // Verify given EndSem matching: Boy 1-A, Boy 2-B, Boy 3-C
    vector<int> exam_matching = {0, 1, 2}; // 0->A, 1->B, 2->C
    auto [is_stable, blocker] = solver.verify_stability(exam_matching);
    cout << "Exam matching (1-A, 2-B, 3-C) stable? " 
         << (is_stable ? "YES (Stable)" : "NO (Unstable)") << "\n";

    // Check uniqueness
    bool unique = has_unique_stable_matching(3, boy_prefs, girl_prefs);
    cout << "Does this instance have a unique stable matching? " 
         << (unique ? "YES" : "NO (Multiple stable matchings exist)") << "\n";

    return 0;
}
```

---

## 5. The Bedrock Mathematical Proofs (The "Why" Behind Everything)

Exams routinely test the foundational proofs of the algorithm. Here are the clear, elegant proofs you need.

---

### Proof 1: Termination in at most $n^2$ proposals
- **Observation:** In every proposal, a boy proposes to a girl he has never proposed to before.
- A boy never proposes to the same girl twice (his proposal index monotonically increments from $1$ to $n$).
- There are $n$ boys, and each boy can propose to at most $n$ girls.
- **Conclusion:** The total number of proposals across the entire algorithm is strictly bounded by:
  $$\text{Total Proposals} \le n \times n = n^2$$
- Since each while-loop iteration executes exactly one proposal, the algorithm **must terminate in at most $n^2$ steps**. $\blacksquare$

---

### Proof 2: Perfection (Every person gets married)
Could a boy remain unmatched when the algorithm terminates?

- **Fundamental Invariant for Girls:**  
  *Once a girl receives her first proposal, she remains engaged forever after.* She only switches partners if a strictly better proposer arrives.
- Suppose for contradiction that upon termination, some boy $b$ remains **free** (unmarried).
- Since the algorithm terminated, boy $b$ must have proposed to **all $n$ girls** and been rejected by every single one of them.
- But for a girl to reject boy $b$, she must be engaged to someone she strictly prefers.
- This implies that **all $n$ girls are currently engaged**!
- If all $n$ girls are engaged, they must be engaged to $n$ distinct boys.
- But including our free boy $b$, that would require at least $n + 1$ boys! This is a contradiction, since there are only $n$ boys in total.
- **Conclusion:** Every boy is married, and every girl is married. The matching is a **perfect bijection**. $\blacksquare$

---

### Proof 3: Guaranteed Stability (No blocking pairs exist)
Suppose for contradiction that the resulting matching $M$ contains a blocking pair $(b, g)$:
1. Boy $b$ prefers $g$ over his assigned wife $M(b)$ ($g >_b M(b)$).
2. Girl $g$ prefers $b$ over her assigned husband $M(g)$ ($b >_g M(g)$).

Let's trace what happened during the algorithm:
- Since boy $b$ proposes in strictly decreasing order of his preferences, and $g >_b M(b)$, **boy $b$ must have proposed to girl $g$ before he proposed to his eventual wife $M(b)$**.
- What happened when $b$ proposed to $g$?
  - Either $g$ was already engaged to someone she preferred over $b$, OR
  - $g$ tentatively accepted $b$, but dumped him later for someone she preferred even more.
- Because a girl's partner **strictly improves over time** (her sequence of partners is strictly increasing in preference), girl $g$'s final husband $M(g)$ must be at least as good as the boy who displaced $b$.
- Therefore:
  $$M(g) >_g b$$
- This directly contradicts condition (2) that girl $g$ prefers $b$ over $M(g)$!
- **Conclusion:** No blocking pair can exist. The matching is **always stable**. $\blacksquare$

---

### Proof 4: Proposer-Optimality (Boys get their best valid partner)

> **Definition of a Valid Partner:**  
> A girl $g$ is a **valid partner** for boy $b$ if there exists *at least one* stable matching in which $b$ and $g$ are married.

> **Theorem (Boy-Optimality):**  
> Gale-Shapley produces a matching where **every boy gets the best valid partner** he could possibly achieve in ANY stable matching.

#### The Inductive Proof:
Suppose for contradiction that some boy is rejected by a valid partner.  
Let $b$ be the **very first boy** in the execution of the algorithm to be rejected by a valid partner, and let $g$ be that valid partner.

- Girl $g$ rejected $b$ in favor of some boy $b'$, which means:
  $$b' >_g b$$
- Since $g$ is a valid partner for $b$, there exists some stable matching $M^*$ where $(b, g) \in M^*$.
- In $M^*$, boy $b'$ must be married to some girl $g' \ne g$ ($M^*(b') = g'$).
- Now, when was $b'$ accepted by $g$ in Gale-Shapley? At that moment, $b'$ had not yet been rejected by any valid partner (since $b$ was the *first* boy to be rejected by a valid partner).
- Therefore, girl $g$ is at least as good as $b'$'s best valid partner, which means boy $b'$ strictly prefers $g$ over his partner $g'$ in $M^*$:
  $$g >_{b'} g'$$
- But look at pair $(b', g)$:
  1. $b'$ prefers $g$ over his wife $g'$ in $M^*$ ($g >_{b'} g'$).
  2. $g$ prefers $b'$ over her husband $b$ in $M^*$ ($b' >_g b$).
- This means $(b', g)$ forms a **blocking pair for $M^*$**!
- This contradicts the fact that $M^*$ was a stable matching!
- **Conclusion:** No boy is ever rejected by a valid partner. Every boy gets his optimal valid partner. $\blacksquare$

---

### Proof 5: Accepter-Pessimality (Girls get their worst valid partner)

> **Theorem (Girl-Pessimality):**  
> In the boy-proposing Gale-Shapley algorithm, **every girl receives the worst valid partner** she could possibly get in ANY stable matching.

#### Proof:
Suppose for contradiction that in the Gale-Shapley matching $M_B$, some girl $g$ is married to boy $b$, but there exists another stable matching $M^*$ where $g$ is married to a boy $b^*$ whom she prefers **less** than $b$:
$$b >_g b^*$$
- In this other stable matching $M^*$, boy $b$ is married to some girl $g' \ne g$ ($M^*(b) = g'$).
- By **Boy-Optimality**, girl $g$ was boy $b$'s *best* valid partner across all stable matchings.
- Since $M^*$ is a stable matching, boy $b$'s partner $g'$ in $M^*$ cannot be better than his optimal partner $g$. Thus, boy $b$ strictly prefers $g$ over $g'$:
  $$g >_b g'$$
- Now look at pair $(b, g)$ in $M^*$:
  1. $b$ prefers $g$ over his wife $g'$ in $M^*$.
  2. $g$ prefers $b$ over her husband $b^*$ in $M^*$.
- $(b, g)$ forms a **blocking pair for $M^*$**, contradicting the stability of $M^*$!
- **Conclusion:** Every girl receives her worst possible valid partner. $\blacksquare$

---

### Proof 6: Invariance to Proposal Order (The McVitie-Wilson Theorem)
In the while-loop, we pick *any* free boy. Does the choice of which free boy proposes first change the final outcome?

> **Theorem (Order Invariance):**  
> The final stable matching produced by Gale-Shapley is **100% invariant** to the order in which free boys propose (Queue, Stack, or Random). Furthermore, the **exact set of proposals made is identical**!

**Intuition:**  
Every boy is rejected only by girls who are mathematically unavailable to him in any stable matching. The sequence of rejections is determined purely by the underlying preferences, not by the scheduling queue.

---

## 6. Deep Deconstruction of Previous Year Exam Questions

Let us analyze the three exact problems from the EndSem exam and extract the universal principles behind them.

---

### PYQ 1 (Puzzle 6a): Designing Worst-Case Proposal Cascades ($\ge \frac{n(n+1)}{2}$)

> **The Question:** Design preference lists of $n$ boys and $n$ girls such that Gale-Shapley makes at least $\frac{n(n+1)}{2}$ proposals.

#### The Universal Adversarial Strategy: "The Common Nemesis"
To maximize proposals, you must force boys to be rejected as many times as possible before settling down.
- **The Construction:**
  1. **All boys share identical preferences:** Every boy has the exact same ranking over girls:
     $$g_n > g_{n-1} > g_{n-2} > \dots > g_1$$
  2. **Girls rank boys in increasing order of index:** Every girl prefers higher-indexed boys:
     $$b_n > b_{n-1} > \dots > b_1$$

#### Why This Forces a Waterfall Cascade:
- Boy $b_1$ proposes to $g_n$. ($1$ proposal).
- Boy $b_2$ proposes to $g_n$, kicking out $b_1$. $b_1$ falls back to $g_{n-1}$. ($2$ proposals in this round).
- Boy $b_3$ proposes to $g_n$, kicking out $b_2$. $b_2$ falls back to $g_{n-1}$, kicking out $b_1$. $b_1$ falls back to $g_{n-2}$. ($3$ proposals in this round).
- In general, introducing boy $b_k$ triggers a cascade of $k$ proposals!
- Total proposals:
  $$\sum_{k=1}^n k = 1 + 2 + 3 + \dots + n = \frac{n(n+1)}{2}$$

---

### PYQ 2 (Puzzle 6b): Auditing Stability & Discovering Alternative Matchings

> **The Question:** Given 3 boys and 3 girls with preferences, determine if $M_1 = \{(1, A), (2, B), (3, C)\}$ is stable. If stable, find a new stable marriage.

#### The Systematic Audit Algorithm (Fast Exam Technique):
To verify stability by hand in 60 seconds:
1. List each boy and identify the girls he ranks **higher** than his current wife.
2. For each such girl, look up whether she ranks this boy higher than her current husband.
3. If yes $\implies$ Blocking Pair! If no for all $\implies$ **Stable!**

#### Auditing $M_1$:
- Boy 1 is with $A$. Prefers $B$. Does $B$ prefer 1 over her husband 2? Preferences of $B$: $3 > 2 > 1$. **NO** ($2 > 1$).
- Boy 2 is with $B$. Prefers $C$. Does $C$ prefer 2 over her husband 3? Preferences of $C$: $1 > 3 > 2$. **NO** ($3 > 2$).
- Boy 3 is with $C$. Prefers $A$. Does $A$ prefer 3 over her husband 1? Preferences of $A$: $2 > 1 > 3$. **NO** ($1 > 3$).
- **No blocking pairs $\implies M_1$ is STABLE!**

#### Finding Another Stable Matching:
Run Gale-Shapley with **Boys Proposing** $\implies M_2 = \{(1, B), (2, C), (3, A)\}$.  
In $M_2$, every boy gets his #1 choice, so no boy can ever want to deviate! $M_2$ is trivially stable and distinct from $M_1$.  
*(Bonus: Running Gale-Shapley with **Girls Proposing** gives a third stable matching $M_3 = \{(1, C), (2, A), (3, B)\}$, where every girl gets her #1 choice!)*

---

### PYQ 3 (Puzzle 6c): The $O(n^2)$ Uniqueness Test via Proposer-Accepter Duality

> **The Question:** Devise an efficient algorithm using the Gale-Shapley subroutine to check whether an instance has **exactly one** stable matching.

#### The Solution:
- Run Gale-Shapley with **Boys Proposing** $\implies M_B$ (Boy-Optimal, Girl-Pessimal).
- Run Gale-Shapley with **Girls Proposing** $\implies M_G$ (Girl-Optimal, Boy-Pessimal).
- **The Core Theorem:**  
  $$\text{Unique Stable Matching} \iff M_B \equiv M_G$$
- **Algorithm:** Compare the two matchings in $O(n)$ time.
- **Total Time Complexity:** $2 \times O(n^2) + O(n) = O(n^2)$, which is linear in the input size!

---

## 7. The Twisted Exam Question Bank (Advanced Variations & Traps)

Here are the 9 most likely conceptual twists professors use on exams.

---

### Variation 1: The Absolute Worst-Case Upper Bound ($n^2 - n + 1$ Proposals)

> **Question:** In the lecture, we showed a cascade of $\frac{n(n+1)}{2}$ proposals. But what is the **absolute worst-case number of proposals** possible in Gale-Shapley for $n$ boys and $n$ girls? Can it reach $n^2$? Can you construct an instance achieving the exact maximum?

#### The Analysis:
- The absolute maximum is **NOT $n^2$**!
- **Theorem:** The maximum possible number of proposals in Gale-Shapley is:
  $$\mathbf{n^2 - n + 1}$$

#### Why not $n^2$?
- For a boy to make $n$ proposals, he must be rejected by $n - 1$ girls and accept the $n^{\text{th}}$ girl.
- However, the algorithm terminates the instant the **last free boy** finds an available partner!
- At that final step, $n - 1$ girls are already engaged to the other $n - 1$ boys. Exactly one girl is still single!
- Therefore, the last boy can be rejected by at most $n - 1$ girls before landing on the single girl.
- The other $n - 1$ boys can be rejected by at most $n - 1$ girls each.
- Maximum possible rejections: $(n - 1) \times (n - 1) = n^2 - 2n + 1$.
- Total proposals = Successful proposals ($n$) + Rejections ($n^2 - 2n + 1$) = $n^2 - n + 1$.

#### Concrete Construction for $n = 3$ ($3^2 - 3 + 1 = 7$ proposals):
- **Boys:**
  - $b_1: g_1 > g_2 > g_3$
  - $b_2: g_2 > g_1 > g_3$
  - $b_3: g_1 > g_2 > g_3$
- **Girls:**
  - $g_1: b_2 > b_3 > b_1$
  - $g_2: b_1 > b_2 > b_3$
  - $g_3: b_1 > b_2 > b_3$
- Tracing this instance yields **exactly 7 proposals**!

---

### Variation 2: Strategic Behavior & Manipulation (Can Participants Lie?)

> **Question:**  
> 1. Can a **boy** get a better partner by lying about his preference list in boy-proposing Gale-Shapley?  
> 2. Can a **girl** get a better partner by lying about her preference list?

#### Verdict:
1. **Boys CANNOT cheat:** **Truth-telling is a weakly dominant strategy for proposers.**  
   *(Dubins-Freedman & Roth Theorem)*: No boy (or coalition of boys) can achieve a strictly better partner in boy-proposing Gale-Shapley by submitting a falsified ranking.
2. **Girls CAN cheat (Manipulability of Deferred Acceptance):**  
   Receivers can strategically misreport their preferences to obtain a strictly superior partner!

#### The Girl Truncation Strategy (Counterexample):
Consider 2 boys and 2 girls:
- Boy 1: $g_1 > g_2$
- Boy 2: $g_2 > g_1$
- Girl 1: $b_2 > b_1$
- Girl 2: $b_1 > b_2$

- **Honest Execution:**
  - Boy 1 proposes to $g_1$ (accepted).
  - Boy 2 proposes to $g_2$ (accepted).
  - Result: $(b_1, g_1)$ and $(b_2, g_2)$. Girl 1 gets her **worst** choice ($b_1$)!
- **Strategic Lie by Girl 1:**
  - Girl 1 **lies** and declares that $b_1$ is completely unacceptable (she truncates her list to only $b_2$).
  - Now: Boy 1 proposes to $g_1 \implies$ **Rejected!**
  - Boy 1 is forced to propose to his second choice, $g_2$!
  - Girl 2 prefers $b_1 > b_2$, so $g_2$ **dumps Boy 2** and accepts Boy 1!
  - Boy 2 is now free, and proposes to his second choice, $g_1$!
  - Girl 1 accepts Boy 2!
  - **Result:** Girl 1 gets $b_2$ (her #1 choice!). By lying, Girl 1 improved her outcome from worst to best!

---

### Variation 3: Unbalanced Markets ($n \ne m$) & The Rural Hospital Theorem

> **Question:** Suppose there are $n$ boys and $m$ girls with $n > m$.  
> 1. How many boys will remain unmarried?  
> 2. Is it possible that boy $b_1$ is unmarried in one stable matching, but married in a different stable matching?

#### The Rural Hospital Theorem:
> **Theorem:** In any marriage market (even with unequal numbers and incomplete preference lists):  
> 1. Exactly the same **number** of individuals are matched in EVERY stable matching.  
> 2. The **exact subset of individuals who remain single is 100% IDENTICAL across all stable matchings!**

- If boy $b_1$ is single in one stable matching, he is **guaranteed to be single in EVERY stable matching**!
- No participant can escape being single by switching to a different stable matching.

---

### Variation 4: Incomplete Preference Lists (Unacceptable Partners)

> **Question:** Suppose participants only list people they find acceptable (they prefer to remain single rather than marry someone not on their list).  
> Does Gale-Shapley still work?

**YES!**
- **Modification:** A boy only proposes to girls on his acceptable list. If he exhausts his list, he stops and remains single. A girl rejects any proposal from a boy not on her acceptable list.
- **Guarantees:** The algorithm terminates, produces a matching that is individually rational (no one is matched to an unacceptable partner), and contains no blocking pairs.

---

### Variation 5: Weak Preferences & Ties (Indifference)

> **Question:** What if a student is indifferent between two colleges ($A \sim B$)? Does a stable matching still exist?

With ties, stability branches into three distinct definitions:
1. **Weak Stability:** A pair $(b, g)$ blocks $M$ only if **both strictly prefer** each other over their spouses.  
   $\implies$ **Always exists!** (Simply break ties arbitrarily and run standard Gale-Shapley).
2. **Strong Stability:** A pair $(b, g)$ blocks $M$ if one strictly prefers and the other is at least indifferent.  
   $\implies$ **May NOT exist!**
3. **Super Stability:** A pair $(b, g)$ blocks $M$ if both are at least indifferent.  
   $\implies$ **May NOT exist!**

---

### Variation 6: The Stable Roommates Problem (Non-Bipartite Odd Cycles)

> **Question:** Suppose $2n$ students must be paired into $n$ dorm rooms of 2 students each (a non-bipartite graph). Every student ranks all other $2n - 1$ students. Does a stable matching always exist?

**NO! (Classic Exam Counterexample)**  
Unlike the bipartite marriage problem, the Stable Roommates problem **does not always have a solution**!

#### The 4-Student Odd Cycle Counterexample:
Consider 4 roommates $\{A, B, C, D\}$ with preferences:
- $A: B > C > D$
- $B: C > A > D$
- $C: A > B > D$
- $D: \text{arbitrary (everyone's last choice)}$

Notice the cyclic preferences among $A, B, C$: $A$ prefers $B$, $B$ prefers $C$, $C$ prefers $A$.
Whoever is paired with $D$ will always trigger an elopement with someone in the $\{A, B, C\}$ cycle!
- If $(A, D)$ and $(B, C) \implies A$ and $C$ block (since $C$ prefers $A > B$, and $A$ prefers $C > D$).
- If $(B, D)$ and $(A, C) \implies B$ and $A$ block.
- If $(C, D)$ and $(A, B) \implies C$ and $B$ block.

**No stable matching exists!**  
*(Robert Irving developed an $O(n^2)$ algorithm in 1985 that determines whether a stable roommate matching exists and finds one if it does).*

---

### Variation 7: The Distributive Lattice of Stable Matchings

> **Question:** If an instance has multiple stable matchings, what mathematical structure do they form?

The set of all stable matchings forms a **Distributive Lattice**:
- For any two stable matchings $M_1$ and $M_2$:
  - **The Join ($M_1 \vee M_2$):** Give each boy the partner he prefers between $M_1(b)$ and $M_2(b)$.  
    $\implies$ **Guaranteed to be a valid stable matching!**
  - **The Meet ($M_1 \wedge M_2$):** Give each boy the partner he likes less between $M_1(b)$ and $M_2(b)$ (which corresponds to giving each girl her preferred partner!).  
    $\implies$ **Guaranteed to be a valid stable matching!**
- The top element of the lattice is the **Boy-Optimal matching** ($M_B$).
- The bottom element of the lattice is the **Girl-Optimal matching** ($M_G$).

---

### Variation 8: Many-to-One Matching (Hospital-Resident / College Admissions)

> **Question:** In medical residency matching (the National Resident Matching Program, NRMP), each hospital has a capacity $c_h > 1$ to admit multiple doctors. How is Gale-Shapley modified?

- Doctors propose to hospitals one by one.
- A hospital holds up to $c_h$ doctors.
- If a hospital is at capacity and receives a proposal from a better doctor, it rejects its least preferred currently held doctor.
- **Result:** Produces a doctor-optimal, stable many-to-one matching in $O(N \cdot H)$ time!

---

### Variation 9: Can There Be Exponentially Many Stable Matchings?

> **Question:** Can an instance of size $n$ have an exponential number of distinct stable matchings?

**YES!**  
By constructing independent sub-blocks of 2 boys and 2 girls that each have 2 distinct stable matchings, one can create an instance with $n$ pairs that has:
$$2^{n/2} \text{ distinct stable matchings!}$$

---

## 8. The Exam Checklist: Fast Mental Heuristics

When answering exam questions on Gale-Shapley, check these 8 facts:

1. **Existence:** A stable matching **always exists** for the two-sided bipartite marriage problem.
2. **Running Time:** $O(n^2)$ using the $O(1)$ inverse ranking matrix; bounded by $n^2 - n + 1$ proposals.
3. **Proposer Advantage:** Proposers get their **best valid partner**; receivers get their **worst valid partner**.
4. **Order Doesn't Matter:** The final matching is invariant to the scheduling order of proposals.
5. **No Proposer Can Cheat:** Proposers cannot gain by misreporting preferences.
6. **Receivers CAN Cheat:** Receivers can truncate their preference lists to get a better partner.
7. **Uniqueness Test:** Run Boy-Proposing and Girl-Proposing. Unique $\iff M_B \equiv M_G$.
8. **Roommates Fallacy:** Non-bipartite pairing (roommates) may have **no stable solution** due to odd preference cycles.
