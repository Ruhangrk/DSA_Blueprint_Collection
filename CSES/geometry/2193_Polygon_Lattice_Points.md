# [2193 — Polygon Lattice Points](https://cses.fi/problemset/task/2193)

**Platform:** CSES  
**Source:** CSES Problem Set — Geometry  
**Tags:** Pick's theorem, shoelace, gcd  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given a polygon, your task is to calculate the number of lattice points inside the polygon and on its boundary. A lattice point is a point whose coordinates are integers.

The polygon consists of `n` vertices `(x1,y1), ..., (xn,yn)`. Adjacent vertices form the edges; first and last are also adjacent.

### Input

The first input line has an integer `n`: the number of vertices.

After this, there are `n` lines that describe the vertices. The `i` th such line has two integers `xi` and `yi`.

You may assume that the polygon is simple, i.e., it does not intersect itself.

### Output

Print two integers: the number of lattice points inside the polygon and on its boundary.

### Constraints

- `3 <= n <= 1e5`
- `-1e9 <= xi, yi <= 1e9`

### Examples

**Input**
```
4
1 1
5 3
3 5
1 4
```

**Output**
```
6 8
```

---

## What this problem really is

Count integer points strictly inside (`I`) and on the boundary (`B`) of a simple lattice polygon — without scanning a bounding box (`n` and coords are huge).

## What to use

| Tool | Why |
|------|-----|
| Shoelace | Exact area `A` (use `2A` as integer) |
| `gcd` on each edge | Lattice points on a segment |
| Pick’s theorem `A = I + B/2 - 1` | Solve for `I` |

## Approach

**Root idea (Pick):** area encodes interior and boundary lattice counts. Compute `B` edge-by-edge, `A` by shoelace, then `I = A - B/2 + 1`.

**Mental picture:** every edge is a lattice segment; `gcd(|dx|,|dy|)` counts how finely the grid dots the edge. Pick relates those dots to the filled area.

**Boundary count**

For edge from `(x1,y1)` to `(x2,y2)`, lattice points **including endpoints** = `gcd(|dx|,|dy|) + 1`.  
Summing over all edges double-counts each vertex once, so

```
B = sum_over_edges gcd(|dx|, |dy|)
```

**Interior**

With shoelace absolute sum `S = 2A` (integer):

```
I = (S - B + 2) / 2
```

(equivalent to `I = A - B/2 + 1`).

**Steps**

1. Compute `S = abs(shoelace sum)`.
2. Sum `gcd` of each edge’s deltas → `B`.
3. `I = (S - B + 2) / 2`.
4. Print `I` and `B`.

## Walkthrough (on a sample)

Sample polygon → `I = 6`, `B = 8`.

## Pitfalls

- Using `gcd + 1` per edge and summing without removing double-counted vertices.  
- Forgetting absolute value on shoelace.  
- Overflow on area.  
- Floating area / Pick with doubles.

## Complexity

- Time: `O(n log COORD)` for gcd  
- Space: `O(n)`

## Key takeaway

**Lattice inside/boundary = Pick’s theorem:** `B = sum gcd(|dx|,|dy|)`, `I = A - B/2 + 1`.
