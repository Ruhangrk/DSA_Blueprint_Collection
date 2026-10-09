# [3419 — Mex Grid Construction](https://cses.fi/problemset/task/3419)

**Platform:** CSES  
**Source:** CSES Problem Set — Introductory Problems  
**Tags:** mex, XOR, constructive  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to construct an `n x n` grid where each square has the smallest nonnegative integer that does not appear to the left on the same row or above on the same column.

### Input

The only line has an integer `n`.

### Output

Print the grid according to the example.

### Constraints

- `1 <= n <= 100`

### Examples

**Input**
```
5
```

**Output**
```
0 1 2 3 4
1 0 3 2 5
2 3 0 1 6
3 2 1 0 7
4 5 6 7 0
```

---

## What this problem really is

Fill `g[i][j]` (0-based) with `mex` of all values left in the row and above in the column. Deterministic — one correct grid.

## What to use

| Tool | Why |
|------|-----|
| Closed form `i ^ j` | This mex-row-col grid equals bitwise XOR |
| Or incremental mex | For each cell, mark used left/up values, find smallest missing |
| `n <= 100` | Either approach is fine |

## Approach

**Root idea:** the unique grid defined by that mex rule is `g[i][j] = i XOR j`. You can prove by induction that XOR is exactly the mex of previous row/column XORs (standard identity for this construction).

**Mental picture:** each cell picks the smallest “color” not used earlier on its row or column — and XOR paints the board that way, with zeros on the diagonal.

**Steps**

1. For `i = 0..n-1`, for `j = 0..n-1`, print `i ^ j`.
2. Done.

**Brute verification path:** for each `(i,j)`, put left and up values in a set/bool array, scan from `0` for mex — same result, `O(n^3)` or `O(n^2 * n)` with arrays.

## Walkthrough (on a sample)

`0^0=0`, `0^1=1`, `1^1=0`, `2^3=1`, `4^1=5`, … matches the sample grid.

## Pitfalls

- 1-based XOR (`(i+1)^(j+1)`) — wrong; use 0-based indices.
- Computing mex with only row or only column — need both.

## Complexity

- Time: `O(n^2)`  
- Space: `O(1)` extra if printing XOR directly

## Key takeaway

**Row/column mex grid = `i XOR j`** (0-based) — print that table.
