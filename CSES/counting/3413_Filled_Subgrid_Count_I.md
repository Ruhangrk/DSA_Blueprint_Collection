# [3413 — Filled Subgrid Count I](https://cses.fi/problemset/task/3413)

**Platform:** CSES  
**Source:** CSES Problem Set — Counting Problems  
**Tags:** grid, histogram, identical squares  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a grid of letters. Your task is to calculate, for each letter, the number of square subgrids whose each letter is the same.

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
21
10
3
```

---

## What this problem really is

For each letter `L`, count axis-aligned **squares** that are entirely filled with `L` (including `1×1`).

## What to use

| Tool | Why |
|------|-----|
| Per-letter binary grid | Treat `L` as `1`, else `0` |
| DP `h[i][j]` | Height of consecutive `L` upward ending at `(i,j)` |
| Stack / largest square histogram | For each row, count squares whose bottom-right is on that row |

## Approach

**Root idea:** a square of side `s` with bottom-right `(i,j)` exists for letter `L` iff the `s×s` block is all `L`. Maintain upward run lengths; for each row process leftward with a stack to know, for each bottom-right cell, the largest `s` such that the square fits, then add `s` (any side `1..s` works).

**Mental picture:** for one letter, paint only those cells. At each spot ask how large a solid square can sit with corner there; every smaller size counts too.

**How that becomes the method**

1. For each letter `L` independently (or one pass with shared DP keyed by letter):  
2. `up[j] = up[j]+1` if `grid[i][j]==L` else `0`.  
3. Using a monotonic stack on `up` across columns, for each `j` compute the maximum square side ending at bottom-right `(i,j)` (limited by `up[j]` and widths to the left with height `>= s`). Classic: max square side = `min(up[j], left_span, ...)` — or use `dp[i][j] = min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1])+1` on the binary mask (all-`L` square DP).  
4. Add `dp[i][j]` to the answer for `L` (counts all squares with that BR corner).

The `O(1)` local DP on binary grids is enough: `dp = 0` if cell not `L`, else `1+min(of three neighbors)`.

**Data structures:** `dp` row rolling arrays; `ans[k]` as 64-bit.

## Walkthrough (on a sample)

Letter `A` fills large bottom blocks → many squares totaling `21`; `B` and `C` fewer.

## Pitfalls

- Counting rectangles (that is problem II).  
- `O(n^3)` enumerate side lengths.  
- 32-bit overflow of counts.

## Complexity

- Time: `O(k n^2)` or `O(n^2)` with one DP per cell’s letter  
- Space: `O(n)` rolling

## Key takeaway

**Monochrome square count = binary “largest square” DP** — add `dp[i][j]` per letter.
