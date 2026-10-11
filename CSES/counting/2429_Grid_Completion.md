# [2429 — Grid Completion](https://cses.fi/problemset/task/2429)

**Platform:** CSES  
**Source:** CSES Problem Set — Counting Problems  
**Tags:** inclusion-exclusion, permutations, combinatorics  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  
**Editorial:** [USACO Guide — Grid Completion](https://usaco.guide/problems/cses-2429-grid-completion/solution)

---

## Problem Statement

Your task is to create an `n x n` grid whose each row and column has exactly one `A` and `B`. Some of the characters have already been placed. In how many ways can you complete the grid?

### Input

The first input line has an integer `n`: the size of the grid.

After this, there are `n` lines that describe the grid. Each line has `n` characters: `.` means an empty square, and `A` and `B` show the characters already placed.

You can assume that every row and column has at most one `A` and `B`.

### Output

Print one integer: the number of ways modulo `1e9+7`.

### Constraints

- `2 <= n <= 500`

### Examples

**Input**
```
5
.....
..AB.
.....
B....
...A.
```

**Output**
```
16
```

---

## What this problem really is

Complete two Latin-like placements: a permutation of columns for all `A`s and another for all `B`s, respecting prefilled cells, with `A` and `B` never sharing a cell in the same row (and column uniqueness already encoded by permutations).

## What to use

| Tool | Why |
|------|-----|
| Two permutations | Row `r` gets `A` in column `p[r]` and `B` in column `q[r]`; each value used once |
| Category counts | Prefills partition rows/columns into a few types |
| Inclusion-exclusion | Count assignments allowing “same cell” collisions, then cancel them with signs |

## Approach

**Root idea:** ignore the “`A` and `B` distinct in a row” constraint first, assign free `A` columns and free `B` columns like matching free rows to free columns, then use PIE over rows that are forced to collide (`p[r]=q[r]`).

**Mental picture:** each row needs an `A`-dot and a `B`-dot. Prefills pin some dots. Free `A`s are a bijection from rows-missing-`A` onto columns-missing-`A`; same for `B`. A bad completion is one where some row’s two dots land on the same square — treat “this row collides” as a property and IE over those properties.

**How that becomes the method**

Read the grid; for each row store `p[r]` = column of prefilled `A` or `-1`, and `q[r]` similarly for `B`.

Define counts (names match common writeups):

- `c0` = rows with neither `A` nor `B`
- `c1` = rows with only `B` at column `c`, and no prefilled `A` exists in column `c` (so that `B` could still “collide” if we later put `A` there)
- `c2` = rows with only `A` at column `c`, and no prefilled `B` in column `c`
- `c3` = columns with neither letter
- `c4` = columns with no `A` (= number of rows still needing an `A`)
- `c5` = columns with no `B`

Then:

`ans = sum_{i,j,k} (-1)^{i+j+k} * C(c0,i) * C(c1,j) * C(c2,k) * C(c3,i) * i! * (c4-i-j)! * (c5-i-k)!`

Interpretation sketch:

- Choose `i` empty rows and `i` fully free columns and pair them (`C(c0,i)*C(c3,i)*i!`) as deliberate collision sites.
- Choose `j` of the `c1` rows and `k` of the `c2` rows as additional collision sites.
- Remaining free `A` / `B` slots permute arbitrarily: `(c4-i-j)!` and `(c5-i-k)!`.
- Sign `(-1)^{i+j+k}` cancels configurations with collisions.

Optimize to `O(n^2)` by fixing `i` and factoring the `j` and `k` sums (they separate).

**Data structures:** arrays `p[n]`, `q[n]`; precomputed `fact`, `invfact`, binomials; nested loops or factored sums.

**Short steps:** parse prefills → classify counts → evaluate IE sum mod `1e9+7`.

## Walkthrough (on a sample)

Grid `5 x 5`:

| row | prefills |
|-----|----------|
| 0 | none |
| 1 | `A` at col `2`, `B` at col `3` |
| 2 | none |
| 3 | `B` at col `0` |
| 4 | `A` at col `3` |

- Columns used by `A`: `{2,3}`; by `B`: `{0,3}`.
- `c0 = 2` (rows `0,2`), `c3 = 2` (cols `1,4`).
- `c1 = 1` (row `3`: `B` in col `0`, and col `0` has no `A`).
- `c2 = 0` (row `4`’s `A` sits in col `3`, which already has a `B`).
- `c4 = c5 = 3` (three free `A`s and three free `B`s).

Plug into the IE sum (only small `i,j,k` ranges). The signed total is `16`.

Sanity: row `1` is fully fixed (`A`/`B` distinct). Remaining work is matching free letters without creating a double occupancy in any row — IE does that counting.

## Pitfalls

- Forgetting that a prefilled `A` and `B` in the **same** row must already be different columns (guaranteed by statement assumptions, but your classification should not double-count finished rows).
- Misclassifying `c1`/`c2`: the “column has no opposite letter” condition matters.
- Negative modular results from `(-1)^{...}` — always normalize to `[0, MOD)`.
- `O(n^3)` triple loop is tight at `n=500`; prefer the factored `O(n^2)` form.

## Complexity

- Time: `O(n^2)` with factored IE (or `O(n^3)` naive)
- Space: `O(n)` for facts and row maps

## Key takeaway

Two constrained permutations plus “no same cell” → classify free rows/columns, then inclusion-exclusion over collision rows.
