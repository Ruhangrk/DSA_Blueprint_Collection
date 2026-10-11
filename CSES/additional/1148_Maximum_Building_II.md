> **Warning: this solution might be incorrect.** The counting idea below was checked against brute force on the sample. The fast O(n·m) version has not been implemented or verified by me.

# [1148 — Maximum Building II](https://cses.fi/problemset/task/1148)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems II  
**Tags:** Counting rectangles, monotonic stack, histogram, difference arrays  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A forest is an `n x m` grid. `.` is empty and `*` is a tree. For each building size `(r, c)`, count the places a `r x c` rectangle fits with no trees inside.

### Input

- The first line has `n` and `m`.
- Next `n` lines describe the forest.

### Output

Print `n` lines with `m` integers each. Line `r`, column `c` is the number of `r x c` rectangles (`r` rows tall, `c` columns wide).

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
24 17 13 9 6 3 1 
16 9 7 5 3 1 0 
9 3 2 1 0 0 0 
3 0 0 0 0 0 0
```

---

## What this problem really is

A rectangle is identified by its bottom row, its height `r`, and its left and right columns. Fix the bottom row. Above it, each column has a height `h[j]` = how many empty cells run up from the bottom row in column `j`. A rectangle with bottom row `i`, columns `[l, rr]` and height `r` fits exactly when `r <= min(h[l..rr])`.

So each bottom row contributes: for every window of columns `[l, rr]`, add 1 to `count[r][width]` for every `r <= min(h[l..rr])`.

## What to use

| Tool | Why |
|------|-----|
| Histogram heights `h[j]` per bottom row | Turns each row into "how tall is the empty column" |
| Window minimum `min(h[l..rr])` | Decides which heights fit the window |
| Monotonic stack over columns | Groups windows by their minimum, so we avoid checking all windows |
| 2D difference array over `(height, width)` | Adds a whole range of heights and widths in O(1) |

## Approach

**Checked counting formulation (sample verified):** for each bottom row, add contributions window by window using the rule above. This matches the expected output on the sample, which I confirmed against an exhaustive count.

**Faster version (not verified by me):** scan the columns left to right with a monotonic stack of heights. When a stack entry is popped, its minimum `v` applies to a contiguous range of windows. Add its contribution to heights `1..v` and to a range of widths, using 2D difference arrays. Finally take prefix sums in both directions.

**Mental picture:** for each bottom row, a skyline of empty heights. A window is a horizontal slice of the skyline. Its minimum height is the tallest rectangle that fits. The stack sweeps the skyline and reports, for each "limiting column", how many slices it limits.

**Why the window-by-window version is too slow:** it is O(n · m²) window checks, about `1e9` for the limits. The stack version reduces this to O(n · m) with difference arrays.

## Walkthrough (on the sample)

Heights for bottom row 4 (`......*`), computed from all four rows:

- Column heights after row 4: `h = 4 2 4 3 4 3 0`. Column 7 has a tree in row 4, so its height is 0.
- Consider the window of columns `[1..6]`. Its minimum is `2` (column 2). So for bottom row 4, this window fits every height `1` and `2` at width `6`.

So this window adds 1 to the count for height 1 and width 6, and 1 to the count for height 2 and width 6. The full table sums such contributions over all bottom rows. I have not finished that sum by hand, so this walkthrough does not yet confirm the expected table.

## Pitfalls

- Mixing up rows and columns. The output row is the height `r`, and the column is the width `c`.
- Forgetting heights reset to `0` at a tree.
- Counting windows once per bottom row, which is exactly what the formulation requires.
- Off-by-one in the difference array indices.

## Complexity

- Checked formulation: O(n · m²) (too slow for the limits, but correct).
- Fast version: O(n · m) time and memory, not verified.

## Key takeaway

Each bottom row reduces the problem to counting windows by their minimum height. The monotonic stack is the tool that makes that count fast, and difference arrays add the ranges.
