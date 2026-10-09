# [1744 — Rectangle Cutting](https://cses.fi/problemset/task/1744)

**Platform:** CSES  
**Source:** CSES Problem Set — Dynamic Programming  
**Tags:** dynamic programming, geometry-ish cuts  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an `a x b` rectangle, your task is to cut it into squares. On each move you can select a rectangle and cut it into two rectangles in such a way that all side lengths remain integers. What is the minimum possible number of moves?

### Input

The only input line has two integers `a` and `b`.

### Output

Print one integer: the minimum number of moves.

### Constraints

- `1 <= a, b <= 500`

### Examples

**Input**
```
3 5
```

**Output**
```
3
```

---

## What this problem really is

Start with one `a x b` rectangle. Each move guillotine-cuts one current rectangle into two smaller axis-aligned rectangles with integer sides. Stop when every piece is a square. Minimize the number of cuts.

## What to use

| Tool | Why |
|------|-----|
| `dp[w][h]` | Min cuts needed to turn a `w x h` rectangle into squares |
| Try all cut positions | First cut is vertical or horizontal at an integer offset |
| Symmetry `dp[w][h] = dp[h][w]` | Same rectangle rotated — fill both or store with `w <= h` |

## Approach

**Root idea:** after the first cut of a `w x h` rectangle, you get two smaller rectangles. Total cuts = `1` + (cuts for left piece) + (cuts for right piece). Try every legal first cut and take the minimum. Squares need `0` cuts.

**Mental picture:** you hold a chocolate bar `w` by `h`. One straight snap splits it into two bars. Keep snapping until every piece is square. DP remembers the best snap sequence for every smaller size.

**Data structures**

- `dp[0..A][0..B]` with `A,B <= 500` (use max side `500`).
- Init: for all `s`, `dp[s][s] = 0`. For non-squares start as `INF`.

**Recurrence** (for `w != h`)

```
dp[w][h] = INF
// vertical cuts: split width
for i = 1 .. w-1:
    dp[w][h] = min(dp[w][h], 1 + dp[i][h] + dp[w-i][h])
// horizontal cuts: split height
for j = 1 .. h-1:
    dp[w][h] = min(dp[w][h], 1 + dp[w][j] + dp[w][h-j])
```

Fill in order of increasing `w*h` (or nested loops `w = 1..a`, `h = 1..b` ensuring smaller rectangles are already computed — when you reference `i < w` or `j < h`, those sizes are smaller in at least one dimension; standard double loop over all pairs works if you iterate by increasing area, or iterate carefully).

Practical fill: for `len = 1 .. max(a,b)`, for all `w,h` with max side constraints, compute using already-known strictly smaller widths/heights. Simplest correct approach: loop `w` from `1` to `a`, `h` from `1` to `b`, and because cuts always produce strictly smaller width or height, previously computed cells with smaller first or second index are ready if you also fill all `w',h'` with `w' <= w` and `h' <= h` first (use two nested loops in that order — when computing `(w,h)`, all `(i,h)` with `i < w` and `(w,j)` with `j < h` are already done).

**Steps**

1. Read `a`, `b`.
2. Build `dp` for all sizes up to `a` and `b`.
3. Print `dp[a][b]`.

## Walkthrough (on a sample)

`a = 3`, `b = 5` (not a square).

One optimal sequence with **3** cuts (example):

1. Cut the `3x5` vertically into `3x2` and `3x3`.  
2. `3x3` is already a square → `0` more.  
3. Cut `3x2` into `2x2` and `1x2`.  
4. Cut `1x2` into two `1x1` squares.

That’s `3` cuts total. DP explores all first-cut positions and stores `dp[3][5] = 3`.

Check bases: `dp[1][1]=0`, `dp[2][2]=0`, `dp[3][3]=0`. A `1x2` needs exactly `1` cut.

## Pitfalls

- **Greedy “cut largest square”** is not always optimal for this cutting model — use DP.
- **Already square** → answer `0`, not `1`.
- **Order of filling** — only cut into strictly smaller rectangles; ensure those states exist before use.
- **`a,b <= 500`** → `O(a*b*(a+b))` ≈ `500^3` order is tight but acceptable in C++.

## Complexity

- Time: `O(a * b * (a + b))`  
- Space: `O(a * b)`

## Key takeaway

**Min cuts into squares = DP over rectangle sizes.** First cut splits into two sub-rectangles; answer is `1 +` best sum of sub-answers. Squares are the `0` base.
