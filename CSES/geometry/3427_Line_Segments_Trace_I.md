# [3427 — Line Segments Trace I](https://cses.fi/problemset/task/3427)

**Platform:** CSES  
**Source:** CSES Problem Set — Geometry  
**Tags:** Li Chao, convex hull trick, envelope  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` line segments whose endpoints have integer coordinates. The left x-coordinate of each segment is `0` and the right x-coordinate is `m`. The slope of each segment is an integer.

For each x-coordinate `0,1,...,m`, find the maximum point in any line segment.

### Input

The first line has two integers `n` and `m`: the number of line segments and the maximum x-coordinate.

The next `n` lines describe the line segments. Each line has two integers `y1` and `y2`: there is a line segment between points `(0,y1)` and `(m,y2)`.

### Output

Print `m+1` integers: the maximum points for `x=0,1,...,m`.

### Constraints

- `1 <= n, m <= 1e5`
- `0 <= y1, y2 <= 1e9`

### Examples

**Input**
```
4 5
1 6
7 2
5 5
10 0
```

**Output**
```
10 8 6 5 5 6
```

---

## What this problem really is

At every integer `x` in `{0..m}`, evaluate the **upper envelope** of `n` full-width segments (each is an affine line on the whole interval), and report that max height.

## What to use

| Tool | Why |
|------|-----|
| Line form `y = a*x + b` | Integer slope: `a = (y2 - y1) / m`, `b = y1` |
| Li Chao tree on `x` in `[0..m]` | Insert lines, query max at each `x` in `O(log m)` |
| 64-bit arithmetic | Heights up to `~1e9 + 1e5*1e9` scale |

Slope is promised integer, so `(y2 - y1)` is divisible by `m`.

## Approach

**Root idea:** every segment is the restriction of a straight line to `[0,m]`, and here the restriction is the whole domain. So the answer array is just `ans[x] = max over lines of (a_i * x + b_i)`.

**Mental picture:** `n` rubber bands stretched from `x=0` to `x=m`. Walk along the floor from left to right; at each integer step, look up and read the highest band.

**How that becomes the method**

1. For each `(y1,y2)`, set `a = (y2 - y1) / m`, `b = y1` (integer division exact).
2. Build a Li Chao tree over discrete `x` in `[0..m]`: each node stores one candidate line; on insert, keep the better line on the larger half of the node interval and recurse on the other half.
3. For each `x = 0..m`, query the tree at `x` and print.

Convex hull trick / multiset of lines also works if you sort slopes and queries carefully; Li Chao is the direct “insert any line, query any `x`” tool.

**Data structures:** Li Chao nodes with fields `(a, b)` for the stored line; tree size `O(m)` static or `O(n log m)` dynamic.

## Walkthrough (on a sample)

`m=5`. Lines roughly:

- `(0,1)-(5,6)` → rise  
- `(0,7)-(5,2)` → fall  
- `(0,5)-(5,5)` → flat  
- `(0,10)-(5,0)` → steep fall  

At `x=0` max is `10`; then the steep fall and others weave so the printed row is `10 8 6 5 5 6`.

## Pitfalls

- Floating point for heights / slopes.  
- Using `a = (y2-y1)/m` with truncated division when slope is not integer (statement guarantees it is).  
- Naive `O(n*m)` evaluation — TLE.  
- Overflow when evaluating `a*x + b`.

## Complexity

- Time: `O((n + m) log m)`  
- Space: `O(m)` (static Li Chao) or `O(n log m)`

## Key takeaway

**Full-width integer-slope segments → max envelope = Li Chao (or CHT) over lines `a*x+b`**, query every `x`.
