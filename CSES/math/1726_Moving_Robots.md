# [1726 — Moving Robots](https://cses.fi/problemset/task/1726)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** expected value, linearity, random walk, DP  
**Difficulty / Rating:** Medium–Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Each square of an `8 x 8` chessboard has a robot. Each robot independently moves `k` steps, and there can be many robots on the same square.

On each turn, a robot moves one step left, right, up or down, but not outside the board. It randomly chooses a direction among those where it can move.

Your task is to calculate the expected number of empty squares after `k` turns.

### Input

The only input line has an integer `k`.

### Output

Print the expected number of empty squares rounded to six decimal places (rounding half to even).

### Constraints

- `1 <= k <= 100`

### Examples

**Input**
```
10
```

**Output**
```
23.120740
```

---

## What this problem really is

64 independent random walks on a grid with reflecting/blocked off-board moves. After `k` steps, expected count of cells with **zero** robots.

## What to use

| Tool | Why |
|------|-----|
| Linearity of expectation | `E[# empty] = sum_cells P(cell empty)` — no need for joint occupancy law |
| Independence of robots | `P(cell empty) = product over starts (1 - P(robot from s ends at cell))` |
| DP random walk | For each start, distribute probability mass for `k` steps |
| Degree of a cell | Corner 2, edge 3, center 4 legal moves |

## Approach

**Root idea:** indicator `I_c = 1` if cell `c` is empty. Expectation sums `P(I_c = 1)`. Because robots move independently, a cell is empty iff **every** robot misses it.

**Mental picture:** 64 particles bouncing on the chessboard (cannot step off). For one target square, multiply the chances each particle is elsewhere.

**Steps**

1. Initialize `emptyProb[c] = 1` for all 64 cells.
2. For each starting cell `s`:
   - Run a walk DP: `p[0][s] = 1`, else 0.
   - For `t = 0 .. k-1`, for each cell `u`, split `p[t][u]` equally among legal neighbors of `u`.
   - After `k` steps, for each cell `c`: `emptyProb[c] *= (1 - p[k][c])`.
3. Answer = `sum_c emptyProb[c]`.
4. Print with six decimals, half-to-even.

Time: `64 * k * 64 * O(1)` ≈ fine.

## Walkthrough (on a sample)

`k = 10`: after multiplying miss-probabilities over all starts, the sum of empty probabilities is `23.120740`.

(Sanity: `k` large → robots mix; many empties expected; `k` small → almost no empties.)

## Pitfalls

- Treating occupancy of robots as dependent when multiplying miss probs (they are independent — product is correct).
- Using uniform 1/4 even on edges — must divide by the **actual** number of legal moves.
- Computing `P(occupied)` incorrectly as a sum of probs (can exceed 1) instead of `1 - product(1-p)`.
- Wrong rounding mode.

## Complexity

- Time: `O(64^2 * k)`  
- Space: `O(64 * k)` or `O(64)` with rolling layers

## Key takeaway

**Expected empty cells = sum over cells of product of miss-probabilities** — linearity + independence + per-start walk DP.
