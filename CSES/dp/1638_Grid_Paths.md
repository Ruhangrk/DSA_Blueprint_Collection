# [1638 — Grid Paths](https://cses.fi/problemset/task/1638)

**Platform:** CSES  
**Source:** CSES Problem Set — Dynamic Programming  
**Tags:** dynamic programming, grid, path counting  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Consider an `n x n` grid whose squares may have traps. It is not allowed to move to a square with a trap.

Your task is to calculate the number of paths from the upper-left square to the lower-right square. You can only move right or down.

### Input

The first input line has an integer `n`: the size of the grid.

After this, there are `n` lines that describe the grid. Each line has `n` characters: `.` denotes an empty cell, and `*` denotes a trap.

### Output

Print the number of paths modulo `1e9+7`.

### Constraints

- `1 <= n <= 1000`

### Examples

**Input**
```
4
....
.*..
...*
*...
```

**Output**
```
3
```

---

## What this problem really is

Count paths from `(0,0)` to `(n-1,n-1)` on a grid, moving only **right** or **down**, never stepping on `*`. Modulo `1e9+7`.

## What to use

| Tool | Why |
|------|-----|
| 2D DP | Paths to a cell only come from the left or above |
| Trap = zero | A blocked cell contributes no paths |
| Modulo `1e9+7` | Path counts grow fast |

## Approach

**Root idea:** every path that reaches cell `(r, c)` took a last step either from the left `(r, c-1)` or from above `(r-1, c)`. So ways to here = ways from left + ways from above (if those cells exist and are not traps).

Picture filling the grid in reading order. Once the top and left borders are known, each empty cell’s answer is forced:

```
dp[r][c] = 0                          if grid[r][c] is '*'
dp[r][c] = dp[r-1][c] + dp[r][c-1]    otherwise (missing neighbors count as 0)
```

**Base:** if `grid[0][0]` is `.`, set `dp[0][0] = 1` (one way to “be” at the start). If start is `*`, answer is `0`.

**Steps:**

1. Allocate `dp[n][n]`, all `0`.
2. If start is free, `dp[0][0] = 1`.
3. For each cell in order (row by row, left to right):
   - If trap, leave `0`.
   - Else add from above and from left, modulo `1e9+7` (skip the start cell’s self-add).
4. Answer `dp[n-1][n-1]`.

## Walkthrough (on a sample)

`n = 4` (`.` free, `*` trap):

```
. . . .
. * . .
. . . *
* . . .
```

`dp` after filling (traps stay `0`):

```
1 1 1 1
1 0 1 2
1 1 2 0
0 1 3 3
```

- Top row: only from the left → all `1`.
- `(1,1)` trap → `0`, so nothing flows through it.
- `(2,3)` and `(3,0)` traps cut those corridors.
- End cell `(3,3) = from above (0) + from left (3) = 3`.

Answer `3`. If start or end were `*`, that cell’s `dp` would be `0`.

## Pitfalls

- **Start / end trap** — answer `0`; don’t assume `dp[0][0] = 1` blindly.
- **Only right/down** — no left/up; don’t add extra directions.
- **`n = 1000`** → `O(n^2)` time/space is fine; recursion without memo on the grid can TLE / stack-overflow.
- **Modulo** on every addition.

## Complexity

- Time: `O(n^2)`  
- Space: `O(n^2)`, or `O(n)` if you keep only the previous row

## Key takeaway

**Grid path counts = “from left + from above.”** Blocked cells zero out. Same idea as unique paths on a grid, with traps as permanent zeros.
