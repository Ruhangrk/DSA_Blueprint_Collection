# [3226 — Subarray Sum Queries II](https://cses.fi/problemset/task/3226)

**Platform:** CSES  
**Source:** CSES Problem Set — Range Queries  
**Tags:** segment tree, max subarray sum, range query  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array of `n` integers and `q` queries. In each query, your task is to calculate the maximum subarray sum in the range `[a,b]`.

Empty subarrays (with sum `0`) are allowed.

### Input

The first line contains two integers `n` and `q`: the number of elements and the number of queries.

Then there are `n` integers `x1, x2, ..., xn`: the contents of the array.

Finally there are `q` lines that describe the queries. Each line has two integers `a` and `b`.

### Output

Print the answer for each query.

### Constraints

- `1 <= n, q <= 2e5`
- `-1e9 <= xi <= 1e9`
- `1 <= a <= b <= n`

### Examples

**Input**
```
8 4
2 5 1 -2 3 -1 -7 1
2 4
2 5
6 7
4 8
```

**Output**
```
6
7
0
3
```

---

## What this problem really is

**Static** array. For each query range `[a,b]`, report the maximum contiguous subarray sum **inside that range only** (empty allowed → at least `0`).

## What to use

| Tool | Why |
|------|-----|
| Segment tree with `{sum, pref, suff, best}` | Same merge as global max-subarray, but query merges `O(log n)` nodes covering `[a,b]` |
| Identity / empty node | Neutral element when combining query pieces |
| `long long` | Large partial sums |

No updates — you could also use sparse table tricks in special cases, but the four-field segtree is the clean general tool.

## Approach

**Root idea:** the max subarray inside `[a,b]` is the `best` field of the segment that is the merge of all tree nodes covering `[a,b]`, using the same combine as for whole-array max subarray.

**Mental picture:** you don’t rebuild Kadane from scratch each query. You glue a few precomputed chunks left-to-right; each chunk already knows its best internal piece and its best prefix/suffix so a crossing piece can form at glue points.

**Data structures — node**

```
sum, pref, suff, best
```

**Merge `L` then `R` (order matters)**

```
sum  = L.sum + R.sum
pref = max(L.pref, L.sum + R.pref)
suff = max(R.suff, R.sum + L.suff)
best = max(L.best, R.best, L.suff + R.pref)
```

Leaf for `v`: all four fields = `v`.

**Neutral node** (for “no segment yet” when folding the query):  
`sum = 0`, `pref = suff = best = -INF` (a value more negative than any real sum, e.g. `-4e18`). After the full merge of `[a,b]`, answer = `max(0, best)` for the empty-subarray rule.

**Steps**

1. Build the segtree once from `x`.
2. For each query `[a,b]`: merge covering nodes left-to-right with the combine above; print `max(0, best)`.

## Walkthrough (on a sample)

Array: `2 5 1 -2 3 -1 -7 1`.

`[2..4] = 5,1,-2` → best `5+1 = 6` → **6**.

`[2..5] = 5,1,-2,3` → best `5+1-2+3 = 7` → **7**.

`[6..7] = -1,-7` → all negative → empty wins → **0**.

`[4..8] = -2,3,-1,-7,1` → best `3` alone → **3**.

A query merge might combine e.g. node `[2..2]`, `[3..4]`, `[5..5]` in order; each merge updates `pref`/`suff`/`best` so the crossing `5+1-2+3` appears as `L.suff + R.pref` at the right glue step.

## Pitfalls

- Merging query nodes in the **wrong order** (must be left-to-right by index).
- Neutral `best = 0` instead of `-INF` — wrongly treats “empty fold” as a real answer mid-merge.
- Forgetting `max(0, …)` when the range is all negative.
- Using only a sparse table on `best` of fixed blocks — that cannot form crossing subarrays across block boundaries correctly without the full merge.

## Complexity

- Time: `O((n + q) log n)`  
- Space: `O(n)`

## Key takeaway

**Range max-subarray sum = query a `(sum, pref, suff, best)` segtree** over `[a,b]` with Kadane merge; clamp with `0` if empty subarrays are allowed.
