# [2192 — Point in Polygon](https://cses.fi/problemset/task/2192)

**Platform:** CSES  
**Source:** CSES Problem Set — Geometry  
**Tags:** ray casting, point-in-polygon  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a polygon of `n` vertices and a list of `m` points. Your task is to determine for each point if it is inside, outside or on the boundary of the polygon.

The polygon consists of `n` vertices `(x1,y1), ..., (xn,yn)`. The vertices `(xi,yi)` and `(x{i+1},y{i+1})` are adjacent for `i=1..n-1`, and `(x1,y1)` and `(xn,yn)` are also adjacent.

### Input

The first input line has two integers `n` and `m`: the number of vertices in the polygon and the number of points.

After this, there are `n` lines that describe the polygon. The `i` th such line has two integers `xi` and `yi`.

You may assume that the polygon is simple, i.e., it does not intersect itself.

Finally, there are `m` lines that describe the points. Each line has two integers `x` and `y`.

### Output

For each point, print `"INSIDE"`, `"OUTSIDE"` or `"BOUNDARY"`.

### Constraints

- `3 <= n, m <= 1000`
- `-1e9 <= xi, yi <= 1e9`
- `-1e9 <= x, y <= 1e9`

### Examples

**Input**
```
4 3
1 1
4 2
3 5
1 4
2 3
3 1
1 3
```

**Output**
```
INSIDE
OUTSIDE
BOUNDARY
```

---

## What this problem really is

Classify each query as interior / exterior / on an edge of a simple polygon.

## What to use

| Tool | Why |
|------|-----|
| On-segment + orientation | Detect `BOUNDARY` first |
| Ray casting (crossing number) | Odd crossings → inside |
| Half-open edge rule | Avoid double-counting vertices the ray hits |

## Approach

**Root idea:** Jordan curve — a ray from the query to infinity crosses the boundary oddly iff the start is inside. First rule out “on an edge,” then count proper crossings with a consistent vertex policy.

**Mental picture:** shoot a horizontal ray right from the query. Each time you cross a wall, flip inside/outside. Touching a corner carefully so you don’t flip twice for one vertex.

**Steps (per query `q`)**

1. For each edge `a-b`: if `q` is collinear with `a,b` and inside the segment box → `BOUNDARY`.
2. Else cast ray to `+inf` in `x`. For each edge, if it straddles `q.y` with a half-open rule, e.g.  
   `(a.y <= q.y && q.y < b.y) || (b.y <= q.y && q.y < a.y)`,  
   and the edge crosses strictly to the right of `q` (cross-product / intersection x `> q.x`), toggle a boolean.
3. Odd toggles → `INSIDE`, else `OUTSIDE`.

Use integer arithmetic only.

## Walkthrough (on a sample)

Quad as given: `(2,3)` inside, `(3,1)` outside, `(1,3)` on the left edge → `BOUNDARY`.

## Pitfalls

- Skipping boundary check (ray methods disagree on edges).  
- Counting both edges at a vertex the ray hits.  
- Floating point.  
- Horizontal edges — usually ignored by the half-open y-filter (correct).

## Complexity

- Time: `O(n * m)`  
- Space: `O(n)`

## Key takeaway

**Point-in-polygon: BOUNDARY via on-segment, else ray-cast with a half-open crossing rule — odd = INSIDE.**
