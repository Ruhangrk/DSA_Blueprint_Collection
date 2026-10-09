# [1734 — Distinct Values Queries](https://cses.fi/problemset/task/1734)

**Platform:** CSES  
**Source:** CSES Problem Set — Range Queries  
**Tags:** offline queries, Fenwick tree, last occurrence, Mo's algorithm  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array of `n` integers and `q` queries of the form: how many distinct values are there in a range `[a,b]`?

### Input

The first input line has two integers `n` and `q`: the array size and number of queries.

The next line has `n` integers `x1, x2, ..., xn`: the array values.

Finally, there are `q` lines describing the queries. Each line has two integers `a` and `b`.

### Output

For each query, print the number of distinct values in the range.

### Constraints

- `1 <= n, q <= 2e5`
- `1 <= xi <= 1e9`
- `1 <= a <= b <= n`

### Examples

**Input**
```
5 3
3 2 3 1 2
1 3
2 4
1 5
```

**Output**
```
2
3
3
```

---

## What this problem really is

Static array, many range queries: **count unique values** in `[a,b]`. Need something faster than scanning each range.

## What to use

| Tool | Why |
|------|-----|
| Offline + Fenwick (BIT) | Sweep by right endpoint; maintain “latest index of each value” |
| Coordinate compression / map | `xi` up to `1e9` |
| (Alt.) Mo’s algorithm | Also works at `2e5` with careful constants |

## Approach

**Root idea:** a value is “new” in `[a,b]` at the **leftmost** occurrence inside that range. Equivalently: position `i` contributes `1` to query `[a,b]` iff `a <= i <= b` and the previous occurrence of `x[i]` is `< a` (or none).

**Mental picture:** walk left to right. When you see a number, erase its old mark and place a coin at the current index. A query ending at `b` asks: how many coins sit in `[a,b]`? Each distinct value has at most one coin — its latest position so far.

**Data structures**

- `last[value]` → previous index where that value appeared (`0` if none). Compress values or use `map`/`unordered_map`.
- Fenwick `bit[1..n]`: `+1` at an index when it becomes the current last occurrence; `-1` when that role moves right.
- Queries stored as `(a, b, id)`, sorted by `b` ascending.

**Steps**

1. Sort queries by increasing `b`.
2. `r = 0`. For each query in that order:
   - While `r < b`: `r++`; if `last[x[r]] > 0`, Fenwick-add `-1` at `last[x[r]]`; Fenwick-add `+1` at `r`; set `last[x[r]] = r`.
   - Answer = Fenwick range sum on `[a, b]` (or `prefix(b) - prefix(a-1)`).
3. Print answers in original order.

## Walkthrough (on a sample)

Array: `3 2 3 1 2`.

After processing up to `b=3` (values `3,2,3`): last coins at index `2` (for `2`) and `3` (for `3`). Query `[1,3]`: sum on `[1,3]` = **2**.

Up to `b=4`: add `1` at index `4` → three coins. Query `[2,4]`: coins at `2,3,4` → **3**.

Up to `b=5`: value `2` moves coin from `2` to `5` → coins at `3,4,5` → query `[1,5]` = **3**.

## Pitfalls

- Updating Fenwick with `+1` only and never removing the old last occurrence → overcount.
- Forgetting to restore answer order after sorting by `b`.
- `xi` not compressed when using arrays keyed by value.

## Complexity

- Time: `O((n + q) log n)` after sorting  
- Space: `O(n + q)`

## Key takeaway

**Distinct count on ranges = offline sweep by `b` + Fenwick of last occurrences** — each value keeps one live index; range sum = distinct count.
