# [3311 — Grid Coloring I](https://cses.fi/problemset/task/3311)

**Platform:** CSES  
**Source:** CSES Problem Set — Introductory Problems  
**Tags:** constructive, graph coloring, grid  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an `n x m` grid where each cell contains one character `A`, `B`, `C` or `D`.

For each cell, you must change the character to `A`, `B`, `C` or `D`. The new character must be different from the old one.

Your task is to change the characters in every cell such that no two adjacent cells have the same character.

### Input

The first line has two integers `n` and `m`: the number of rows and columns.

The next `n` lines each have `m` characters: the description of the grid.

### Output

Print `n` lines each with `m` characters: the description of the final grid.

You may print any valid solution.

If no solution exists, just print `IMPOSSIBLE`.

### Constraints

- `1 <= n, m <= 500`

### Examples

**Input**
```
3 4
AAAA
BBBB
CCDD
```

**Output**
```
CDCD
DCDC
ABAB
```

---

## What this problem really is

Recolor every cell with `{A,B,C,D}`, each cell **must change**, and 4-adjacent neighbors must differ. With 4 colors and a “must differ from old” constraint, a checkerboard-style pattern usually works.

## What to use

| Tool | Why |
|------|-----|
| 2-color tiling idea | Adjacent cells get colors from different pairs |
| Per-cell choice among 2 options | Always avoid the old color |
| 4 colors total | Enough slack that a solution always exists |

## Approach

**Root idea:** think of a checkerboard. On black squares use colors from `{A,B}`, on white squares from `{C,D}` (or any partition into two pairs). Adjacent cells never share a pair, so they never match. For each cell, pick either color of its pair that is **not** equal to the old character (at least one of the two works because the old char is a single letter).

**Mental picture:** paint the board like a chessboard with two palettes; on each square, flip to the other palette color if the first equals the old letter.

**Steps**

1. For each cell `(i,j)`:
   - If `(i+j) % 2 == 0`: candidates `A,B` (try `A` then `B`, take first `!=` old).
   - Else: candidates `C,D`.
2. Print the grid.
3. (With this scheme you never need `IMPOSSIBLE` — 4 colors + must-change is always solvable this way.)

Alternative: for each cell choose any letter different from old and from already colored up/left neighbors — also works with careful order.

## Walkthrough (on a sample)

Even cells → `C`/`D`, odd → `A`/`B` (or as in sample `CDCD / DCDC / ABAB`): every cell changed from its row of identical letters, and neighbors differ by construction.

## Pitfalls

- Reusing the old character.
- Checkerboard with only 2 colors total — then “must change” can conflict with neighbor constraints.
- Claiming impossible when a 4-color construction works.

## Complexity

- Time: `O(n*m)`  
- Space: `O(n*m)`

## Key takeaway

**Recolor with change + adjacent distinct = checkerboard of two color-pairs**; each cell picks a pair-color ≠ old.
