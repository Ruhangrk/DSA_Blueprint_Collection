# [3417 — Border Subgrid Count I](https://cses.fi/problemset/task/3417)

**Platform:** CSES  
**Source:** CSES Problem Set — Counting Problems  
**Tags:** grid, border, squares  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a grid of letters. Your task is to calculate, for each letter, the number of square subgrids whose border consists of that letter.

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
ABABC
ABBBC
ABBBC
CCCCC
```

**Output**
```
5
14
9
```

---

## What this problem really is

For each letter `L`, count squares whose **perimeter** is all `L` (interior may be anything). `1×1` squares count if that cell is `L`.

## What to use

| Tool | Why |
|------|-----|
| Prefix / run lengths on rows & cols | Check four sides quickly |
| Fix top-left + side | Or fix top and bottom rows |
| `O(n^2)` per letter goal | `n=3000` tight |

## Approach

**Root idea:** a square with top row `r`, left col `c`, side `s` is good for `L` iff the four sides (top, bottom, left, right segments of length `s`) are all `L`. Precompute consecutive-`L` runs so each side is an `O(1)` range check.

**Mental picture:** stamp square frames on the grid; only the frame must be painted `L`.

**How that becomes the method**

1. For letter `L`, build `rowOk[r][c1][c2]` implicitly via `next_not_L` / prefix equals: horizontal segment `[c..c+s)` on row `r` is all `L` iff run-length from `c` is `>= s`.  
2. Same for vertical segments.  
3. For each top-left `(r,c)` and each feasible `s`, test four sides in `O(1)`; add to `ans[L]`.  
4. Speed: for fixed top and bottom rows both equal to `L` on matching columns, count valid widths — or for each `(r,c)` grow `s` while sides stay valid (amortized).

**Data structures:** horizontal/vertical run lengths `hRun[r][c]`, `vRun[r][c]`; `ans[k]` 64-bit.

## Walkthrough (on a sample)

Many `B` frames and some `A`/`C` frames give `5 / 14 / 9`.

## Pitfalls

- Requiring the interior filled (Filled Subgrid).  
- Double-counting corners (still must be `L`, checked on two sides).  
- `O(n^3)` without run-length `O(1)` side tests.

## Complexity

- Time: `O(k n^2)` with careful enumeration  
- Space: `O(n^2)`

## Key takeaway

**Monochrome square border = four side segments all `L`** — precompute runs for `O(1)` checks.
