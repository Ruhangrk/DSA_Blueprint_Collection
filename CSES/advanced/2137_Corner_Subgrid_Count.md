# [2137 — Corner Subgrid Count](https://cses.fi/problemset/task/2137)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Techniques  
**Tags:** bitset, enumeration, grid  
**Difficulty / Rating:** Medium–Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an `n x n` grid whose each square is either black or white. A subgrid is called beautiful if its height and width is at least two and all of its corners are black. How many beautiful subgrids are there within the given grid?

### Input

The first input line has an integer `n`: the size of the grid.

Then there are `n` lines describing the grid: `1` means that a square is black and `0` means that a square is white.

### Output

Print the number of beautiful subgrids.

### Constraints

- `1 <= n <= 3000`

### Examples

**Input**
```
5
00010
11111
00110
11001
00010
```

**Output**
```
4
```

---

## What this problem really is

Count unordered pairs of distinct rows that share at least two black columns — for each such pair with `c` shared black columns, add `C(c, 2)` (each column pair forms one beautiful subgrid with those two rows).

## What to use

| Tool | Why |
|------|-----|
| `bitset<N>` per row | Fast AND of two rows’ black masks |
| `C(c,2) = c*(c-1)/2` | All ways to pick two corner columns |
| 64-bit accumulator | Up to `~n^2 * n^2` scale in theory; fits in 64-bit here |

## Approach

**Root idea:** a beautiful subgrid is fully determined by choosing two distinct rows and two distinct columns where all four intersections are black. So fix the row pair, count common black columns `c`, add `c*(c-1)/2`.

**Mental picture:** stack two rows’ black dots. Every way to pick two columns that are black in **both** rows draws a rectangle with black corners.

**How that becomes the method**

1. Read the grid; for row `i`, set `bs[i][j] = 1` if cell `(i,j)` is black.
2. `ans = 0`.
3. For all `i < j`: `c = (bs[i] & bs[j]).count(); ans += c * (c - 1) / 2`.
4. Print `ans`.

**Data structures:** `bitset<3000> bs[3000]` (or `n` bitsets of size `n`).

## Walkthrough (on a sample)

Several row pairs share exactly two (or more) black columns; summing `C(c,2)` over pairs yields `4`.

## Pitfalls

- Using slow `vector<bool>` or nested column loops without bitsets → TLE at `n=3000`.  
- 32-bit answer.  
- Double-counting ordered row pairs (`i<j` only once).

## Complexity

- Time: `O(n^3 / w)` with word size `w` (bitset AND)  
- Space: `O(n^2 / w)`

## Key takeaway

**Count black-corner subgrids = for every row pair, `C(popcount(and), 2)`** — bitsets make the ANDs fast.
