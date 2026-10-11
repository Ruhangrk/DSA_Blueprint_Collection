# [3306 — Nearest Campsites I](https://cses.fi/problemset/task/3306)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems I  
**Tags:** Manhattan distance, nearest neighbor, sweep line, Fenwick tree (BIT)  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A campground is a grid. Each square holds at most one campsite, and every campsite is either reserved or free. The distance between squares `(x1, y1)` and `(x2, y2)` is the Manhattan distance `|x1 - x2| + |y1 - y2|`.

Find the maximum, over all free campsites, of the distance from that free campsite to its nearest reserved campsite.

### Input

- The first line has two integers `n` and `m`: the number of reserved and free campsites.
- The next `n` lines describe the reserved campsites, each with `x y`.
- The next `m` lines describe the free campsites, each with `x y`.

Each square contains at most one campsite.

### Output

Print one integer: the longest distance from a free campsite to the nearest reserved campsite.

### Constraints

- `1 <= n, m <= 1e5`
- `1 <= x, y <= 1e6`

### Examples

**Input**
```
4 2
1 1
5 2
2 6
4 7
1 3
7 5
```

**Output**
```
5
```

**Explanation:** The best choice is the free campsite `(7, 5)`. Its distance to the nearest reserved campsite is 5.

---

## What this problem really is

For every free point, compute the Manhattan distance to the closest reserved point, then take the maximum of those values. The answer is a max-of-min over a 2D point set.

## What to use

| Tool | Why |
|------|-----|
| Quadrant decomposition | Splits "nearest reserved point in Manhattan metric" into 4 easy one-sided problems |
| Coordinate reflection | Lets one routine handle all 4 quadrants |
| Sort by x + sweep | Makes the "x <= fx" condition a prefix that grows as we sweep |
| Fenwick tree over y, storing prefix max | Answers "max value among reserved points with y <= fy seen so far" in `O(log C)` |

Grid size is `1e6 x 1e6`, so BFS on the grid is out. Only the `n + m` points matter.

## Approach

**Root idea:** for a free point `f`, every reserved point `r` lies in one of the four closed quadrants around `f`. Inside the lower-left quadrant (`rx <= fx` and `ry <= fy`), the Manhattan distance is linear:

`dist = (fx - rx) + (fy - ry) = (fx + fy) - (rx + ry)`

So the nearest reserved point in that quadrant is the one with the largest `rx + ry`. Minimizing distance becomes maximizing a single key.

**Mental picture:** imagine a plane with reserved stones. Stand on a free point and look only at stones to your lower-left. Each such stone's distance is "how far you are along the diagonal `x + y`" minus "how far the stone is along the diagonal". The stone closest to you is the one that sits furthest up-right on that diagonal while still inside your lower-left region. Do this for all four quadrants (by reflecting the board) and take the minimum; a reserved stone always falls into at least one quadrant, so no stone is missed.

**How that becomes the method**

1. Reflect the board: for each of the four sign choices, replace `x` by `X - x` and/or `y` by `Y - y` (with `X = Y = 1e6 + 1`) to turn the target quadrant into the lower-left one.
2. In the reflected board, sort all points by `x` (reserved and free together). Ties: process reserved points before free points so that `rx <= fx` is respected.
3. Sweep in increasing `x`. When a reserved point is seen, update a Fenwick tree at position `ry` with value `rx + ry`, using prefix max.
4. When a free point is seen, query the Fenwick tree for prefix `[1..fy]`. If the result exists, candidate distance = `(fx + fy) - best`. Keep the minimum per free point over the four reflections.
5. The answer is the maximum over free points of their minimum distance.

**Data structures:**
- Points array: sorted by `x`, each entry stores type (reserved/free), `x`, `y`, and the free point's index.
- Fenwick tree `bit[1..1e6]`: prefix max of `rx + ry`, initialized to negative infinity. Reset it between the four reflections.
- `best[f]`: minimum distance found so far for free point `f`, initialized to infinity.

## Walkthrough (on a sample)

Reserved: `(1,1)`, `(5,2)`, `(2,6)`, `(4,7)`. Free: `(1,3)`, `(7,5)`.

Lower-left pass (no reflection). Sorted by `x`: `(1,1)R`, `(1,3)F`, `(2,6)R`, `(4,7)R`, `(5,2)R`, `(7,5)F`. Ties at `x=1`: reserved first.

- Process `(1,1)R`: BIT at `y=1` gets `1+1 = 2`.
- Process `(1,3)F`: query prefix `y <= 3` gives max `2`. Distance `= (1+3) - 2 = 2`. `best[(1,3)] = 2`.
- Process `(2,6)R`: BIT at `y=6` gets `8`.
- Process `(4,7)R`: BIT at `y=7` gets `11`.
- Process `(5,2)R`: BIT at `y=2` gets `7`.
- Process `(7,5)F`: query prefix `y <= 5` gives max over `y=1 (2)`, `y=2 (7)` → `7`. Distance `= (7+5) - 7 = 5`. `best[(7,5)] = 5`.

Other reflections only find larger or equal distances for these points (the nearest stones are already in the lower-left quadrant), so the values stay `2` and `5`.

Final: max over free points of `best` = `max(2, 5) = 5`. Matches the expected output.

Check of the free point `(7,5)` by hand: distances to reserved are `(1,1)=10`, `(5,2)=5`, `(2,6)=6`, `(4,7)=5`. Minimum is 5. Consistent.

## Pitfalls

- Forgetting one of the four quadrants. A reserved point directly up-left of a free point is only caught by a reflection.
- Strict vs non-strict inequality on ties. Use `<=` in the lower-left condition and process reserved before free at equal `x`; otherwise a point on the same column can be missed.
- Using `x + y` alone for every quadrant. The key changes per quadrant, which is why reflection is needed.
- Forgetting to reset the Fenwick tree between reflections.
- The answer can be up to about `2e6`, so `int` is fine, but intermediate sums should still be treated carefully.
- Assuming the grid BFS is feasible. It is not, with coordinates up to `1e6`.

## Complexity

- Time: `O(4 * (n + m) * log C)` with `C = 1e6`, about `8e7` simple operations.
- Space: `O(n + m + C)`.

## Key takeaway

Nearest point under Manhattan distance = four one-sided problems. In each quadrant, the distance is a linear function of one key, so a sweep plus a prefix-max Fenwick tree finds the nearest point.
