# [2191 — Polygon Area](https://cses.fi/problemset/task/2191)

**Platform:** CSES  
**Source:** CSES Problem Set — Geometry  
**Tags:** shoelace, polygon  
**Difficulty / Rating:** Easy  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to calculate the area of a given polygon.

The polygon consists of `n` vertices `(x1,y1), (x2,y2), ..., (xn,yn)`. The vertices `(xi,yi)` and `(x{i+1},y{i+1})` are adjacent for `i = 1..n-1`, and the vertices `(x1,y1)` and `(xn,yn)` are also adjacent.

### Input

The first input line has an integer `n`: the number of vertices.

After this, there are `n` lines that describe the vertices. The `i` th such line has two integers `xi` and `yi`.

You may assume that the polygon is simple, i.e., it does not intersect itself.

### Output

Print one integer: `2*a` where the area of the polygon is `a` (this ensures that the result is an integer).

### Constraints

- `3 <= n <= 1000`
- `-1e9 <= xi, yi <= 1e9`

### Examples

**Input**
```
4
1 1
4 2
3 5
1 4
```

**Output**
```
16
```

---

## What this problem really is

Compute the area of a simple polygon, but output **twice** the area so the answer stays an integer (shoelace without dividing by 2).

## What to use

| Tool | Why |
|------|-----|
| Shoelace formula | Sum of cross products of consecutive edges |
| Absolute value | Vertices may be CW or CCW |
| 64-bit / wider ints | Large coordinates |

## Approach

**Root idea:** the signed area is half the sum over edges of `xi*y{i+1} - x{i+1}*yi` (with `x{n+1}=x1`). Twice the area is exactly that absolute sum.

**Mental picture:** each edge contributes a signed trapezoid/triangle from the origin; they cancel outside and leave the polygon.

**Steps**

1. Read points `p[0..n-1]`.
2. `sum = 0`; for `i = 0..n-1`:  
   `sum += p[i].x * p[(i+1)%n].y - p[(i+1)%n].x * p[i].y` (in 64-bit+).
3. Print `abs(sum)` (this is `2*a`).

## Walkthrough (on a sample)

Quad `(1,1)-(4,2)-(3,5)-(1,4)` → shoelace absolute sum `16` → area `8`, printed `16`.

## Pitfalls

- Dividing by 2 (problem wants `2*a`).  
- Forgetting to close the polygon (`n-1` → `0`).  
- Overflow.  
- Floating shoelace — unnecessary.

## Complexity

- Time: `O(n)`  
- Space: `O(n)`

## Key takeaway

**Polygon area via shoelace: print `abs(sum (xi y{i+1} - x{i+1} yi))` = twice the area.**
