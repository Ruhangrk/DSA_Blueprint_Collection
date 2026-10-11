# [3410 — Maximum Manhattan Distances](https://cses.fi/problemset/task/3410)

**Platform:** CSES  
**Source:** CSES Problem Set — Geometry  
**Tags:** Manhattan distance, coordinate transform  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A set is initially empty and `n` points are added to it. Calculate the maximum Manhattan distance of two points after each addition.

### Input

The first line has an integer `n`: the number of points.

The following `n` lines describe the points. Each line has two integers `x` and `y`. You can assume that each point is distinct.

### Output

After each addition, print the maximum distance.

### Constraints

- `1 <= n <= 2e5`
- `-1e9 <= x, y <= 1e9`

### Examples

**Input**
```
5
1 1
3 2
2 4
2 1
4 5
```

**Output**
```
0
3
4
4
7
```

---

## What this problem really is

Online: after each inserted point, report `max |xi-xj| + |yi-yj|` over the current set. After one point the answer is `0`.

## What to use

| Tool | Why |
|------|-----|
| Identity `\|dx\|+\|dy\| = max(\|(x+y)-(x'+y')\|, \|(x-y)-(x'-y')\|)` | Manhattan → Chebyshev in rotated coords |
| Running min/max of `x+y` and `x-y` | Extreme pairs only |

## Approach

**Root idea:** in coordinates `u = x+y`, `v = x-y`, Manhattan distance equals Chebyshev `max(|du|, |dv|)`. The maximum over the set is therefore simply `max(max_u - min_u, max_v - min_v)`.

**Mental picture:** rotate the plane 45°. Diamond balls become squares; the farthest pair sits on opposite faces of the axis-aligned bounding box in `(u,v)`.

**Steps**

1. Initialize `minU=minV=+inf`, `maxU=maxV=-inf`, `ans=0`.
2. For each new `(x,y)`:
   - `u = x+y`, `v = x-y`.
   - Update mins/maxes of `u` and `v`.
   - `ans = max(maxU - minU, maxV - minV)` (after first point this is `0`).
   - Print `ans`.

## Walkthrough (on a sample)

After `(1,1)` → `0`.  
Add `(3,2)`: `|2|+|1|=3`.  
… eventually `(1,1)` vs `(4,5)` → `3+4=7`.

## Pitfalls

- Recomputing all pairs each insertion — TLE.  
- Tracking only `x` and `y` ranges (Euclidean / wrong metric).  
- 32-bit overflow on `x+y`.

## Complexity

- Time: `O(n)`  
- Space: `O(1)`

## Key takeaway

**Max Manhattan = max range of `x+y` and of `x-y`** — maintain four extrema online.
