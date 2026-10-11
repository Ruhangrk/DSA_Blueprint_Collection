# [2432 — Grid Puzzle I](https://cses.fi/problemset/task/2432)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems II  
**Tags:** Bipartite degree sequences, Gale–Ryser theorem, greedy construction, residual capacities  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You have an `n x n` grid. Choose some cells so that row `i` contains exactly `a[i]` chosen cells and column `j` contains exactly `b[j]` chosen cells. Print one valid grid, using `X` for chosen and `.` for not chosen. If impossible, print `-1`.

### Input

- The first line has `n`.
- The second line has `a[1..n]`.
- The third line has `b[1..n]`.

### Output

Print `n` lines describing the grid, or `-1`.

### Constraints

- `1 <= n <= 50`
- `0 <= a[i], b[j] <= n`

### Examples

**Input**
```
5
0 1 3 2 0
1 2 2 0 1
```

**Output**
```
.....
..X..
.XX.X
XX...
.....
```

---

## What this problem really is

This is a bipartite graph question. Rows and columns are vertices, and a chosen cell is an edge between its row and its column. We need a graph with prescribed degrees on each side. Such a graph exists exactly when the two sums match and a degree condition holds (the Gale–Ryser theorem). We do not need the theorem itself, because a greedy construction finds the grid and fails exactly when it is impossible.

## What to use

| Tool | Why |
|------|-----|
| Sum check | Every chosen cell counts once for its row and once for its column, so `sum a` must equal `sum b` |
| Residual demands `b` | Track how many cells each column still needs |
| Greedy: each row takes the columns with the largest remaining demand | Standard construction for bipartite degree sequences |

## Approach

**Root idea:** process rows one at a time. For row `i`, choose the `a[i]` columns that still need the most cells. Subtract one from each of those columns' remaining demand. If at any point a row needs more columns than exist with positive demand, or a demand goes negative, the instance is impossible.

**Why the greedy works:** the columns with the largest remaining demand are the hardest to fill later. Giving them the cell now is never worse than giving it to a column that can wait. This is the classic argument behind the Havel–Hakimi style construction for bipartite graphs.

**Mental picture:** each column is a bucket that needs `b[j]` balls. Each row must drop `a[i]` balls into different buckets. The row drops its balls into the emptiest-needing buckets, the ones with the most still missing. Buckets that are nearly full get skipped, and the process ends with every bucket exactly full if the totals work out.

**How that becomes the method**

1. If `sum a != sum b`, print `-1`.
2. Keep `need[j] = b[j]` for each column.
3. For each row `i` in order:
   - Pick the `a[i]` columns with the largest `need[j]` among those with `need[j] > 0`. If there are fewer than `a[i]` such columns, print `-1`.
   - Mark those cells with `X`, and decrement `need[j]` for each.
4. At the end, all `need[j]` should be `0`. If not, print `-1` (this cannot happen once the sums match and every row succeeded, but it is a cheap check).

**Data structures:** a `need` array of size `n`, and a sort per row (or a small loop, since `n <= 50`).

## Walkthrough (on a sample)

`a = 0 1 3 2 0`, `b = 1 2 2 0 1`. Sums: `6` and `6`, so it can work.

`need = [1, 2, 2, 0, 1]` (columns 1 to 5).

| Row | `a[i]` | Columns chosen (largest `need`) | `need` after |
|---|---|---|---|
| 1 | 0 | none | `[1, 2, 2, 0, 1]` |
| 2 | 1 | column 2 (need 2) | `[1, 1, 2, 0, 1]` |
| 3 | 3 | column 3 (need 2), then columns 1 and 2 (need 1 each) | `[0, 0, 1, 0, 1]` |
| 4 | 2 | columns 3 and 5 (need 1 each) | `[0, 0, 0, 0, 0]` |
| 5 | 0 | none | `[0, 0, 0, 0, 0]` |

All demands are met. The grid:

- Row 2: column 2 → `.X...` pattern with X at column 2.
- Row 3: columns 1, 2, 3 → `XXX..`.
- Row 4: columns 3, 5 → `..X.X`.

Written out:

```
.....
.X...
XXX..
..X.X
.....
```

Check the row sums: `0, 1, 3, 2, 0` ✓. Column sums: column 1 gets `1` (row 3), column 2 gets `2` (rows 2, 3), column 3 gets `2` (rows 3, 4), column 4 gets `0`, column 5 gets `1` (row 4). Matches `b = 1 2 2 0 1` ✓.

The grid differs from the sample output, which is allowed because any valid grid is accepted.

## Pitfalls

- Skipping the sum check. Mismatched totals can still fail silently in the greedy.
- Choosing columns by index instead of by largest remaining need. Index order can fail on valid instances.
- Forgetting that a column with `need = 0` cannot be chosen again.
- Decrementing before selecting. Select first, then update.
- Printing a grid when the answer is impossible. Print only `-1`.

## Complexity

- Time: `O(n^2 log n)` with sorting per row, or `O(n^3)` with a simple scan. For `n = 50` both are trivial.
- Space: `O(n^2)` for the output grid.

## Key takeaway

Degree-sequence construction on a bipartite graph is greedy: each row takes the columns with the most remaining demand. The sum check and the failure check decide feasibility.
