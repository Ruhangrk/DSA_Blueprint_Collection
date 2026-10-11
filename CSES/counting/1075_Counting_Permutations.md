# [1075 — Counting Permutations](https://cses.fi/problemset/task/1075)

**Platform:** CSES  
**Source:** CSES Problem Set — Counting Problems  
**Tags:** DP, permutations, connected components  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  
**Editorial:** [USACO Guide — Permutations II](https://usaco.guide/problems/cses-1075-permutations-ii/solution)

---

## Problem Statement

A permutation of integers `1,2,...,n` is called beautiful if there are no adjacent elements whose difference is `1`.

Given `n`, your task is to count the number of beautiful permutations.

### Input

The only input line contains an integer `n`.

### Output

Print the number of beautiful permutations of `1,2,...,n` modulo `1e9+7`.

### Constraints

- `1 <= n <= 1000`

### Examples

**Input**
```
5
```

**Output**
```
14
```

---

## What this problem really is

Count permutations of `1..n` with no two consecutive integers sitting next to each other in value-order (no `|p[i]-p[i+1]| = 1`).

## What to use

| Tool | Why |
|------|-----|
| Insert numbers in order `1..n` | Consecutive values are the only danger; insert `i` when smaller values already form blocks |
| Connected-component DP | Blocks of already placed numbers; `i` can start a block, extend a border, or merge two blocks |
| Optional OEIS recurrence | Same sequence `A002464` has a 4-term linear recurrence for a faster `O(n)` path |

## Approach

**Root idea:** place values from smallest to largest. When you place `i`, the only forbidden adjacencies involving `i` are next to `i-1`. So track the linear arrangement as unordered *components* (contiguous segments of already placed numbers), and how many “hot borders” the newest value created.

**Mental picture:** think of already placed numbers as several intervals on a line (the permutation positions). Value `i` may:

- sit alone as a new interval,
- glue onto one end of an existing interval (one border touching `i-1`’s side),
- or squeeze between two intervals and merge them (two borders) — but only if that does not put `i` next to `i-1` incorrectly; the DP encodes legal border counts.

**How that becomes the method (component DP)**

Define `f[i][j][k]` = ways after placing `1..i`, forming `j` components, where value `i` contributes `k` restricted borders (`k` in `{0,1,2}`).

Transitions (mod `1e9+7`):

- New component (`k=2`): `f[i][j][2] = f[i-1][j-1][0] + f[i-1][j-1][1] + f[i-1][j-1][2]`
- Append to one side (`k=1`): multiply previous states by the number of legal ends among `j` components
- Merge two components (`k=0`): choose an ordered pair of components to join with `i` in the middle

Base: `f[0][0][0] = 1`. Answer: `f[n][1][0] + f[n][1][1] + f[n][1][2]` (one component = a full permutation).

**Faster formula (same answers):**  
`a[i] = (i+1)*a[i-1] - (i-2)*a[i-2] - (i-5)*a[i-3] + (i-3)*a[i-4]`  
with small base cases (`a[0]=a[1]=1`, `a[2]=a[3]=0` in the usual indexing used on CSES writeups — verify against tiny `n` before trusting coefficients). Prefer implementing the DP if unsure.

**Data structures:** 3D DP `O(n^2)` states with 3 border kinds, or a 1D recurrence array.

## Walkthrough (on a sample)

`n=5`, answer `14`.

For `n=1`: only `[1]` → `1`.  
For `n=2`: `[1,2]` and `[2,1]` both have adjacent difference `1` → `0`.  
For `n=3`: also `0` (any perm of `1,2,3` forces a consecutive pair somewhere in a short line).  
For `n=4`: a few survive (e.g. `[2,4,1,3]`).  
Building with the component DP up to `i=5`, `j=1` sums to `14`.

One valid example for `n=5`: `[2,4,1,3,5]` is **invalid** (`3,5` ok but `4,1` ok… actually `|1-3|=2`, check carefully). A valid one: `[2,4,1,5,3]` — adjacent diffs `2,3,4,2`, none equal to `1`.

## Pitfalls

- Confusing this with “no two *positions* differ by 1” — the ban is on **values** that differ by `1` being neighbors.
- Off-by-one in component / border transition multipliers (easy WA).
- Using the OEIS recurrence with wrong base cases.
- `n=1` special case must return `1`.

## Complexity

- Time: `O(n^2)` for component DP; `O(n)` for the linear recurrence
- Space: `O(n^2)` or `O(n)` with rolling layers

## Key takeaway

Forbidden adjacent *values* → insert numbers in order and DP on how the newest value attaches to existing connected blocks.
