# [2181 — Counting Tilings](https://cses.fi/problemset/task/2181)

**Platform:** CSES  
**Source:** CSES Problem Set — Dynamic Programming  
**Tags:** dynamic programming, bitmasks, tiling  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to count the number of ways you can fill an `n x m` grid using `1 x 2` and `2 x 1` tiles.

### Input

The only input line has two integers `n` and `m`.

### Output

Print one integer: the number of ways modulo `1e9+7`.

### Constraints

- `1 <= n <= 10`
- `1 <= m <= 1000`

### Examples

**Input**
```
4 7
```

**Output**
```
781
```

---

## What this problem really is

Count perfect tilings of an `n x m` board by dominoes (`1x2` and `2x1`). `n` is tiny (`<= 10`), `m` can be `1000` → profile / column DP with bitmasks.

## What to use

| Tool | Why |
|------|-----|
| Swap so `n <= m` optional | Keep mask size `2^min(n,m)`; here `n <= 10` already |
| Mask = column occupancy | Bit `r` means cell `(r, col)` already filled by a horizontal domino sticking out from the previous column |
| `dp[col][mask]` | Ways to fill first `col` columns and leave `mask` as “need to be covered from the left” / outgoing profile |
| Generate transitions | DFS/recursion down a column placing vertical / horizontal dominoes |

## Approach

**Root idea:** fill the grid **column by column**. The only information that passes between columns is which rows are already covered by a horizontal domino that started in the previous column (a bitmasks profile).

**Mental picture:** stand at column `c`, top to bottom. For each empty cell: place a vertical domino (covers this row and next in the same column), or start a horizontal domino (covers this cell and the same row in column `c+1`, recorded in the next mask).

**Data structures**

- `dp[m+1][1<<n]` (or two layers `cur`/`nxt`) modulo `1e9+7`.
- Transition list: for each `in_mask`, all compatible `out_mask` with multiplicity, or compute recursively while filling a column.

**Column fill recursion (sketch)**

State inside a column: `(row, cur_mask_in, next_mask_building)`.

- If `row == n`: accept transition `in_mask → next_mask`.
- If bit `row` already set in `cur_mask_in` (cell pre-filled by previous horizontal): skip to `row+1`.
- Else try:
  - vertical domino if `row+1 < n` and that cell also free,
  - horizontal domino: set bit `row` in `next_mask`.

**DP**

```
dp[0][0] = 1
for col = 0 .. m-1:
  for each mask:
    for each transition mask -> nmask:
      dp[col+1][nmask] += dp[col][mask]
```

Answer = `dp[m][0]` (board full, nothing sticking out past the last column).

**Steps**

1. Read `n`, `m` (if you swap dimensions, place the smaller as mask height).
2. Precompute or on-the-fly generate mask transitions.
3. Run column DP.
4. Print `dp[m][0] % MOD`.

## Walkthrough (on a sample)

`n = 4`, `m = 7` → answer **`781`**.

Small check: `2 x 1` has `1` way (one vertical); `2 x 2` has `2` ways (two verticals, or two horizontals). The DP reproduces those base cases before scaling to `4 x 7`.

## Pitfalls

- **Odd total cells** (`n*m` odd) → `0` ways (each domino covers 2).
- **Final mask must be 0** — a horizontal sticking past column `m` is illegal.
- **`n` and `m` swap** — mask must be on the dimension `<= 10`.
- Modulo on every addition.

## Complexity

- Time: roughly `O(m * 2^n * poly(n))` for generating/applying transitions  
- Space: `O(2^n)` with rolling columns

## Key takeaway

**Domino tilings on a long strip:** DP on columns with a bitmask profile for cells waiting to be finished by horizontal tiles.
