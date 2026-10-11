# [3218 — Knight Moves Queries](https://cses.fi/problemset/task/3218)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems II  
**Tags:** Knight distance, closed form, symmetry, case analysis  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

On an infinite chessboard (1-indexed), a knight is at `(x, y)`. For each query, find the minimum number of knight moves to reach the top-left corner `(1, 1)`.

### Input

- The first line has `n`, the number of queries.
- Each of the next `n` lines has `x` and `y`.

### Output

For each query, print the minimum number of moves.

### Constraints

- `1 <= n <= 1e5`
- `1 <= x, y <= 1e9`

### Examples

**Input**
```
4
1 1
2 3
4 1
42 1337
```

**Output**
```
0
1
3
669
```

---

## What this problem really is

A knight move changes the position by one of eight `(±1, ±2)` or `(±2, ±1)` steps. We need the shortest path to the corner. Since the board is infinite and the queries are many, we want a formula, not a search.

## What to use

| Tool | Why |
|------|-----|
| Shift to the corner | Set `dx = x-1`, `dy = y-1`, so the target is `(0, 0)` |
| Symmetry | Swapping coordinates does not change the answer, so assume `dx >= dy` |
| Two special small cases | The general formula is wrong for `(1,0)` and `(2,2)` |
| Closed-form formula with `delta = dx - dy` | Gives the answer in `O(1)` per query |

## Approach

**Root idea:** a knight covers at most 2 units along its long axis per move, and the short-axis error has to be paid for. Move mostly along the long axis. The difference `delta = dx - dy` is the leftover that must be spent. Each block of 4 units of leftover costs 2 moves in the usual case, and the corner needs its own correction.

**Mental picture:** imagine you are walking across a grid of tiles, where each knight move jumps two tiles one way and one tile the other. Along the long axis, you can cover about 2 tiles per move, so the long axis sets the pace. The short axis only needs to be matched. Once the short axis is nearly matched, the remaining gap is burned off in bundles: four tiles of gap take two moves, and near the corner the bundles are three tiles, which is why the formula has two branches.

**How that becomes the method**

1. For each query, set `dx = x - 1`, `dy = y - 1`. If `dx < dy`, swap them. Now `dx >= dy >= 0`.
2. Special cases:
   - `(dx, dy) = (0, 0)`: answer `0`.
   - `(dx, dy) = (1, 0)`: answer `3`.
   - `(dx, dy) = (2, 2)`: answer `4`.
3. Otherwise let `delta = dx - dy`.
   - If `dy > delta`: answer `delta - 2 * floor((delta - dy) / 3)`.
   - Else: answer `delta - 2 * floor((delta - dy) / 4)`.
   - The floor here is the mathematical floor, so for negative values `-2 / 3` gives `-1`.

**Data structures:** none. Each query is `O(1)`.

## Walkthrough (on a sample)

Query `(4, 1)`: `dx = 3`, `dy = 0`. No special case. `delta = 3`. Since `dy = 0` is not greater than `delta = 3`, use the second branch: `3 - 2 * floor((3 - 0) / 4) = 3 - 2 * 0 = 3`. Matches the expected `3`.

Concrete check: the offset `(3, 0)` is reached in three moves: `(2, 1) + (-1, -2) + (2, 1) = (3, 0)`. Two moves cannot reach it, because the two moves would need to sum to `(3, 0)` and no pair of knight moves does. So `3` is correct.

Query `(2, 3)`: `dx = 1`, `dy = 2`, swap to `dx = 2`, `dy = 1`. No special case. `delta = 1`. `dy = 1` is not greater than `delta = 1`, so use the second branch: `1 - 2 * floor(0 / 4) = 1`. Matches.

Query `(42, 1337)`: `dx = 41`, `dy = 1336`, swap to `dx = 1336`, `dy = 41`. `delta = 1295`. `dy = 41` is not greater than `1295`, so use the second branch: `1295 - 2 * floor(1254 / 4) = 1295 - 2 * 313 = 669`. Matches.

Query `(1, 1)`: `dx = dy = 0`, answer `0`. Matches.

## Pitfalls

- Forgetting the swap. The formula assumes `dx >= dy`.
- Skipping the special cases. `(1, 0)` gives `1` from the formula but the true answer is `3`. `(2, 2)` gives `2` from the formula but the true answer is `4`.
- Using truncating division for negative values. `(delta - dy) / 3` with `delta - dy < 0` must floor toward negative infinity.
- Off-by-one when shifting to the corner. Use `x - 1` and `y - 1`, not `x` and `y`.
- Checking only one sample. Test the large sample, which hits the second branch.

## Complexity

- Time: `O(1)` per query, `O(n)` overall.
- Space: `O(1)`.

## Key takeaway

Knight distance on an infinite board has a closed form once you align the target to the origin, put the long axis first, and handle the two small corner cases separately.
