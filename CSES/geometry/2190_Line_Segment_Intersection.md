# [2190 — Line Segment Intersection](https://cses.fi/problemset/task/2190)

**Platform:** CSES  
**Source:** CSES Problem Set — Geometry  
**Tags:** segments, orientation, collinear overlap  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are two line segments: the first goes through the points `(x1, y1)` and `(x2, y2)`, and the second goes through the points `(x3, y3)` and `(x4, y4)`.

Your task is to determine if the line segments intersect, i.e., they have at least one common point.

### Input

The first input line has an integer `t`: the number of tests.

After this, there are `t` lines that describe the tests. Each line has eight integers `x1`, `y1`, `x2`, `y2`, `x3`, `y3`, `x4` and `y4`.

### Output

For each test, print `"YES"` if the line segments intersect and `"NO"` otherwise.

### Constraints

- `1 <= t <= 1e5`
- `-1e9 <= xi, yi <= 1e9`
- `(x1,y1) != (x2,y2)`
- `(x3,y3) != (x4,y4)`

### Examples

**Input**
```
5
1 1 5 3 1 2 4 3
1 1 5 3 1 1 4 3
1 1 5 3 2 3 4 1
1 1 5 3 2 4 4 1
1 1 5 3 3 2 7 4
```

**Output**
```
NO
YES
YES
YES
YES
```

---

## What this problem really is

Closed segment intersection (touching endpoints / overlapping collinear pieces count as yes). Purely combinatorial — stay in integers.

## What to use

| Tool | Why |
|------|-----|
| Orientation / cross product | General proper crossing |
| `on_segment` bounding-box test | Collinear endpoint-on-segment and overlap |
| Wide integers | Avoid overflow |

## Approach

**Root idea:** segments `AB` and `CD` properly cross if `C` and `D` are on opposite sides of line `AB` **and** `A` and `B` are on opposite sides of line `CD`. Degenerate cases (endpoint on the other segment, or collinear overlap) need an explicit on-segment check.

**Mental picture:** each segment as a fence. Proper cross = each fence splits the other’s endpoints. Touching tip-to-tip or lying on top of each other still counts.

**Helpers**

```
orient(a,b,c) = cross(b-a, c-a)   // sign: left/right/collinear of c vs directed ab
on_segment(a,b,p) = (p in axis-aligned box of a,b) when collinear
```

**Steps**

1. Compute `o1 = sign(orient(A,B,C))`, `o2 = sign(orient(A,B,D))`, `o3 = sign(orient(C,D,A))`, `o4 = sign(orient(C,D,B))`.
2. If any of `C,D` is collinear with `AB` and lies on segment `AB` → `YES` (same for `A,B` on `CD`).
3. Else if `o1*o2 < 0` and `o3*o4 < 0` → `YES`.
4. Else `NO`.

(Fully collinear overlap: the on-segment checks / projection overlap handle it.)

## Walkthrough (on a sample)

Shared endpoint `(1,1)` → `YES`. Proper X-cross → `YES`. Parallel disjoint → `NO`.

## Pitfalls

- Ignoring collinear overlap / endpoint touch.  
- Floating point.  
- Overflow in cross products.  
- Treating infinite lines instead of segments.

## Complexity

- Time: `O(t)`  
- Space: `O(1)`

## Key takeaway

**Segment intersection = opposite-side orientations + collinear on-segment cases** — closed segments, integer cross only.
