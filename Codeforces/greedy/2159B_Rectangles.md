# [2159B — Rectangles](https://codeforces.com/problemset/problem/2159/B)

**Platform:** Codeforces
**Source:** Codeforces Round 1058 (Div. 1)
**Tags:** brute force, data structures, dp, dsu, greedy, implementation, two pointers
**Difficulty / Rating:** 2100
**Time limit:** 4 seconds
**Memory limit:** 1024 megabytes
**Editorial:** [Codeforces Round 1058 Editorial (Problem B section)](https://codeforces.com/blog/entry/147322)

---

## Problem Statement

You are given a binary grid `G` of dimensions `n x m`.

A rectangle is a tuple `(u, d, l, r)` that satisfies:

- `1 <= u < d <= n`,
- `1 <= l < r <= m`,
- cells `(u, l)`, `(u, r)`, `(d, l)`, `(d, r)` all contain a `1`.

The area of a rectangle `(u, d, l, r)` is `(d - u + 1) * (r - l + 1)`.

For each cell `(i, j)`, find the minimum area of any rectangle `(u, d, l, r)` such that `u <= i <= d` and `l <= j <= r`.

Here `G[i][j]` denotes the cell in row `i`, column `j`.

### Input

Each test contains multiple test cases. The first line contains the number of test cases `t` (`1 <= t <= 1e4`).

The first line of each test case contains two integers `n` and `m` (`2 <= n, m`, `n * m <= 250000`).

Each of the next `n` lines contains a binary string of length `m`, the `i`-th row of `G`.

It is guaranteed that the sum of `n * m` over all test cases does not exceed `250000`.

### Output

For each test case, output a grid of `n` rows and `m` columns. In column `j` of row `i`, output the minimum area of a rectangle covering cell `(i, j)`, or `0` if no rectangle covers it.

### Constraints

- `1 <= t <= 1e4`
- `2 <= n, m`, `n * m <= 250000`
- Sum of `n * m` over all tests `<= 250000`

### Examples

Input:

```
3
3 5
10101
10100
00101
4 6
011101
010001
100010
101110
5 5
11100
10110
11111
01101
00111
```

Output:

```
6 6 6 9 9
6 6 6 9 9
0 0 9 9 9
0 10 8 8 10 10
0 10 8 8 10 10
10 10 8 8 10 0
10 10 8 8 10 0
6 6 6 0 0
6 6 4 4 0
6 4 4 4 6
0 4 4 6 6
0 0 6 6 6
```

### Note

The first test case is explained in the statement: rectangles `(1, 2, 1, 3)` (area 6) and `(1, 3, 3, 5)` (area 9) are the relevant ones.

---

## What this problem really is

For each cell, we want the smallest rectangle (with corners on `1`s, and at least 2 rows and 2 columns) that contains it. The naive view checks every possible rectangle, which is far too many. The real task is to find a small set of candidate rectangles that is guaranteed to contain the best one for every cell, and then spread each candidate's area to the cells it covers.

## What to use

| Tool | Why |
|------|-----|
| Fix a pair of rows `(u, d)` | The rectangle's top and bottom are rows, so pairs of rows are the outer loop. |
| Common `1` columns of two rows | A rectangle with rows `u` and `d` needs a left and right column where both rows have `1`. Only columns where both rows are `1` matter. |
| Consecutive common columns | For a fixed row pair, the useful rectangles are between neighbouring common columns. Wider ones are never better for any cell. |
| Row-range DP per column | Propagating the best area for "rectangles whose row range contains `[u, d]`" lets each cell read its answer in `O(1)` from the diagonal entry. |
| Transposing when `n > m` | Keeps the number of row pairs at most `min(n, m)`, which controls the running time. |

## Approach

**Root idea:** Only rectangles between consecutive common `1` columns of a row pair are needed. If a wider rectangle with the same rows covers a cell, the narrower one between consecutive common columns that also covers that cell has a smaller or equal area. So the candidate set is small: for each row pair, one candidate per consecutive pair of common columns. The editorial bounds this total as roughly `n^2 * m / 2` candidates, each with a small total column width, so the work stays manageable.

**Mental picture:** Think of two rows as two strips laid on top of each other. Columns where both strips have a `1` are windows. A rectangle is the stretch between two neighbouring windows, spanning the rows from the top strip to the bottom strip. Each candidate paints its area over every cell it covers. Then each cell keeps the smallest paint it received. The DP is a way to paint efficiently: a rectangle with rows `[u', d']` that contains `[u, d]` also contains every smaller row range inside it, so a value at `[u, d]` can be improved from its neighbours `[u, d+1]` and `[u-1, d]`.

**How that becomes the method:**
1. Optionally transpose so that `n <= m` (fewer row pairs).
2. For every row pair `u < d`, find the columns `c` where `G[u][c] = G[d][c] = 1`, in order.
3. For each consecutive pair of common columns `(a, b)`, the rectangle `(u, d, a, b)` has area `(d - u + 1) * (b - a + 1)`. Record this value on the columns `a..b` for row range `[u, d]`.
4. Build a DP `best[u][d][col]` meaning "minimum area over recorded rectangles covering column `col` whose row range contains `[u, d]`". Propagate: `best[u][d]` can be lowered by `best[u][d+1]` and by `best[u-1][d]`, because containing a larger range implies containing a smaller one.
5. The answer for cell `(i, col)` is `best[i][i][col]`, or `0` if no rectangle is recorded.

**Data structures:**
- Per row pair, a list of common `1` columns (built by scanning two rows).
- A DP table over row ranges and columns, filled in order of increasing row span from the inside out. The editorial notes memory can be reduced to `O(min(n, m) * (n + m))` by processing one row range at a time.

## Walkthrough (on a sample)

First test: `n = 3`, `m = 5`.

```
row1: 1 0 1 0 1
row2: 1 0 1 0 0
row3: 0 0 1 0 1
```

Row pair `(1, 2)`: common `1` columns are `1` and `3`. One consecutive pair, so rectangle `(1, 2, 1, 3)` with area `2 * 3 = 6`. It covers rows `1..2`, columns `1..3`.

Row pair `(1, 3)`: common `1` columns are `3` and `5`. Rectangle `(1, 3, 3, 5)` with area `3 * 3 = 9`. It covers rows `1..3`, columns `3..5`.

Row pair `(2, 3)`: common column is only `3`. No consecutive pair, so no rectangle.

Now fill the answers:

- Cell `(1, 1)`: covered only by the area-6 rectangle. Answer `6`.
- Cell `(1, 3)`: covered by area 6 and area 9. Minimum `6`.
- Cell `(3, 3)`: covered only by the area-9 rectangle (rows `1..3`, columns `3..5`). Answer `9`.
- Cell `(3, 1)`: no rectangle includes column 1 in row 3. Answer `0`.

The output row 3 is `0 0 9 9 9`, matching the sample.

## Pitfalls

- **Using all common columns, not consecutive ones:** Only consecutive common columns give minimal useful rectangles. Wider spans just cost more.
- **Forgetting the `u < d` and `l < r` requirement:** A single row or single column is not a rectangle.
- **Indexing rows and columns inconsistently:** Cells use 1-indexed rows and columns in the statement; the covering test is `u <= i <= d` and `l <= j <= r`.
- **Dropping a candidate's side columns:** Candidates come only from columns that are `1` in both rows. Columns outside the first and last common column of a row pair are not covered by that pair at all.
- **Memory blow-up:** Storing every rectangle naively can be large. Process per row pair or per row range to keep memory low.
- **Forgetting to transpose:** Working with the larger dimension as the row count makes the number of row pairs too big.
- **Answer for uncovered cells is `0`:** Not `-1` or infinity.

## Complexity

- Time: `O(n * m * min(n, m))`, which is `O(n * m * sqrt(n * m))` in the worst case, within the 4 second limit for `n * m <= 250000`.
- Space: the simple version uses up to `O(n * m * min(n, m))` memory; a leaner version uses `O(min(n, m) * (n + m))`.

## Key takeaway

For "smallest enclosing rectangle covering each cell", reduce candidates to neighbouring common columns of row pairs, then spread each candidate's value through a row-range DP where a smaller row range inherits the best value of any larger range that contains it.
