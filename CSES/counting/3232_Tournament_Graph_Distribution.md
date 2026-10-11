# [3232 — Tournament Graph Distribution](https://cses.fi/problemset/task/3232)

**Platform:** CSES  
**Source:** CSES Problem Set — Counting Problems  
**Tags:** combinatorics, tournaments, SCC, DP  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A tournament graph is a directed graph where a single directed edge exists between every pair of nodes.

Given `n`, your task is to calculate for each `k = 1..n` the number of tournament graphs that have `n` nodes and `k` strongly connected components.

### Input

The only line has an integer `n`: the number of nodes.

### Output

Print `n` lines: for each `k=1..n` the number of graphs modulo `1e9+7`.

### Constraints

- `1 <= n <= 500`

### Examples

**Input**
```
3
```

**Output**
```
2
0
6
```

---

## What this problem really is

For labeled tournaments on `n` nodes, output the histogram of SCC-counts: how many have exactly `k` strong components, for each `k`.

## What to use

| Tool | Why |
|------|-----|
| Source-SCC decomposition | Every tournament has a unique source SCC; rest is a smaller tournament |
| `S[j]` = #strong tournaments on `j` labels | Building block for the histogram |
| DP `dp[i][c]` | Ways on `i` nodes with `c` SCCs |

## Approach

**Root idea:** the condensation of a tournament is a transitive chain of SCCs. Peel the unique *source* strong component of size `j`, force all edges from that component to the outside, and recurse on the remaining tournament.

**Mental picture:** line up SCCs left → right so every cross edge goes left-to-right. Only the insides of each blob can vary — and each blob must itself be strong.

**How that becomes the method**

1. `T[i] = 2^{i*(i-1)/2} mod MOD` — all tournaments on `i` labeled nodes (`T[0]=1`).
2. Recover strong counts `S[1..n]` from  
   `T[i] = sum_{j=1..i} C(i,j) * S[j] * T[i-j]`  
   (choose the source component set of size `j`, it must be strong, remaining any tournament; cross edges all source→rest). Solve for `S[i]` after smaller values are known.
3. Histogram DP: `dp[0][0] = 1`, and for `i>=1`, `c>=1`:  
   `dp[i][c] = sum_{j=1..i-c+1} C(i,j) * S[j] * dp[i-j][c-1]`
4. Print `dp[n][1]`, …, `dp[n][n]`.

**Data structures:** arrays `T[0..n]`, `S[0..n]`, `dp[0..n][0..n]`; binomial table `C[0..n][0..n]`; modular pow for `T[i]`.

## Walkthrough (on a sample)

`n=3`: `T = [1,1,2,8]`.

- `S[1]=1`
- `T[2]=C(2,1)S[1]T[1] + C(2,2)S[2]T[0]` → `2 = 2 + S[2]` → `S[2]=0` (a 2-node tournament is never strong)
- `T[3]=C(3,1)S[1]T[2] + C(3,2)S[2]T[1] + C(3,3)S[3]T[0]` → `8 = 6 + S[3]` → `S[3]=2` (the two 3-cycles)

Then:

- `dp[3][1] = C(3,3)*S[3]*dp[0][0] = 2`
- `dp[3][2] = 0` (need a strong block of size `2`)
- `dp[3][3] = 6` (transitive tournaments = `3!` linear orders)

## Pitfalls

- Using unordered partitions without the source-component uniqueness (overcount).
- Setting `S[2]=1` by mistake.
- Forgetting cross edges are *determined* (do not multiply by `2^{j*(i-j)}` in this formulation — that factor is already absorbed by fixing direction source→rest).
- `n=500` needs `O(n^2)` DP with careful modops; `O(n^3)` binomial-free loops also OK if tight constants are fine (`500^3` is borderline — prefer precomputed `C` and `O(n^2)` style nested sums).

## Complexity

- Time: `O(n^2)` after `O(n^2)` binomials (or `O(n^3)` naive)
- Space: `O(n^2)` for `dp`

## Key takeaway

Tournament SCCs form a chain; count via strong-tournament numbers `S[j]` and a source-component DP.
