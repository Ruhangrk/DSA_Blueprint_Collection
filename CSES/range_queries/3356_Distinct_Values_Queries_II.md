# [3356 — Distinct Values Queries II](https://cses.fi/problemset/task/3356)

**Platform:** CSES  
**Source:** CSES Problem Set — Range Queries  
**Tags:** segment tree, next occurrence, sets, point update  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an array of `n` integers, your task is to process `q` queries of the following types:

1. update the value at position `k` to `u`
2. check if every value in range `[a,b]` is distinct

### Input

The first line has two integers `n` and `q`: the number of values and queries.

The second line has `n` integers `x1, x2, ..., xn`: the array values.

Finally, there are `q` lines describing the queries. Each line has three integers: either `"1 k u"` or `"2 a b"`.

### Output

For each query of type `2`, print `YES` if every value in the range is distinct and `NO` otherwise.

### Constraints

- `1 <= n, q <= 2e5`
- `1 <= xi, u <= 1e9`
- `1 <= k <= n`
- `1 <= a <= b <= n`

### Examples

**Input**
```
5 4
3 2 7 2 8
2 3 5
2 2 5
1 2 9
2 2 5
```

**Output**
```
YES
NO
YES
```

---

## What this problem really is

Point updates + ask whether a subarray is **duplicate-free** (all elements unique). Counting distinct values is unnecessary — only a yes/no collision check.

## What to use

| Tool | Why |
|------|-----|
| `next[i]` = next index with the same value | Duplicate in `[a,b]` iff some `i` in `[a,b]` has `next[i] <= b` |
| Segtree of `next` (range minimum) | Query: `min(next[a..b]) > b`? |
| `set` of positions per value | Rebuild `next` links on update |

## Approach

**Root idea:** the range `[a,b]` has a repeated value iff there exist two equal elements with both indices in `[a,b]`. For each index `i`, let `next[i]` be the smallest `j > i` with `x[j] == x[i]` (or `INF`). Then `[a,b]` is all-distinct **iff** every `next[i]` for `i in [a,b]` is `> b`, i.e. `min(next[a..b]) > b`.

**Mental picture:** each value shoots a “same-color arrow” to its next copy. A range is clean iff no arrow that starts inside the range lands still inside it.

**Data structures**

- `map` / compressed `x` → `set<int>` of indices holding that value.
- Array `next[1..n]` (+ Fenwick/segtree RMQ on `next`).
- When building: for each value’s sorted indices `p1 < p2 < ...`, set `next[pi] = p(i+1)`, and `next[last] = INF`.

**Point update `k := u`**

1. Remove `k` from old value’s set; fix `next` of the previous index of the old value (now points to what was after `k`, or `INF`).
2. Insert `k` into new value’s set; set `next[prev] = k`, `next[k] = succ` (neighbors in the set).
3. Point-update those `O(1)` changed `next` entries in the RMQ tree.

**Type `2 a b`:** YES iff `rangeMin(a,b) > b`.

## Walkthrough (on a sample)

Array `3 2 7 2 8`. For value `2`, indices `2` and `4` → `next[2] = 4`.

`[3..5] = 7,2,8`: no internal arrows → **YES**.

`[2..5] = 2,7,2,8`: `next[2] = 4 <= 5` → **NO**.

Set index `2` to `9`: arrow for `2` disappears; `[2..5]` now distinct → **YES**.

## Pitfalls

- Using `min(next) >= b` instead of `> b` (arrow landing exactly on `b` is still a duplicate).
- Forgetting to update the **previous** index’s `next` when deleting/inserting.
- `INF` must be `> n` (e.g. `n+1`).

## Complexity

- Time: `O((n + q) log n)`  
- Space: `O(n)`

## Key takeaway

**Subarray all-distinct with updates = RMQ on next-equal indices** — answer YES iff `min(next[a..b]) > b`.
