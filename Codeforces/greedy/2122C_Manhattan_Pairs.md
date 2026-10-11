# [2122C — Manhattan Pairs](https://codeforces.com/problemset/problem/2122/C)

**Platform:** Codeforces
**Source:** Order Capital Round 1 (Codeforces Round 1038, Div. 1 + Div. 2)
**Tags:** constructive algorithms, geometry, greedy, math, sortings
**Difficulty / Rating:** 1700
**Time limit:** 2 seconds
**Memory limit:** 256 megabytes
**Editorial:** [Round 1038 editorial (Problem C section)](https://codeforces.com/blog/entry/144876)

---

## Problem Statement

You are given `n` points `(x[i], y[i])` on a 2D plane, where `n` is even. Select `n / 2` disjoint pairs `(a[i], b[i])` to maximize the sum of Manhattan distances between the points in each pair. In other words, maximize

`sum over i = 1..n/2 of |x[a[i]] - x[b[i]]| + |y[a[i]] - y[b[i]]|`.

### Input

The first line contains `t` (`1 <= t <= 1e4`), the number of test cases. Each test case has:
- a line with an even integer `n` (`2 <= n <= 2e5`);
- `n` lines, the `i`-th containing `x[i]` and `y[i]` (`-1e6 <= x[i], y[i] <= 1e6`).

The sum of `n` over all test cases does not exceed `2e5`.

### Output

For each test case, print `n / 2` lines. The `i`-th line contains two integers `a[i]` and `b[i]`, the indices of the points in the `i`-th pair. If there are multiple solutions, print any of them.

### Constraints

- `1 <= t <= 1e4`
- `2 <= n <= 2e5`, with `n` even
- `-1e6 <= x[i], y[i] <= 1e6`
- Sum of `n` over all test cases `<= 2e5`

### Examples

Input:

```
2
4
1 1
3 0
4 2
3 4
10
-1 -1
-1 2
-2 -2
-2 0
0 2
2 -3
-4 -4
-4 -2
0 1
-4 -2
```

Output (one valid answer; any valid answer is accepted):

```
4 1
2 3
8 1
9 10
7 5
2 3
6 4
```

### Note

- Test 1: the pairs `(1, 4)` and `(2, 3)` give a distance sum of `5 + 3 = 8`.
- Test 2: the pairs `(1, 8)`, `(9, 10)`, `(5, 7)`, `(2, 3)`, `(4, 6)` give a distance sum of `4 + 7 + 10 + 5 + 7 = 33`.

---

## What this problem really is

Pair up the points so that the total Manhattan distance is as large as possible. Manhattan distance adds the horizontal gap and the vertical gap, so the problem splits into two separate one-dimensional problems that must be solved together.

## What to use

| Tool | Why |
|------|-----|
| Sorting by `x` and by `y` | Finds the lower half and upper half of the points in each coordinate. |
| Four buckets | Each point falls into one of four groups: lower-x or upper-x, combined with lower-y or upper-y. Pairing must respect these groups. |
| Greedy pairing across buckets | In 1D, the best pairing puts the smallest half against the largest half. Doing this in both coordinates at once keeps both parts optimal. |

## Approach

**Root idea:** In one dimension, the best pairing is to match the smallest half of the values with the largest half. Manhattan distance is the x-part plus the y-part, so if one pairing is optimal in both coordinates at the same time, it is optimal overall.

**Mental picture:** Draw a vertical line through the median `x` and a horizontal line through the median `y`. The plane splits into four quadrants: lower-x with lower-y (LL), lower-x with upper-y (LR), upper-x with lower-y (RL), and upper-x with upper-y (RR). Every pair should cross the vertical line and the horizontal line. That means LL pairs with RR, and LR pairs with RL. Both pairings cross both lines, so both coordinates contribute their maximum.

**Why the pairing always exists:** Let the four quadrant sizes be LL, LR, RL, RR. Each half in x has `n/2` points, so `LL + LR = RL + RR`. Each half in y also has `n/2` points, so `LL + RL = LR + RR`. Subtracting these two equations gives `LR = RL`, and then `LL = RR`. So LL and RR have equal size, and LR and RL have equal size, which means the pairing is always possible.

**How that becomes the method:**
1. Sort the indices by `x`. The first `n/2` are lower-x, the rest are upper-x.
2. Sort the indices by `y`. The first `n/2` are lower-y, the rest are upper-y.
3. Put each point into the bucket for its (x-half, y-half) pair.
4. Pair the points of bucket LL with the points of bucket RR, one to one.
5. Pair the points of bucket LR with the points of bucket RL, one to one.
6. Print the pairs using 1-based indices.

**Data structures:**
- Two sorted index arrays (one by `x`, one by `y`) to decide each point's halves.
- Four lists, one per quadrant, holding the point indices.

## Walkthrough (on a sample)

Test 1: points `1: (1, 1)`, `2: (3, 0)`, `3: (4, 2)`, `4: (3, 4)`.

- Sort by `x`: `1 (1)`, `2 (3)`, `4 (3)`, `3 (4)`. Lower-x = `{1, 2}`, upper-x = `{4, 3}`.
- Sort by `y`: `2 (0)`, `1 (1)`, `3 (2)`, `4 (4)`. Lower-y = `{2, 1}`, upper-y = `{3, 4}`.
- Buckets: LL = `{1, 2}`, RR = `{3, 4}`, LR = empty, RL = empty.
- Pair LL with RR: `(1, 4)` and `(2, 3)`.
- Distances: `(1, 4)` gives `|1 - 3| + |1 - 4| = 2 + 3 = 5`. `(2, 3)` gives `|3 - 4| + |0 - 2| = 1 + 2 = 3`.
- Total: `8`, which matches the optimum from the statement.

## Pitfalls

- **Pairing within one half:** Two points both in lower-x do not cross the median, so their x-gap is wasted. Always pair across the x-median and across the y-median.
- **Using only one coordinate:** Sorting by `x` alone ignores the `y` gap. The buckets need both sorts.
- **Mismatched bucket sizes:** The proof says LL matches RR in size and LR matches RL. If your sizes differ, a sort or split is wrong.
- **Ties at the median:** Break ties with the index so that exactly `n/2` points go into each half. Any consistent split works.
- **Output format:** Print exactly `n/2` lines per test, using 1-based indices.

## Complexity

- Time: `O(n log n)` for the two sorts, per test. Over all tests, `O(sum of n * log n)`.
- Space: `O(n)`.

## Key takeaway

Split the points by the median in each coordinate and pair points that lie on opposite sides in both. When one pairing is optimal for every coordinate separately, it is optimal for their sum.
