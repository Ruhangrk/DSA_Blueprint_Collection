# [2176 — Counting Bishops](https://cses.fi/problemset/task/2176)

**Platform:** CSES  
**Source:** CSES Problem Set — Counting Problems  
**Tags:** combinatorics, DP, chessboard, diagonals  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  
**Editorial:** [cp-algorithms — bishops on a chessboard](https://cp-algorithms.com/combinatorics/bishops-on-chessboard.html)

---

## Problem Statement

Your task is to count the number of ways `k` bishops can be placed on an `n x n` chessboard so that no two bishops attack each other.

Two bishops attack each other if they are on the same diagonal.

### Input

The only input line has two integers `n` and `k`: the board size and the number of bishops.

### Output

Print one integer: the number of ways modulo `1e9+7`.

### Constraints

- `1 <= n <= 500`
- `1 <= k <= n*n`

### Examples

**Input**
```
5 4
```

**Output**
```
2728
```

---

## What this problem really is

Count placements of `k` non-attacking bishops on `n x n`. Bishops on opposite colors never meet, so split by color and combine with a convolution.

## What to use

| Tool | Why |
|------|-----|
| Color split | A bishop stays on one color; black and white placements multiply |
| Diagonal DP | On one color, “rows” after a 45° view are diagonals; at most one bishop per diagonal of each family |
| Convolution | Ways for `i` on black * `(k-i)` on white, sum over `i` |

## Approach

**Root idea:** bishops attack only within the same color. Solve “place `j` non-attacking bishops on one color,” then sum over how many sit on black vs white.

**Mental picture:** paint the board in checkerboard colors. Rotate 45°. Same-color squares line up into strips (diagonals). Two bishops on that color attack iff they share a strip of the other diagonal family — so placing them is like putting at most one piece per strip, with later strips blocked by earlier choices.

**How that becomes the method**

1. Index diagonals of one color by size. Let `cnt[t]` = number of squares on the `t`-th diagonal of that color (classic lengths grow then shrink: `1,2,...,n,...,2,1` with color-dependent shift).
2. DP on one color: `dp[t][j]` = ways to place `j` bishops using the first `t` diagonals of this color.
   - Skip diagonal `t`: `dp[t-1][j]`.
   - Place one bishop on it: `(cnt[t] - (j-1)) * dp[t-1][j-1]` — the previous `j-1` bishops each block one square on the current diagonal.
3. Equivalently process only same-color diagonals with step 2 in the usual `1..2n-1` diagonal numbering (`dp[i]` from `dp[i-2]`).
4. Let `B[j]`, `W[j]` be ways for black / white. Answer `sum_j B[j] * W[k-j]` mod `1e9+7`.
5. If `k > 2*n-1`, answer is `0` (pigeonhole on diagonals).

**Data structures:** two rolling DP arrays of length `k+1` (or `n+1`); factorial-free integer arithmetic mod `1e9+7`.

**Short steps:** build black DP → build white DP → convolve at `k`.

## Walkthrough (on a sample)

`n=5`, `k=4`. Max non-attacking bishops is `2*5-1=9`, so `k=4` is feasible.

On a `5 x 5` board there are 13 black and 12 white squares (or vice versa). After the diagonal DP:

- You get arrays `B[0..5]`, `W[0..5]` (at most one bishop per diagonal; one color has at most `n` usable diagonals in the DP).
- Answer = `B[0]*W[4] + B[1]*W[3] + B[2]*W[2] + B[3]*W[1] + B[4]*W[0]`.
- That sum is `2728` mod `1e9+7`.

Tiny check for `n=1`: only `k=1` works (one square), answer `1`; `k>1` → `0`.

## Pitfalls

- Forgetting the color split and trying to place all `k` on one diagonal family alone.
- Off-by-one in diagonal lengths / which diagonals are black vs white.
- Not handling `k` larger than `2*n-1` (immediate `0`).
- Negative mods when subtracting in related formulas — here the DP only adds, but the convolution must stay in range.

## Complexity

- Time: `O(n * min(k,n))` for each color’s DP, plus `O(k)` convolution → fine for `n<=500`
- Space: `O(k)` (or `O(n)`) with rolling arrays

## Key takeaway

Non-attacking bishops factor by color; each color is a diagonal DP where previous bishops punch holes in the current diagonal.
