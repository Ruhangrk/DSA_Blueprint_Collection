# [3418 — Border Subgrid Count II](https://cses.fi/problemset/task/3418)

**Platform:** CSES  
**Source:** CSES Problem Set — Counting Problems  
**Tags:** grid, border, rectangles  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a grid of letters. Your task is to calculate, for each letter, the number of rectangular subgrids whose border consists of that letter.

### Input

The first line has two integers `n` and `k`: the size of the grid and the number of letters. The letters are the first `k` uppercase letters.

After this, there are `n` lines that describe the grid. Each line has `n` letters.

### Output

Print `k` lines: for each letter, the number of subgrids.

### Constraints

- `1 <= n <= 500`
- `1 <= k <= 26`

### Examples

**Input**
```
5 3
ABBBC
ABABC
ABBBC
ABBBC
CCCCC
```

**Output**
```
11
38
29
```

---

## What this problem really is

For each letter `L`, count rectangles whose perimeter is all `L` (interior free). `n<=500` allows `O(n^3)` approaches.

## What to use

| Tool | Why |
|------|-----|
| Fix top & bottom rows | Both must be fully `L` on `[Lcol..Rcol]` |
| Run lengths | Horizontal segments on top/bottom; vertical on left/right columns |
| Enumerate left/right | Complete the frame check |

## Approach

**Root idea:** choose top row `t` and bottom row `b`. For a width `[c1..c2]`, need: top `[c1..c2]` all `L`, bottom all `L`, and for every row `r` in `(t..b)` the cells `(r,c1)` and `(r,c2)` are `L` (sides). When `t=b`, only the horizontal segment (degenerate rectangle / single row) — borders collapse to that segment all `L`.

**Mental picture:** pick top and bottom rails painted `L`, then pick left/right posts also painted `L` down the sides.

**How that becomes the method**

1. Precompute horizontal runs of `L` on each row; vertical runs on each column.  
2. For each letter, for each `t <= b`:  
   - Build an array of columns where both sides work for all rows `t..b` (or maintain AND of side masks while growing `b`).  
   - Count pairs `c1 <= c2` where top and bottom segments are all `L` and `c1,c2` are valid side columns (if `b>t`; if `b=t`, count all-`L` subsegments on that row).  
3. Add to `ans[L]`.

**Data structures:** run-length tables; temporary boolean `goodCol[n]`; 64-bit answers.

## Walkthrough (on a sample)

More rectangles than squares: `11 / 38 / 29` for `A/B/C`.

## Pitfalls

- Forgetting single-row / single-column frames.  
- Checking interior.  
- `O(n^4)` without strip optimization.

## Complexity

- Time: about `O(k n^3)`  
- Space: `O(n^2)`

## Key takeaway

**Monochrome rectangle border = top/bottom segments + left/right columns all `L`** — fix `t,b` then count widths.
