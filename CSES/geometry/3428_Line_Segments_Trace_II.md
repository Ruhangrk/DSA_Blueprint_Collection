# [3428 — Line Segments Trace II](https://cses.fi/problemset/task/3428)

**Platform:** CSES  
**Source:** CSES Problem Set — Geometry  
**Tags:** Li Chao, segment tree, upper envelope  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` line segments whose endpoints have integer coordinates. Each x-coordinate is between `0` and `m`. The slope of each segment is an integer.

For each x-coordinate `0,1,...,m`, find the maximum point in any line segment. If there is no segment at some point, the maximum is `-1`.

### Input

The first line has two integers `n` and `m`: the number of line segments and the maximum x-coordinate.

The next `n` lines describe the line segments. Each line has four integers `x1`, `y1`, `x2` and `y2`: there is a line segment between points `(x1,y1)` and `(x2,y2)`.

### Output

Print `m+1` integers: the maximum points for `x=0,1,...,m`.

### Constraints

- `1 <= n, m <= 1e5`
- `0 <= x1 < x2 <= m`
- `0 <= y1, y2 <= 1e9`

### Examples

**Input**
```
4 5
1 1 3 3
1 2 4 2
2 4 5 7
2 8 5 2
```

**Output**
```
-1 2 8 6 6 7
```

---

## What this problem really is

Upper envelope of **partial** segments on `{0..m}`: each line only exists on its `[x1, x2]`. Where nothing covers `x`, answer `-1`.

## What to use

| Tool | Why |
|------|-----|
| Affine form on a segment | `y = a*x + b` with integer slope `a = (y2-y1)/(x2-x1)`, `b = y1 - a*x1` |
| Li Chao on a segment tree | Insert a line only on `[x1, x2]`; query max at each `x` |
| Sentinel “empty” | No coverage → `-1` |

## Approach

**Root idea:** Trace I is “all lines live on the whole `[0,m]`”. Here each insert is range-restricted, so you need a structure that can add `a*x+b` on an arbitrary interval and query a point.

**Mental picture:** sticky notes of line equations taped only over some integer columns. At each column, read the highest note; blank column → `-1`.

**How that becomes the method**

1. For segment `(x1,y1)-(x2,y2)`, set `dx = x2-x1`, `a = (y2-y1)/dx` (exact — slope integer), `b = y1 - a*x1`.
2. Build a segment tree over `x` in `[0..m]`. Each node holds a Li Chao (or a single candidate line that is pushed/split like classic Li Chao over that node’s interval).
3. Insert: decompose `[x1,x2]` into `O(log m)` tree nodes; on each full node, Li-Chao-insert the line into that node’s envelope.
4. Query `x`: walk root→leaf, take max evaluation among all lines stored on the path (and treat “no line ever touched this path” as `-1`).

Offline alternative: sweep + Li Chao with adds/removes at endpoints — messier because removals of arbitrary lines are hard; range Li Chao is the clean fit.

**Data structures:** segment tree of Li Chao trees (or one Li Chao with range insert via canonical decomposition); each stored line `(a,b)`.

## Walkthrough (on a sample)

`m=5`. At `x=0` nothing → `-1`. At `x=1`: segments start with heights `1` and `2` → `2`. At `x=2`: also the tall start `8` → `8`. Then envelopes of the rising `(2,4)-(5,7)` and falling `(2,8)-(5,2)` produce `6 6 7` for `x=3,4,5`.

## Pitfalls

- Forgetting `-1` when uncovered.  
- Floating point / non-exact slope division.  
- Inserting on open vs closed ends — endpoints are included (`x1` and `x2` both matter).  
- Evaluating with 32-bit ints.

## Complexity

- Time: `O((n + m) log^2 m)` typical for segment-tree Li Chao  
- Space: `O(n log m)` (dynamic) or similar

## Key takeaway

**Partial-segment upper envelope = Li Chao with range inserts** (segment tree of envelopes); uncovered `x` → `-1`.
