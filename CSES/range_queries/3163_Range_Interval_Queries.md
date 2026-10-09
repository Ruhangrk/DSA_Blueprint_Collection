# [3163 — Range Interval Queries](https://cses.fi/problemset/task/3163)

**Platform:** CSES  
**Source:** CSES Problem Set — Range Queries  
**Tags:** merge sort tree, wavelet tree, offline Fenwick, 2D counting  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an array `x` of `n` integers, your task is to process `q` queries of the form: how many integers `i` satisfy `a <= i <= b` and `c <= x[i] <= d`?

### Input

The first line has two integers `n` and `q`: the number of values and queries.

The second line has `n` integers `x1, x2, ..., xn`: the array values.

Finally, there are `q` lines describing the queries. Each line has four integers `a`, `b`, `c` and `d`.

### Output

Print the result of each query.

### Constraints

- `1 <= n, q <= 2e5`
- `1 <= xi <= 1e9`
- `1 <= a <= b <= n`
- `1 <= c <= d <= 1e9`

### Examples

**Input**
```
8 4
3 2 4 5 1 1 5 3
2 4 2 4
5 6 2 9
1 8 1 5
3 3 4 4
```

**Output**
```
2
0
8
1
```

---

## What this problem really is

**2D range count:** points `(i, x[i])` on a plane; each query asks how many points lie inside the axis-aligned box `[a..b] x [c..d]`. Static array, many queries.

## What to use

| Tool | Why |
|------|-----|
| Merge-sort tree (segtree of sorted vectors) | Index segment → sorted `x` values; binary search count in `[c,d]` |
| Or wavelet tree | Same query, often lighter constants |
| Or offline + Fenwick on values | Sort events by index; add `x[i]` as you sweep `i` |

`n,q` up to `2e5` rules out per-query scans.

## Approach

**Root idea:** fix the index range with a segment tree; at each node keep the values in that index segment **sorted**, so “how many in `[c,d]`?” is two binary searches (or `upper_bound` − `lower_bound`).

**Mental picture:** each segtree node is a sorted bag of the numbers sitting under that index interval. A query `[a,b]` pulls `O(log n)` bags; for each bag count values in `[c,d]` and add.

**Data structures**

- Segment tree over indices `1..n`.
- Node stores `vector<long long>` (or `int`) of values in its range, **sorted**.
- Build: leaf = `{x[i]}`; parent = merge of children’s sorted vectors (like merge-sort).

**Query on `[a,b]` for values in `[c,d]`**

1. Decompose `[a,b]` into `O(log n)` nodes.
2. For each node `v`: `cnt += upper_bound(v, d) - lower_bound(v, c)`.
3. Sum those counts.

**Steps**

1. Build merge-sort tree from `x`.
2. For each query `(a,b,c,d)`: print the summed binary-search counts.

(Offline Fenwick alternative: compress all `xi` and query bounds; process queries sorted by `b`, add points as index increases, answer with Fenwick range sum on values — same asymptotics, different picture.)

## Walkthrough (on a sample)

Array: `3 2 4 5 1 1 5 3`.

Query `2 4 2 4`: indices `2..4` → values `2,4,5`. In `[2,4]`: `2` and `4` → **2**.

Query `5 6 2 9`: values `1,1` — none in `[2,9]` → **0**.

Query `1 8 1 5`: all eight values are in `[1,5]` → **8**.

Query `3 3 4 4`: only `x[3]=4` → **1**.

On a merge-sort tree, `[2..4]` might be one node or a few; each node’s sorted vector contributes via `lower_bound`/`upper_bound`.

## Pitfalls

- Values and `c,d` up to `1e9` — compress if using Fenwick; merge-sort tree stores raw values fine.
- Off-by-one in binary search bounds (`<= d` vs `< d`).
- Memory: merge-sort tree is `O(n log n)` ints — OK at `2e5` but don’t copy vectors carelessly in queries.
- Building with `push_back` without reserving is fine; merging must keep sorted order.

## Complexity

- Time: build `O(n log n)`, each query `O(log^2 n)`  
- Space: `O(n log n)`

## Key takeaway

**Count values in `[c,d]` over indices `[a,b]` = 2D orthogonal range count** — merge-sort tree (sorted bags per index segment) or offline Fenwick on compressed values.
