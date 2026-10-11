# [3411 — All Manhattan Distances](https://cses.fi/problemset/task/3411)

**Platform:** CSES  
**Source:** CSES Problem Set — Geometry  
**Tags:** Manhattan distance, sorting  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given a set of points, calculate the sum of all Manhattan distances between two point pairs.

### Input

The first line has an integer `n`: the number of points.

The following `n` lines describe the points. Each line has two integers `x` and `y`. You can assume that each point is distinct.

### Output

Print the sum of all Manhattan distances.

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
36
```

---

## What this problem really is

Sum `|xi-xj| + |yi-yj|` over all unordered pairs `{i,j}` (or equivalently half the sum over ordered pairs `i < j`).

## What to use

| Tool | Why |
|------|-----|
| Separability | Sum splits into `sum |xi-xj|` + `sum |yi-yj|` |
| Sort + contribution formula | 1D absolute-difference sums in `O(n log n)` |
| 64-bit integers | Large answer |

## Approach

**Root idea:** Manhattan separates by axis. For a sorted 1D array `a[0] <= ... <= a[n-1]`,

```
sum_{i<j} |a[j]-a[i]| = sum_i a[i] * (2*i - n + 1)
```

(each `a[i]` is added once for each smaller element and subtracted once for each larger).

**Mental picture:** on a line, every gap between consecutive sorted values is crossed by `(left_count * right_count)` pairs — same total via the closed formula above.

**Steps**

1. Collect all `x` and all `y`.
2. Sort each; accumulate `sum_x` and `sum_y` with the formula.
3. Print `sum_x + sum_y`.

## Walkthrough (on a sample)

Five points → pairwise Manhattan sum `36` (matches sample).

## Pitfalls

- Double-counting ordered pairs or forgetting one axis.  
- Overflow.  
- `O(n^2)` pairwise loops.

## Complexity

- Time: `O(n log n)`  
- Space: `O(n)`

## Key takeaway

**Sum of Manhattan distances = 1D abs-diff sum on `x` plus on `y`** — sort each axis and apply `a[i]*(2*i-n+1)`.
