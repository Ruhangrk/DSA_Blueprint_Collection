# [2194 — Minimum Euclidean Distance](https://cses.fi/problemset/task/2194)

**Platform:** CSES  
**Source:** CSES Problem Set — Geometry  
**Tags:** closest pair, divide and conquer, sweep line  
**Difficulty / Rating:** Medium–Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given a set of points in the two-dimensional plane, your task is to find the minimum Euclidean distance between two distinct points.

The Euclidean distance of points `(x1,y1)` and `(x2,y2)` is `sqrt((x1-x2)^2 + (y1-y2)^2)`.

### Input

The first input line has an integer `n`: the number of points.

After this, there are `n` lines that describe the points. Each line has two integers `x` and `y`. You may assume that each point is distinct.

### Output

Print one integer: `d^2` where `d` is the minimum Euclidean distance (this ensures that the result is an integer).

### Constraints

- `2 <= n <= 2e5`
- `-1e9 <= x, y <= 1e9`

### Examples

**Input**
```
4
2 1
4 4
1 2
6 3
```

**Output**
```
2
```

---

## What this problem really is

Classic **closest pair of points** in 2D. Output squared distance to stay integral. `n = 2e5` needs `O(n log n)`, not all-pairs.

## What to use

| Tool | Why |
|------|-----|
| Divide-and-conquer closest pair | Textbook `O(n log n)` |
| Or sweep line + set by `y` | Same complexity; maintain a strip of width `d` |
| Compare squared distances | Avoid `sqrt`; use 128-bit if needed for deltas `~2e9` |

## Approach

**Root idea (D&C):** sort by `x`, split mid. Recursively find best in left and right (`d`). Only pairs that can beat `d` have both points in a vertical strip of width `d` around the mid line. In that strip, sort by `y`; each point only needs to check a constant number of neighbors above it (geometry of packing).

**Mental picture:** after left/right are solved, a thin corridor around the cut can still hide a shorter pair — but points in that corridor can’t all be dense in both directions, so only a few `y`-neighbors matter.

**Steps (sketch)**

1. Sort points by `x` (then `y`).
2. Recursive `solve(L,R)`:
   - Base: brute small ranges.
   - `m` mid; `d = min(solve(L,m), solve(m,R))`.
   - Collect points with `|x - x_mid| < d` (or `d^2` compare carefully), sort by `y`.
   - For each, check next ~7 points in `y`-order; update `d`.
3. Print final `d` as a squared integer.

**Sweep alternative:** sort by `x`; keep an ordered set of active points by `y` within the last `d` in `x`; query `y ± d` window.

## Walkthrough (on a sample)

Points `(2,1)` and `(1,2)` give `dx=1, dy=1` → `d^2 = 2`, which is minimal.

## Pitfalls

- All-pairs `O(n^2)` — TLE.  
- Overflow computing `dx*dx + dy*dy` in 64-bit (`dx` up to `2e9` → need `__int128` or careful).  
- Forgetting the strip / neighbor limit in D&C.  
- Taking sqrt and rounding.

## Complexity

- Time: `O(n log n)`  
- Space: `O(n)`

## Key takeaway

**Closest pair in `O(n log n)` via divide-conquer strip or y-ordered sweep — report `d^2`.**
