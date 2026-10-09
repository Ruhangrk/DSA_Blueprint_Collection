# [1736 — Polynomial Queries](https://cses.fi/problemset/task/1736)

**Platform:** CSES  
**Source:** CSES Problem Set — Range Queries  
**Tags:** lazy segment tree, arithmetic progression update, range sum  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to maintain an array of `n` values and efficiently process the following types of queries:

1. Increase the first value in range `[a,b]` by `1`, the second value by `2`, the third value by `3`, and so on.
2. Calculate the sum of values in range `[a,b]`.

### Input

The first input line has two integers `n` and `q`: the size of the array and the number of queries.

The next line has `n` values `t1, t2, ..., tn`: the initial contents of the array.

Finally, there are `q` lines describing the queries. The format of each line is either `"1 a b"` or `"2 a b"`.

### Output

Print the answer to each sum query.

### Constraints

- `1 <= n, q <= 2e5`
- `1 <= ti <= 1e6`
- `1 <= a <= b <= n`

### Examples

**Input**
```
5 3
4 2 3 1 7
2 1 5
1 1 5
2 1 5
```

**Output**
```
17
32
```

---

## What this problem really is

Range **arithmetic-progression add** (add `1,2,3,...` along `[a,b]`) plus range **sum**. Need lazy propagation that can represent AP updates, not only constant adds.

## What to use

| Tool | Why |
|------|-----|
| Segment tree with lazy AP | Push “start value + common difference” onto nodes |
| Closed form for AP sum | Know how much an AP adds to a segment’s total |
| `long long` | Sums grow fast |

## Approach

**Root idea:** an update on `[a,b]` adds the sequence `1,2,...,(b-a+1)` at positions `a..b`. On any subsegment `[L,R]` inside the update, that is still an AP: first term `L-a+1`, difference `1`. Lazy tags can store `(base, step)` meaning “add `base + step*(i-L)` at index `i` in this node’s range”.

**Mental picture:** dropping a rising ramp over `[a,b]`. Each segtree node holds the total height under its interval; lazy remembers unfinished ramps to push down later.

**Data structures**

- Segtree node: `sum` over its range.
- Lazy per node: pair `(add0, d)` — at the left index of the node, add `add0`; each next index adds `d` more (here `d` is always `1` for this problem’s updates, but keeping `d` makes push/merge clean). For this task every type-`1` has step `1` in absolute index space.

**Applying AP `(first, diff)` to a node covering `len` positions**

Sum added = `len * first + diff * (0+1+...+len-1)` = `len * first + diff * len*(len-1)/2`.

**Push:** split the AP so the right child starts at `first + diff * left_len`.

**Type `1 a b`:** range-apply AP with `first = 1` at index `a` (in absolute terms: at position `i` add `i-a+1`).

**Type `2 a b`:** standard range sum query (push lazies on the way).

**Steps**

1. Build segtree from initial `t` (sums only).
2. Update: lazy-apply the ramp on `[a,b]`.
3. Query: return pushed range sum.

## Walkthrough (on a sample)

Initial sum `[1..5]` = `4+2+3+1+7 = 17`.

Type `1 1 5`: add `1,2,3,4,5` → array becomes `5,4,6,5,12`, total `32`.

A node covering `[3..5]` under that update gets AP `3,4,5` (first=`3`, diff=`1`); sum added = `3+4+5 = 12`, matching the formula `3*3 + 1*3*2/2 = 12`.

## Pitfalls

- Treating the update as “add `1` to everyone” — wrong; amounts depend on offset from `a`.
- Wrong right-child starting value when pushing lazy.
- `int` overflow on `len*(len-1)/2` contributions.
- Forgetting to compose lazies when a node already has a pending ramp.

## Complexity

- Time: `O((n + q) log n)`  
- Space: `O(n)`

## Key takeaway

**AP range add + range sum = lazy segtree whose tag is an arithmetic progression**, with a closed-form sum and correct split on push.
