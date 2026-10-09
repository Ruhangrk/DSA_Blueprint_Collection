# [2166 — Prefix Sum Queries](https://cses.fi/problemset/task/2166)

**Platform:** CSES  
**Source:** CSES Problem Set — Range Queries  
**Tags:** segment tree, prefix sums, max subarray-style merge  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an array of `n` integers, your task is to process `q` queries of the following types:

1. update the value at position `k` to `u`
2. what is the maximum prefix sum in range `[a,b]`?

Empty prefixes (with sum `0`) are allowed.

### Input

The first input line has two integers `n` and `q`: the number of values and queries.

The second line has `n` integers `x1, x2, ..., xn`: the array values.

Finally, there are `q` lines describing the queries. Each line has three integers: either `"1 k u"` or `"2 a b"`.

### Output

Print the result of each query of type `2`.

### Constraints

- `1 <= n, q <= 2e5`
- `-1e9 <= xi, u <= 1e9`
- `1 <= k <= n`
- `1 <= a <= b <= n`

### Examples

**Input**
```
8 4
1 2 -1 3 1 -5 1 4
2 2 6
1 4 -2
2 2 6
2 3 4
```

**Output**
```
5
2
0
```

---

## What this problem really is

Point updates. For a subarray `x[a..b]`, consider prefixes of that subarray (including empty = `0`); report the **maximum** among those prefix sums.

## What to use

| Tool | Why |
|------|-----|
| Segment tree node `{sum, best_pref}` | Merge left/right: total sum + best prefix inside the segment |
| Point update | Rebuild leaf and parents |
| Allow empty | Answer is `max(0, best_pref)` of the queried node merge |

## Approach

**Root idea:** for a segment, you need (1) its total sum and (2) the max prefix sum starting at its left end. When merging `L` and `R`:

```
sum = L.sum + R.sum
best_pref = max(L.best_pref, L.sum + R.best_pref)
```

A query on `[a,b]` merges `O(log n)` nodes **in left-to-right order** with that combine; then take `max(0, best_pref)`.

**Mental picture:** walking from `a` toward `b`, track running sum; report the highest it ever reaches (or `0` if all negative).

**Steps**

1. Build segtree with the merge above (`best_pref` for a leaf = `max(0, x[i])` or store raw and clamp at query — empty allowed so clamp with `0` at the end).
2. Type `1`: set leaf to `u`.
3. Type `2`: query merge on `[a,b]`, print `max(0, best_pref)`.

Leaf: `sum = x[i]`, `best_pref = x[i]` (then final `max(0,…)` covers empty).

## Walkthrough (on a sample)

On `[2..6] = 2,-1,3,1,-5`, best prefix is `2-1+3+1 = 5`. After setting index `4` to `-2`, best becomes `2`. On `[3..4]` all negative → **`0`**.

## Pitfalls

- Forgetting empty prefix `0`.
- Merging nodes in wrong order.
- `long long` for sums.

## Complexity

- Time: `O((n + q) log n)`  
- Space: `O(n)`

## Key takeaway

**Max prefix sum on a subarray with updates = segtree storing `(sum, best_pref)`** with the standard left/right merge; clamp with `0` for the empty prefix.
