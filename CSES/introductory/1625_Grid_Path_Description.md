# [1625 — Grid Path Description](https://cses.fi/problemset/task/1625)

**Platform:** CSES  
**Source:** CSES Problem Set — Introductory Problems  
**Tags:** backtracking, pruning, grid paths  
**Difficulty / Rating:** Hard (for introductory)  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `88418` paths in a `7 x 7` grid from the upper-left square to the lower-left square. Each path corresponds to a 48-character description consisting of characters `D` (down), `U` (up), `L` (left) and `R` (right).

You are given a description of a path which may also contain characters `?` (any direction). Your task is to calculate the number of paths that match the description.

### Input

The only input line has a 48-character string of characters `?`, `D`, `U`, `L` and `R`.

### Output

Print one integer: the total number of paths.

### Examples

**Input**
```
??????R??????U??????????????????????????LD????D?
```

**Output**
```
201
```

---

## What this problem really is

Count self-avoiding walks of length `48` on a `7x7` grid from `(0,0)` to `(6,0)` that respect the pattern (fixed letters or `?`). Brute `4^48` is impossible — need heavy pruning.

## What to use

| Tool | Why |
|------|-----|
| DFS / backtracking | Explore walks step by step |
| Visited grid | Paths cannot revisit a cell |
| Pattern constraint | At step `t`, move must match `s[t]` (or any if `?`) |
| Pruning rules | Cut dead partial paths early (critical for TL) |

## Approach

**Root idea:** recurse on `(r, c, step)`. If `step == 48`, accept iff at `(6,0)`. Otherwise try the allowed directions, mark visited, recurse, unmark.

**Mental picture:** a snake filling the board in 48 steps into the bottom-left corner; `?` are free wiggles, letters are forced turns; abandon routes that trap empty regions.

**Important prunes** (without these, TLE):

1. **Early destination:** if you reach `(6,0)` before step 48 → dead (must use all moves).
2. **Split / wall hits:** if the walk hits a boundary or visited cell in a way that splits the remaining unvisited cells into disconnected regions the path can’t fill — common checks include:
   - Moving so that an unvisited neighbor is “sealed” with only one corridor while you leave it behind.
   - The classic CSES prune: if the path hits the left/right/top/bottom edge and both “forward-side” cells along the edge are blocked while the cell “ahead” along the walk’s potential U-turn is free in a trapping way — implement the well-known “cannot split the grid” heuristics from CSES editorial solutions.
3. **Forced move optimization:** if exactly one of U/D/L/R is open, only try that (optional speedup).

**Steps**

1. Parse the 48-char pattern.
2. `dfs(0,0,0)` with `vis[0][0]=true`.
3. Return the count of accepting leaves.

## Walkthrough (on a sample)

The partial pattern with a fixed `R` early and `LD` near the end admits **201** compatible full paths; the search explores only walks consistent with those letters and the self-avoiding + prune rules.

## Pitfalls

- Allowing revisits.
- Ending at `(6,0)` too early.
- Missing prunes → TLE despite correct logic.
- Off-by-one on coordinates (`7x7`, 48 = `49-1` steps between 49 cells — actually visiting all 49 cells needs 48 moves).

## Complexity

- Time: exponential with aggressive pruning (passes for `7x7`)  
- Space: `O(48)` recursion + `7x7` visited

## Key takeaway

**Patterned Hamiltonian-ish paths on `7x7` = backtracking + visited + strong dead-end prunes** — the pattern only restricts which moves are tried at each step.
