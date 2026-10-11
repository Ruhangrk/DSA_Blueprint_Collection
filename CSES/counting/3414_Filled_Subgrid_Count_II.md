# [3414 — Filled Subgrid Count II](https://cses.fi/problemset/task/3414)

**Platform:** CSES  
**Source:** CSES Problem Set — Counting Problems  
**Tags:** grid, histogram, identical rectangles  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a grid of letters. Your task is to calculate, for each letter, the number of rectangular subgrids whose each letter is the same.

### Input

The first line has two integers `n` and `k`: the size of the grid and the number of letters. The letters are the first `k` uppercase letters.

After this, there are `n` lines that describe the grid. Each line has `n` letters.

### Output

Print `k` lines: for each letter, the number of subgrids.

### Constraints

- `1 <= n <= 3000`
- `1 <= k <= 26`

### Examples

**Input**
```
5 3
ABBBC
BBBBC
BCAAA
AAAAA
AAAAA
```

**Output**
```
64
24
4
```

---

## What this problem really is

For each letter `L`, count all-`L` **rectangles** (any height/width ≥1).

## What to use

| Tool | Why |
|------|-----|
| Upward heights `h[j]` | Consecutive `L` in column `j` ending at current row |
| Monotonic stack | Largest rectangle in histogram contributions per row |
| Contribution formula | Each bar adds `#` of rectangles with bottom on this row |

## Approach

**Root idea:** fix the bottom row of the rectangle. Then columns form a histogram of how far up the pure-`L` run goes; the number of all-`L` rectangles with that bottom is the number of sub-rectangles inside that histogram (standard stack DP).

**Mental picture:** for letter `A`, each row is a skyline of `A`-towers. Count every axis-aligned box that fits under the skyline and sits on the ground line (current row).

**How that becomes the method**

1. For each letter `L` (or process row-by-row updating heights per letter):  
2. For row `i` from top to bottom: update `h[j]` for columns where cell is `L`.  
3. Monotonic increasing stack of columns; when popping index `p` with height `hp`, with left boundary `L` and right `R`, add the count of rectangles whose minimum height bar is `p` — classic formula `hp * (p-L) * (R-p)` variants for “rectangles in histogram.”  
   Simpler equivalent: for each `j`, the number of rectangles with bottom-right corner’s bottom side on this row ending at column `j` can be maintained with stack in `O(1)` amortized as `sum of widths`.  
4. Accumulate into `ans[L]`.

Known `O(n)` per row stack: maintain for each position the number of valid rectangles ending at that column on this bottom row.

**Data structures:** `h[n]`, stack of indices; `ans[26]` as `long long`.

## Walkthrough (on a sample)

Same grid as I but all rectangles: `A` gets `64`, etc.

## Pitfalls

- Using square DP from problem I.  
- Wrong histogram rectangle formula (off-by-one borders).  
- Overflow.

## Complexity

- Time: `O(k n^2)`  
- Space: `O(n)`

## Key takeaway

**Monochrome rectangle count = per-row histogram of upward runs + stack contribution.**
