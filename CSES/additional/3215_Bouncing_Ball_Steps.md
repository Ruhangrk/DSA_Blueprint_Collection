# [3215 — Bouncing Ball Steps](https://cses.fi/problemset/task/3215)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems II  
**Tags:** Periodic motion, unfolding, modular arithmetic, counting events  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A ball starts at the top-left corner of an `n x m` grid, moving diagonally away from the corner. Each step it moves one cell diagonally. When it hits a border, it changes direction. After `k` steps, report its cell and the number of direction changes.

### Input

- The first line has `t`.
- Each of the next `t` lines has `n`, `m`, `k`.

### Output

For each test, print the row, the column, and the number of direction changes.

### Constraints

- `1 <= t <= 1000`
- `2 <= n, m <= 1e9`
- `0 <= k <= 1e18`

### Examples

**Input**
```
6
3 4 0
3 4 1
3 4 2
3 4 3
3 4 4
42 1337 123456789
```

**Output**
```
1 1 0
2 2 0
3 3 1
2 4 2
1 3 3
34 300 3101295
```

---

## What this problem really is

The ball moves independently in rows and in columns. The row motion bounces between `1` and `n`, and the column motion bounces between `1` and `m`. Each axis is a simple back-and-forth that repeats. So we can treat each axis on its own and combine the results.

## What to use

| Tool | Why |
|------|-----|
| Unfolding each axis | Turns a bouncing walk into a straight line that repeats with a fixed period |
| Modulo the period | Gives the position after `k` steps without simulating |
| Counting boundary hits | Each axis hits its wall at fixed multiples, so the count is a division |
| Inclusion for simultaneous hits | A corner hit changes direction once, not twice |

## Approach

**Root idea:** the row walk goes from `1` to `n` and back, so its period is `2(n-1)`. The column walk has period `2(m-1)`. Position after `k` steps depends only on `k` modulo these periods.

**Mental picture:** imagine a long strip of paper of length `n-1`, which you fold back and forth. Walking straight along the unfolded strip for `k` units, then mapping back onto the grid, gives the row. The same idea gives the column with the strip of length `m-1`. Every time the unfolded walk crosses a multiple of `n-1`, the real ball bounces off a row wall. Every crossing of a multiple of `m-1` is a column bounce.

**How that becomes the method**

1. Let `t = k mod 2(n-1)`. Then `row = 1 + t` if `t <= n-1`, otherwise `row = 1 + 2(n-1) - t`.
2. Do the same for the column with `m` and `2(m-1)`.
3. Direction changes = `floor(k / (n-1)) + floor(k / (m-1)) - floor(k / lcm(n-1, m-1))`.
   - The first two terms count row and column bounces.
   - The last term removes double counting: at a corner, both axes bounce at the same moment, but this is one direction change.

**Data structures:** only integers. Use 64-bit values, since `lcm(n-1, m-1)` can be up to `1e18`.

## Walkthrough (on a sample)

Take `n = 3`, `m = 4`, `k = 4`.

Row: period `2(3-1) = 4`. `t = 4 mod 4 = 0`, so `row = 1`.

Column: period `2(4-1) = 6`. `t = 4 mod 6 = 4`. Since `4 > m-1 = 3`, `col = 1 + 6 - 4 = 3`.

Direction changes:
- Row bounces: `floor(4/2) = 2`.
- Column bounces: `floor(4/3) = 1`.
- Corner overlaps: `lcm(2, 3) = 6`, `floor(4/6) = 0`.
- Total: `2 + 1 - 0 = 3`.

Output `1 3 3`, matches the sample.

Check `k = 3` for the same grid: row `t = 3`, `3 > 2`, so `row = 1 + 4 - 3 = 2`. Column `t = 3 <= 3`, so `col = 4`. Changes: `floor(3/2) + floor(3/3) - floor(3/6) = 1 + 1 - 0 = 2`. Output `2 4 2`, matches.

Check the large sample `42 1337 123456789` for the change count. `n-1 = 41`, `m-1 = 1336`, `gcd = 1`, `lcm = 54776`.
- `floor(123456789 / 41) = 3011141`
- `floor(123456789 / 1336) = 92407`
- `floor(123456789 / 54776) = 2253`
- Total: `3011141 + 92407 - 2253 = 3101295`. Matches.

## Pitfalls

- Forgetting the corner correction. Without subtracting the `lcm` term, the large sample is off by `2253`.
- Using `m` where the period is `2(m-1)`. Off-by-one in the period gives the wrong cell.
- Mixing rows and columns in the fold formula.
- Overflow: `lcm(n-1, m-1)` can be near `1e18`. Compute it as `(n-1) / gcd * (m-1)` in 64-bit.
- Testing only small cases. The large sample catches the corner correction.

## Complexity

- Time: `O(log)` per test for the gcd.
- Space: `O(1)`.

## Key takeaway

A bouncing walk is a straight walk on a folded strip. Each axis is periodic, and the number of bounces is a count of multiples, minus the double-counted corners.
