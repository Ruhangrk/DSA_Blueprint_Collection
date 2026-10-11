# [1078 — Grid Paths II](https://cses.fi/problemset/task/1078)

**Platform:** CSES  
**Source:** CSES Problem Set — Counting Problems  
**Tags:** combinatorics, inclusion-exclusion, lattice paths  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Consider an `n x n` grid whose top-left square is `(1,1)` and bottom-right square is `(n,n)`.

Your task is to move from the top-left square to the bottom-right square. On each step you may move one square right or down. In addition, there are `m` traps in the grid. You cannot move to a square with a trap.

What is the total number of possible paths?

### Input

The first input line contains two integers `n` and `m`: the size of the grid and the number of traps.

After this, there are `m` lines describing the traps. Each such line contains two integers `y` and `x`: the location of a trap.

You can assume that there are no traps in the top-left and bottom-right square. You can also assume that each square has at most one trap.

### Output

Print the number of paths modulo `1e9+7`.

### Constraints

- `1 <= n <= 1e6`
- `1 <= m <= 1000`
- `1 <= y,x <= n`

### Examples

**Input**
```
3 1
2 2
```

**Output**
```
2
```

---

## What this problem really is

Count right/down lattice paths from `(1,1)` to `(n,n)` that avoid a sparse set of forbidden cells. The grid is too big for cell DP; DP only on traps.

## What to use

| Tool | Why |
|------|-----|
| Binomial paths | Empty rectangle `(a,b) -> (c,d)` has `C((c-a)+(d-b), c-a)` paths |
| Sorted traps + IE DP | `n` is `1e6` but `m<=1000` — process obstacles in order |
| Factorials / invfacts | Combinations up to `2e6` |

## Approach

**Root idea:** total paths to the destination minus paths that hit at least one trap — implemented as “paths whose first trap is trap `j`,” summed with a DP on the trap list.

**Mental picture:** every invalid path has a *first* trap it steps on. Group invalid paths by that first trap. Valid paths to the end = unrestricted paths to the end, minus those whose first trap is some trap (equivalently: run the same recurrence with the destination appended as a fake point).

**How that becomes the method**

1. Precompute `fact` / `invfact` up to `2*n`.
2. Paths between `(y1,x1)` and `(y2,x2)` (only right/down, and `y1<=y2`, `x1<=x2`):  
   `ways = C((y2-y1)+(x2-x1), y2-y1)`.
3. Collect points = all traps, then append `(n,n)`. Sort by `(y,x)` (any topological order for “can reach”).
4. Let `dp[i]` = number of paths from `(1,1)` to point `i` that avoid all traps strictly before `i` in the list (i.e. do not visit earlier traps).
5. Recurrence:  
   `dp[i] = C(y_i+x_i-2, y_i-1)`  
   minus `sum_{j<i, y_j<=y_i, x_j<=x_i} dp[j] * C((y_i-y_j)+(x_i-x_j), y_i-y_j)`.
6. Answer = `dp` of the appended destination.

**Data structures:** vector of `m+1` points; `dp[0..m]`; factorial tables size `2n+1`.

## Walkthrough (on a sample)

`n=3`, one trap at `(2,2)`. Points after append: `(2,2)`, `(3,3)`.

- Paths `(1,1)->(2,2)`: need 1 down + 1 right → `C(2,1)=2`. No earlier trap → `dp[trap]=2`.
- Paths `(1,1)->(3,3)` unrestricted: `C(4,2)=6`.
- Subtract those going through the trap first: `dp[trap] * paths(trap->end) = 2 * C(2,1) = 4`.
- `dp[end] = 6 - 4 = 2`.

The two survivors: `(1,1)->(1,2)->(1,3)->(2,3)->(3,3)` and `(1,1)->(2,1)->(3,1)->(3,2)->(3,3)`.

## Pitfalls

- Coordinates are `1`-indexed; empty path count uses `C(dy+dx, dy)` with `dy=y2-y1`, `dx=x2-x1`.
- When subtracting, only use earlier points that can reach the current one (`y_j<=y_i` and `x_j<=x_i`).
- Modular subtraction: add `MOD` before `%`.
- Trap at a cell that is unreachable from start still needs correct binomials (`0` if not monotone-reachable).

## Complexity

- Time: `O(m^2 + n)` for DP + factorial precompute (`m<=1000`)
- Space: `O(n + m)` for facts and points

## Key takeaway

Huge grid + few obstacles → lattice-path binomials + inclusion-exclusion DP on the obstacle list (plus the destination).
