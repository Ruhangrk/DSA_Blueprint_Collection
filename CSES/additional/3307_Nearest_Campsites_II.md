# [3307 — Nearest Campsites II](https://cses.fi/problemset/task/3307)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems I  
**Tags:** Manhattan distance, nearest neighbor, sweep line, Fenwick tree (BIT)  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A campground is a grid. Each square holds at most one campsite, and every campsite is either reserved or free. The distance between squares `(x1, y1)` and `(x2, y2)` is the Manhattan distance `|x1 - x2| + |y1 - y2|`.

For each free campsite, find the distance to the nearest reserved campsite.

### Input

- The first line has two integers `n` and `m`: the number of reserved and free campsites.
- The next `n` lines describe the reserved campsites, each with `x y`.
- The next `m` lines describe the free campsites, each with `x y`.

Each square contains at most one campsite.

### Output

Print `m` integers: the distance from each free campsite to its nearest reserved campsite, in input order.

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
2 5
```

**Explanation:** The free campsite on the left, `(1, 3)`, has nearest reserved distance 2. The free campsite `(7, 5)` has nearest reserved distance 5.

---

## What this problem really is

For each query point, find the nearest data point under Manhattan distance. This is an offline nearest-neighbor problem in 2D with `n` data points and `m` queries.

## What to use

| Tool | Why |
|------|-----|
| Quadrant decomposition | Reduces the 2D nearest query to 4 one-sided queries |
| Coordinate reflection | Reuses one sweep routine for every quadrant |
| Fenwick tree with prefix max | Answers "best key among points with y <= qy" in `O(log C)` |
| Output indexed by query id | Keeps answers in input order after sorting |

KD-trees also work, but the sweep is simpler and has a predictable constant.

## Approach

**Root idea:** Manhattan distance to a point in the lower-left quadrant is linear in one combined key. So each quadrant gives a "best value" query that a prefix-max BIT answers in logarithmic time.

**Mental picture:** each free campsite shoots a beam toward its lower-left region. Every reserved campsite inside that region is at distance `(fx + fy) - (rx + ry)`. The nearest one is the one with the largest `rx + ry`, i.e. the stone whose diagonal sum is closest to the query's diagonal sum from below. Rotate the board three more times to cover the other three regions. Every reserved point lies in at least one of the four closed quadrants of each free point, so the four minima cover all candidates.

**How that becomes the method**

1. For each of the four reflections (`x -> X - x` and/or `y -> Y - y`), build the list of reserved and free points.
2. Sort by `x`. At equal `x`, put reserved before free.
3. Sweep. For a reserved point `(rx, ry)`: BIT update at `ry` with `rx + ry`, prefix max.
4. For a free point `(fx, fy)` with query id `q`: `cand = (fx + fy) - bit.query(fy)`, if the query is non-empty. Update `ans[q] = min(ans[q], cand)`.
5. After four reflections, print `ans[0..m-1]` in order.

**Data structures:**
- Event list sorted by `x`, storing type, coordinates, and query id for free points.
- Fenwick tree `bit[1..C]`: prefix max of `rx + ry` over reserved points with `ry <= query`. Reset per reflection.
- `ans[q]`: best distance found for free point `q`, initialized to infinity.

## Walkthrough (on a sample)

Reserved: `(1,1)`, `(5,2)`, `(2,6)`, `(4,7)`. Free (queries): `q0 = (1,3)`, `q1 = (7,5)`.

**Reflection 1 (lower-left).** Events sorted by `x`: `(1,1)R`, `(1,3)F q0`, `(2,6)R`, `(4,7)R`, `(5,2)R`, `(7,5)F q1`.

- `(1,1)R`: bit[1] = 2.
- `(1,3)F q0`: prefix max over `y <= 3` → 2. `cand = 4 - 2 = 2`. `ans[q0] = 2`.
- `(2,6)R`: bit[6] = 8.
- `(4,7)R`: bit[7] = 11.
- `(5,2)R`: bit[2] = 7.
- `(7,5)F q1`: prefix max over `y <= 5` → max(2, 7) = 7. `cand = 12 - 7 = 5`. `ans[q1] = 5`.

**Other reflections.** Each reflection re-checks the same points in another quadrant. For `q0 = (1,3)`, nothing is to the left of `x = 1` except the points with `x = 1` already handled, and the other quadrants can only give distances `>= 2` (the true minimum is `2` from `(1,1)`, which is already counted). So `ans` stays `2`. For `q1 = (7,5)`, the upper-left and lower-right quadrants contain `(2,6)` and `(4,7)` with distances `6` and `5` respectively, which do not beat `5`. Final: `ans = [2, 5]`.

Output: `2 5`. Matches.

## Pitfalls

- Missing a quadrant. A reserved point up-left of a free point is only caught by a reflection in `x`.
- Equal coordinates. If reserved and free share an `x`, process reserved first; otherwise a point at distance 0 in `x` is skipped.
- Forgetting to reset the BIT between reflections, which silently mixes quadrant data.
- Printing in sorted order. Answers must be printed in the original input order, so keep the query index.
- Using `int` for the key `rx + ry` is fine (`<= 2e6`), but do not confuse the Fenwick "max" with "min".

## Complexity

- Time: `O(4 * (n + m) * log C)`, with `C = 1e6`.
- Space: `O(n + m + C)`.

## Key takeaway

Manhattan nearest neighbor in the plane is four independent one-sided problems. Each becomes "max of `x + y` over a prefix in `y`", which a sweep with a Fenwick prefix-max answers.
