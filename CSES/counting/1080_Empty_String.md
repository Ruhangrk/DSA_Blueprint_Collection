# [1080 — Empty String](https://cses.fi/problemset/task/1080)

**Platform:** CSES  
**Source:** CSES Problem Set — Counting Problems  
**Tags:** interval DP, catalan-like  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a string consisting of `n` characters between `a` and `z`.

On each turn, you may remove any two adjacent characters that are equal. Your goal is to construct an empty string by removing all the characters.

In how many ways can you do this?

### Input

The only input line has a string of length `n`.

### Output

Print one integer: the number of ways modulo `1e9+7`.

### Constraints

- `1 <= n <= 500`

### Examples

**Input**
```
aabccb
```

**Output**
```
3
```

---

## What this problem really is

Count sequences of moves that delete adjacent equal pairs until empty (order of deletions matters). If impossible, answer `0`.

## What to use

| Tool | Why |
|------|-----|
| Interval DP `dp[l][r]` | Ways to clear substring `s[l..r]` |
| Even length | Odd length ⇒ impossible globally |
| Split / match ends | Last removal joins two equal chars that became adjacent |

## Approach

**Root idea:** the last move that finishes an interval removes some adjacent equal pair that was created after clearing everything between them. For interval `[l..r]` (inclusive length), if length odd → `0`. Otherwise try all ways to clear by matching `s[l]` with a later equal `s[k]` after clearing `(l+1..k-1)` and clearing `(k+1..r)`, multiplying ways and binomial coefficients of interleaving the two sides’ move sequences.

**Mental picture:** bubble pairs annihilate when they touch; count annihilation histories that wipe the tape.

**How that becomes the method**

1. `n` odd → `0` immediately (each move removes 2).  
2. `dp[i][i-1] = 1` empty.  
3. For length `len = 2,4,...,n`: for each `l`, `r = l+len-1`:  
   - Enumerate `k` where `s[k]==s[l]`, `k-l` odd (so middle has even length), add `dp[l+1][k-1] * dp[k+1][r] * C(moves_left + moves_right, moves_left)` (interleave independent clear sequences).  
4. Answer `dp[0][n-1]`.

(The binomial counts the order of mixing moves from disjoint regions.)

**Data structures:** `dp[n][n]`; binomial `C[n/2][n/2]` mod `1e9+7`.

## Walkthrough (on a sample)

`aabccb` can be cleared in three distinct deletion orders.

## Pitfalls

- Forgetting interleaving binomials (under-count).  
- Allowing odd intervals.  
- `O(n^2)` without the match index loop properly → need `O(n^3)`.

## Complexity

- Time: `O(n^3)`  
- Space: `O(n^2)`

## Key takeaway

**Adjacent-pair deletion ways = interval DP with equal-endpoint splits and move interleaving.**
