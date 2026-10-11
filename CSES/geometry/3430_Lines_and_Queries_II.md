# [3430 — Lines and Queries II](https://cses.fi/problemset/task/3430)

**Platform:** CSES  
**Source:** CSES Problem Set — Geometry  
**Tags:** Li Chao, segment tree, range-active lines  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to efficiently process the following types of queries:

1. Add a line `a*x + b` that is active in range `[l, r]`
2. Find the maximum point in any active line at position `x`

### Input

The first line has an integer `n`: the number of queries.

The following `n` lines describe the queries. The format of each line is either `1 a b l r` or `2 x`.

### Output

Print the answer for each query of type `2`. If no line is active, print `NO`.

### Constraints

- `1 <= n <= 2e5`
- `-1e9 <= a, b <= 1e9`
- `0 <= x <= 1e5`
- `0 <= l <= r <= 1e5`

### Examples

**Input**
```
6
1 1 2 1 3
2 3
2 4
1 0 4 1 5
2 3
2 4
```

**Output**
```
5
NO
5
4
```

---

## What this problem really is

Same as permanent line inserts, but each line only exists on a closed interval `[l,r]` of x. Queries outside all active intervals print `NO`.

## What to use

| Tool | Why |
|------|-----|
| Segment tree over x | Canonical cover of `[l,r]` |
| Li Chao per node | Envelope of lines whose active range fully covers that node |
| Empty marker | Query with no covering line → `NO` |

## Approach

**Root idea:** a line active on `[l,r]` should contribute to every query `x` in that range. Decompose `[l,r]` into `O(log X)` segment-tree nodes and insert the line into each node’s Li Chao; a query at `x` merges envelopes along the root-to-leaf path.

**Mental picture:** each line is a sticker valid only between columns `l` and `r`. Segment-tree nodes are shelves; you place a copy of the sticker on every shelf that sits entirely inside `[l,r]`. Asking about column `x` looks at every shelf on the way down to that column.

**How that becomes the method**

1. Segment tree on `[0..1e5]`; each node owns a Li Chao (possibly dynamic / lazy-built).
2. Type `1 a b l r`: for each complete node in the cover of `[l,r]`, Li-Chao-insert `(a,b)`.
3. Type `2 x`: walk to `x`, evaluate all lines on the path, take the maximum. If no node on the path ever received a line that covers this query (equivalently: max stays at `-inf`), print `NO`.

Adds are permanent for their range (no delete queries), so you never remove from Li Chao.

**Data structures:** segment tree + Li Chao `(a,b)` per node; 64-bit evaluations.

## Walkthrough (on a sample)

- Add `x+2` on `[1,3]`. Query `3` → `5`. Query `4` → no active line → `NO`.  
- Add `0*x+4` on `[1,5]`. Query `3` → `max(5,4)=5`; query `4` → `4`.

## Pitfalls

- Printing a number instead of `NO` when uncovered.  
- Inserting into leaves only (`O(n * range)` blow-up) — must use canonical node cover.  
- Overflow.  
- Confusing with persistent/undo Li Chao — not needed here (no removals).

## Complexity

- Time: `O(n log^2 X)`  
- Space: `O(n log X)`

## Key takeaway

**Range-active lines + point max = segment tree of Li Chao trees**; uncovered → `NO`.
