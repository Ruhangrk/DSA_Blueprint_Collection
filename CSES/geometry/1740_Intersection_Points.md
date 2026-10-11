# [1740 — Intersection Points](https://cses.fi/problemset/task/1740)

**Platform:** CSES  
**Source:** CSES Problem Set — Geometry  
**Tags:** sweep line, Fenwick, axis-aligned segments  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given `n` horizontal and vertical line segments, your task is to calculate the number of their intersection points.

You can assume that no parallel line segments intersect, and no endpoint of a line segment is an intersection point.

### Input

The first line has an integer `n`: the number of line segments.

Then there are `n` lines describing the line segments. Each line has four integers `x1`, `y1`, `x2` and `y2`: a line segment begins at point `(x1,y1)` and ends at point `(x2,y2)`.

### Output

Print the number of intersection points.

### Constraints

- `1 <= n <= 1e5`
- `-1e6 <= x1 <= x2 <= 1e6`
- `-1e6 <= y1 <= y2 <= 1e6`
- `(x1,y1) != (x2,y2)`

### Examples

**Input**
```
3
2 3 7 3
3 1 3 5
6 2 6 6
```

**Output**
```
2
```

---

## What this problem really is

Count crossings between horizontal and vertical segments only. Guarantees kill parallel overlaps and endpoint-on-segment cases, so every counted hit is a proper interior cross of one H with one V.

## What to use

| Tool | Why |
|------|-----|
| Vertical sweep by `x` | Process left ends, verticals, right ends in order |
| Fenwick / segment tree on `y` | Active horizontal segments as points on the `y`-axis |
| Coordinate compression | `y` in `[-1e6,1e6]` but only `O(n)` distinct matter |

## Approach

**Root idea:** a horizontal segment `(xl,y)--(xr,y)` is “alive” on the sweep for all `x` in `(xl, xr)`. A vertical `(x,yb)--(x,yt)` crosses exactly those alive horizontals whose `y` lies strictly between `yb` and `yt`. Sum those range counts over all verticals.

**Mental picture:** a vertical laser scanning left→right. Horizontals appear as dots on a stick when their left end is hit, vanish at their right end. Each vertical queries “how many dots currently sit between my bottom and top?”

**How that becomes the method**

1. Classify each input: if `y1 == y2` → horizontal at height `y1` from `x1` to `x2`; if `x1 == x2` → vertical at `x1` from `y1` to `y2`.
2. Build events: at `x = xl` add `y`; at `x = xr` remove `y`; at vertical’s `x` query `[yb, yt]` (open ends — endpoints are never intersections by statement, so use inclusive or exclusive consistently with that).
3. Sort events by `x`; when `x` ties, process **adds before queries before removes** (or any order that never counts an endpoint as a cross — guarantees already say endpoints are not intersections, so be careful at shared `x`).
4. Fenwick stores multiplicity of active `y` values (compressed). Answer is a 64-bit sum.

**Data structures:** event list `(x, type, y_or_range)`; Fenwick `bit[rank(y)]` = how many active horizontals at that `y`.

## Walkthrough (on a sample)

H: `(2,3)-(7,3)`. V: `x=3` from `y=1..5`, `x=6` from `y=2..6`.

- At `x=2`: add `y=3`.
- At `x=3`: query `y in (1,5)` → finds `3` → `+1`.
- At `x=6`: query `(2,6)` → finds `3` → `+1`.
- At `x=7`: remove `y=3`.

Total `2`.

## Pitfalls

- Wrong event order at the same `x` (counting an endpoint).  
- Treating diagonals — input is only H/V under the constraints’ `x1<=x2`, `y1<=y2` plus equal coords.  
- 32-bit answer overflow (`n` up to `1e5` → up to `~1e10` crossings).  
- Forgetting to compress `y` before Fenwick.

## Complexity

- Time: `O(n log n)`  
- Space: `O(n)`

## Key takeaway

**H–V intersection count = sweep + Fenwick of active horizontal y’s**, query each vertical’s y-range.
