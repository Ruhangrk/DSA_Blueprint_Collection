# [2195 — Convex Hull](https://cses.fi/problemset/task/2195)

**Platform:** CSES  
**Source:** CSES Problem Set — Geometry  
**Tags:** convex hull, monotone chain, Andrew  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given a set of `n` points in the two-dimensional plane, your task is to determine the convex hull of the points.

### Input

The first input line has an integer `n`: the number of points.

After this, there are `n` lines that describe the points. Each line has two integers `x` and `y`: the coordinates of a point.

You may assume that each point is distinct, and the area of the hull is positive.

### Output

First print an integer `k`: the number of points in the convex hull.

After this, print `k` lines that describe the points. You can print the points in any order. Print all points that lie on the convex hull.

### Constraints

- `3 <= n <= 2e5`
- `-1e9 <= x, y <= 1e9`

### Examples

**Input**
```
6
2 1
2 5
3 3
4 3
4 4
6 3
```

**Output**
```
4
2 1
2 5
4 4
6 3
```

---

## What this problem really is

Build the convex hull including **collinear boundary points** (“print all points that lie on the convex hull”). Output any order / any starting vertex is fine.

## What to use

| Tool | Why |
|------|-----|
| Andrew’s monotone chain | Sort by `x` then `y`; build lower + upper hull |
| Cross product turn test | Keep only left turns (or non-right, to keep collinear) |
| 64-bit cross | Large coordinates |

## Approach

**Root idea:** the hull is the boundary of the smallest convex set containing all points. After sorting left-to-right, walk once for the lower chain and once for the upper, popping while the last turn is a strict right turn (use `<= 0` vs `< 0` depending on whether you keep mid-edge collinear points — here keep them).

**Mental picture:** stretch a rubber band around the nails; every nail on the band stays, including those sitting flat on an edge.

**Steps (monotone chain)**

1. Sort points by `(x, y)`; remove duplicates if any (none promised).
2. Build lower hull: for each point in order, while last two + new make a non-left turn, pop; push.
3. Build upper hull: same from right to left.
4. Concatenate; drop the duplicated endpoints where chains meet.
5. Print `k` and the points.

Graham scan (sort by polar angle) also works; monotone chain is simpler for large `n`.

## Walkthrough (on a sample)

Interior points `(3,3)`, `(4,3)` drop; corners `(2,1),(2,5),(4,4),(6,3)` remain (sample also keeps the hull vertices shown).

## Pitfalls

- Dropping collinear edge points when the statement wants them.  
- Overflow in cross.  
- `O(n^2)` gift wrapping — TLE at `2e5`.  
- Forgetting area is positive (no all-collinear case).

## Complexity

- Time: `O(n log n)`  
- Space: `O(n)`

## Key takeaway

**Convex hull in `O(n log n)` = sort + monotone chain** — keep collinear boundary points when required.
