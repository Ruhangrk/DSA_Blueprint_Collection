# [1649 — Dynamic Range Minimum Queries](https://cses.fi/problemset/task/1649)

**Platform:** CSES  
**Source:** CSES Problem Set — Range Queries  
**Tags:** segment tree, point update, RMQ  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an array of `n` integers, your task is to process `q` queries of the following types:

1. update the value at position `k` to `u`
2. what is the minimum value in range `[a,b]`?

### Input

The first input line has two integers `n` and `q`: the number of values and queries.

The second line has `n` integers `x1, x2, ..., xn`: the array values.

Finally, there are `q` lines describing the queries. Each line has three integers: either `"1 k u"` or `"2 a b"`.

### Output

Print the result of each query of type `2`.

### Constraints

- `1 <= n, q <= 2e5`
- `1 <= xi, u <= 1e9`
- `1 <= k <= n`
- `1 <= a <= b <= n`

### Examples

**Input**
```
8 4
3 2 4 5 1 1 5 3
2 1 4
2 5 6
1 2 3
2 1 4
```

**Output**
```
2
1
3
```

---

## What this problem really is

Point updates + range **minimum** queries. Sparse tables can’t handle updates — use a segment tree (or similar).

## What to use

| Tool | Why |
|------|-----|
| Segment tree storing `min` | Combine children with `min` |
| Point update | Set leaf `k` to `u`, recalc ancestors |
| Range query | Recurse on disjoint node segments covering `[a,b]` |

## Approach

**Root idea:** each tree node owns an array segment and stores its minimum. Update climbs from a leaf; query merges `O(log n)` node mins.

**Mental picture:** tournament bracket of mins; change one player’s score and replay the path to the root; ask for the champ of any contiguous block.

**Data structures**

- Segtree array `t[4*n]`; build from `x`.
- `update(k, u)`, `query(a, b) → min`.

**Steps**

1. Build the tree.
2. Type `1`: point set.
3. Type `2`: print range min.

## Walkthrough (on a sample)

`min[1..4]=2`, `min[5..6]=1`; set index `2` to `3`; then `min[1..4]=3`.

## Pitfalls

- Using Fenwick for min with subtract tricks — incorrect.
- Wrong identity for empty (use `+INF` when merging).
- Off-by-one on inclusive bounds.

## Complexity

- Time: `O((n + q) log n)`  
- Space: `O(n)`

## Key takeaway

**Dynamic RMQ = segment tree of mins.** Point update + range min query in `O(log n)`.
