# [2423 — Filling Trominos](https://cses.fi/problemset/task/2423)

**Platform:** CSES  
**Source:** CSES Problem Set — Construction Problems  
**Tags:** construction, tiling, tromino  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to fill an `n x m` grid using L-trominos (three squares that have an L-shape).

### Input

The first input line has an integer `t`: the number of tests.

After that, there are `t` lines that describe the tests. Each line has two integers `n` and `m`.

### Output

For each test, print `YES` if there is a solution, and `NO` otherwise.

If there is a solution, also print `n` lines that each contain `m` letters between `A`–`Z`. Adjacent squares must have the same letter exactly when they belong to the same tromino. You can print any valid solution.

### Constraints

- `1 <= t <= 100`
- `1 <= n, m <= 100`

### Examples

**Input**
```
2
4 6
4 7
```

**Output**
```
YES
AADDBB
ACCDEB
BCAEEC
BBAACC
NO
```

---

## What this problem really is

Tile a rectangle with L-trominos (area 3) and output a letter coloring that marks tiles; or report impossible.

## What to use

| Tool | Why |
|------|-----|
| Area rule | `n*m` must be divisible by `3` |
| Parity / mod 2 | Some thin rectangles still impossible (e.g. `1 x m`) |
| Local patterns | Fill with 2×3 / 3×2 blocks of two trominos |

## Approach

**Root idea:** necessary `n*m % 3 == 0`. Also both dimensions `>=2` except tiny cases — a `1×3k` strip cannot hold an L. When possible, cover with `2×3` rectangles (each takes two L’s) and handle leftover `3×3` / `3×k` bands with fixed templates.

**Mental picture:** stamp the board with 2×3 bricks, each brick split into two L’s painted with distinct letters; fix leftover stripes with known tromino layouts.

**How that becomes the method**

1. If `n*m % 3 != 0` → `NO`. If `min(n,m)==1` → `NO` (unless area 0). If `min==2` and `max%3!=0` maybe still no — check: `2×3` works; `2×6` works; `2×m` needs `m%3==0`.  
2. Assume `n<=m`. Tile using:
   - For even `n`: stack horizontal `2×3` pair-tilings across.  
   - For multiples of 3: use vertical `3×2` patterns.  
3. Assign letters so each tromino gets one letter; change letters so adjacent different trominos differ (cycle `A..Z`).  
4. Print `YES` and the grid.

**Data structures:** `char grid[n][m]`.

## Walkthrough (on a sample)

`4×6` area 24 divisible by 3, both sides ≥2 → tilable. `4×7` area 28 not divisible by 3 → `NO`.

## Pitfalls

- Only checking area (missing `1×3`).  
- Same letter on adjacent different trominos.  
- Overwriting letters inconsistently inside one L.

## Complexity

- Time: `O(n m)` per test  
- Space: `O(n m)`

## Key takeaway

**L-tromino rectangle = area ÷3 plus both sides allow L’s**; fill with `2×3` / `3×2` stamps and letter each tile.
