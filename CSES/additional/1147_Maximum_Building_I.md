# [1147 — Maximum Building I](https://cses.fi/problemset/task/1147)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems I  
**Tags:** Largest empty rectangle, histogram, monotonic stack, grid  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A forest is a grid of `n` rows and `m` columns. Each square is empty (`.`) or has a tree (`*`). Find the maximum area of a rectangle that contains no trees.

### Input

- The first line has `n` and `m`.
- The next `n` lines describe the forest.

### Output

Print the maximum area.

### Constraints

- `1 <= n, m <= 1000`

### Examples

**Input**
```
4 7
...*.*.
.*.....
.......
......*
```

**Output**
```
12
```

---

## What this problem really is

Find the largest all-empty axis-aligned rectangle in a grid. Fix the bottom row of the rectangle, and the problem becomes the classic "largest rectangle in a histogram" question on the column heights.

## What to use

| Tool | Why |
|------|-----|
| Column height array | For each column, how many empty squares stack up ending at the current row |
| Monotonic stack | Solves the largest histogram rectangle for one row in `O(m)` |
| Process rows top to bottom | Each row reuses the heights from the row above, so the whole thing is `O(n * m)` |

## Approach

**Root idea:** a rectangle with its bottom edge on row `r` corresponds to a histogram. Each column's height is how far up the empty squares go from row `r`. The rectangle's best width and height come from one bar of the histogram, exactly as in the largest-rectangle-in-histogram problem. So we solve that problem once per row.

**Mental picture:** imagine standing at the bottom of each row and looking up. Each column shows a bar whose height is the number of empty squares above you before a tree blocks the way. A rectangle sitting on this row is a block of bars that all reach at least as high as the rectangle. The tallest rectangle that fits under the skyline is the best one for this row.

**How that becomes the method**

1. Keep `h[j]` for each column `j`, initially `0`.
2. For each row `i` from top to bottom:
   - For each column `j`: if the cell is empty, `h[j] = h[j] + 1`. Otherwise `h[j] = 0`.
   - Find the largest rectangle in the histogram `h`:
     - Use a stack of indices with increasing heights. When a lower bar comes in, pop taller bars and compute `height * width`, where the width spans from the bar after the new top of the stack to the current index minus one.
     - Add a sentinel bar of height `0` at the end to empty the stack.
3. The answer is the maximum area over all rows.

**Data structures:**
- `h[0..m-1]`: column heights.
- A monotonic stack of column indices.

## Walkthrough (on a sample)

Grid rows (the 4 rows are numbered 1 to 4):

```
...*.*.
.*.....
.......
......*
```

Height after each row (columns 1 to 7):

- After row 1 (`...*.*.`): `h = [1, 1, 1, 0, 1, 0, 1]`.
- After row 2 (`.*.....`): column 2 is a tree, so it resets. `h = [2, 0, 2, 1, 2, 1, 2]`.
- After row 3 (`.......`): `h = [3, 1, 3, 2, 3, 2, 3]`.

Largest rectangle in row 3's histogram `[3, 1, 3, 2, 3, 2, 3]`:
- Height `2` across columns `3..7` (heights `3, 2, 3, 2, 3`, minimum `2`): width `5`, area `10`.
- Height `1` across all 7 columns: area `7`.
- Best for this row: `10`.

After row 4 (`......*`): `h = [4, 2, 4, 3, 4, 3, 0]`.

Largest rectangle in row 4's histogram:
- Height `2` across columns `1..6` (heights `4, 2, 4, 3, 4, 3`, minimum `2`): width `6`, area `12`.
- Best for this row: `12`.

The overall maximum is `12`. Matches.

## Pitfalls

- Resetting `h[j]` to `0` on a tree. A tree must break the column, not just the rectangle.
- Forgetting the final sentinel. Bars still on the stack at the end never get counted.
- Off-by-one widths when popping. The width is `current index - new left boundary - 1`.
- Using `int` for areas. The area is at most `1e6`, but keep the habit of checking the bounds.
- Recomputing heights from scratch each row. That is `O(n^2 * m)` and too slow.

## Complexity

- Time: `O(n * m)`.
- Space: `O(m)`.

## Key takeaway

A grid rectangle problem is a histogram problem repeated for each row. Keep column heights as you move down, and reuse the monotonic-stack histogram solution.
