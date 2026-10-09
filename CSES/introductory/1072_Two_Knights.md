# [1072 — Two Knights](https://cses.fi/problemset/task/1072)

**Platform:** CSES  
**Source:** CSES Problem Set — Introductory Problems  
**Tags:** combinatorics, math  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to count for `k = 1, 2, ..., n` the number of ways two knights can be placed on a `k x k` chessboard so that they do not attack each other.

### Input

The only input line contains an integer `n`.

### Output

Print `n` integers: the results.

### Constraints

- `1 <= n <= 10000`

### Examples

**Input**
```
8
```

**Output**
```
0
6
28
96
252
550
1056
1848
```

---

## What this problem really is

For each board size `k`, count unordered pairs of distinct squares where two knights do **not** attack. Knights attack in an L-shape (`2` by `1`).

## What to use

| Tool | Why |
|------|-----|
| Total placements | `C(k^2, 2) = k^2 * (k^2 - 1) / 2` |
| Count attacking pairs | Subtract from total |
| `2 x 3` / `3 x 2` blocks | Each such block has exactly 2 attacking knight placements |

## Approach

**Root idea:** answer(`k`) = (all ways to choose 2 squares) − (ways they attack).

Attacking geometry: two knights attack iff they sit at opposite corners of a `2 x 3` or `3 x 2` rectangle.

- Number of `2 x 3` placements on `k x k`: `(k-1) * (k-2)` horizontal × vertical positions for the box.
- Each such box contributes **2** attacking pairs.
- Same for `3 x 2`: also `(k-1)*(k-2)` boxes × 2.

So attacking pairs = `4 * (k-1) * (k-2)` (for `k >= 3`; for smaller `k` the product is 0 or the formula still works with care).

```
total = k*k * (k*k - 1) / 2
attack = 4 * (k-1) * (k-2)   // use 0 when k < 3
answer = total - attack
```

**Mental picture:** dump all pairs of squares, then remove those that form a knight’s L.

**Steps**

1. For `k = 1..n`, compute the formula with `long long`.
2. Print each answer on its own line.

## Walkthrough (on a sample)

`k=2`: total `C(4,2)=6`, attack `0` → **6**.

`k=3`: total `C(9,2)=36`, attack `4*2*1=8` → **28**.

Matches the sample prefix.

## Pitfalls

- Ordered pairs (×2) vs unordered — stick to `C(k^2,2)`.
- `int` overflow: `k^4` terms for `k=10000`.
- Forgetting `k=1` → `0`.

## Complexity

- Time: `O(n)`  
- Space: `O(1)`

## Key takeaway

**Safe two-knight placements = `C(k^2,2) - 4(k-1)(k-2)`** — subtract L-attacks from `2x3`/`3x2` boxes.
