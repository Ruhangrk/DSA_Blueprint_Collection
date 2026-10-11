# [1741 — Area of Rectangles](https://cses.fi/problemset/task/1741)

**Platform:** CSES  
**Source:** CSES Problem Set — Geometry  
**Tags:** sweep line, segment tree, union area  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given `n` rectangles, your task is to determine the total area of their union.

### Input

The first line has an integer `n`: the number of rectangles.

After that, there are `n` lines describing the rectangles. Each line has four integers `x1`, `y1`, `x2` and `y2`: a rectangle begins at point `(x1,y1)` and ends at point `(x2,y2)`.

### Output

Print the total area covered by the rectangles.

### Constraints

- `1 <= n <= 1e5`
- `-1e6 <= x1 < x2 <= 1e6`
- `-1e6 <= y1 < y2 <= 1e6`

### Examples

**Input**
```
3
1 3 4 5
3 1 7 4
5 3 8 6
```

**Output**
```
24
```

---

## What this problem really is

Axis-aligned rectangle **union area** — count each covered cell of the plane once, even if many rectangles overlap.

## What to use

| Tool | Why |
|------|-----|
| Vertical sweep by `x` | Between consecutive event x-coords, coverage is constant |
| Segment tree on `y` | Track how much of the y-axis is currently covered (≥1 rectangle) |
| Cover count + length | Node stores how many opens cover the segment and the covered length |

## Approach

**Root idea:** union area = sum over consecutive sweep positions `dx * (measure of y currently covered)`. Maintain the covered y-measure while processing left/right edges.

**Mental picture:** a vertical laser scanning left→right. Rectangles turn “on” at left edge and “off” at right edge. Between two scan positions, the lit total height times the width gap is the area strip.

**How that becomes the method**

1. Events: at `x1` add interval `[y1,y2)`; at `x2` remove `[y1,y2)`. Sort by `x`.
2. Compress all `y` endpoints (or build a dynamic tree on `[-1e6,1e6]`).
3. Segment tree where each leaf is a y-gap between consecutive compressed coordinates. Each node stores:
   - `cnt` = number of active add-intervals fully covering this node
   - `len` = covered length of this node’s y-range (`full length` if `cnt > 0`, else sum of children)
4. Process events in order of `x`. When `x` advances from `prev` to `cur`, add `(cur - prev) * tree.root_len` to the answer, then apply the adds/removes at `cur`.

Use half-open `[y1,y2)` so shared edges do not double-count.

**Data structures:** event list; segment tree over compressed y with `(cnt, len)`.

## Walkthrough (on a sample)

Three overlapping rectangles. Sweep accumulates strips whose heights reflect merged y-coverage; total union area `24` (less than sum of individual areas).

## Pitfalls

- Double-counting overlaps (naive sum of `w*h`).  
- Closed vs half-open y-intervals at shared boundaries.  
- 32-bit area (`n * 2e6 * 2e6` scale → need 64-bit).  
- Sorting events wrong when several share an `x` (apply updates for one `x` after adding the strip that ends there — standard: add area for gap, then update).

## Complexity

- Time: `O(n log n)`  
- Space: `O(n)`

## Key takeaway

**Rectangle union area = sweep + segment tree of covered y-length**, accumulate `dx * covered_y`.
