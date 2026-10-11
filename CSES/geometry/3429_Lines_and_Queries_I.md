# [3429 — Lines and Queries I](https://cses.fi/problemset/task/3429)

**Platform:** CSES  
**Source:** CSES Problem Set — Geometry  
**Tags:** Li Chao, convex hull trick, online queries  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to efficiently process the following types of queries:

1. Add a line `a*x + b`
2. Find the maximum point in any line at position `x`

### Input

The first line has an integer `n`: the number of queries.

The following `n` lines describe the queries. The format of each line is either `1 a b` or `2 x`.

You may assume that the first query is of type `1`.

### Output

Print the answer for each query of type `2`.

### Constraints

- `1 <= n <= 2e5`
- `-1e9 <= a, b <= 1e9`
- `0 <= x <= 1e5`

### Examples

**Input**
```
6
1 1 2
2 1
2 3
1 0 4
2 1
2 3
```

**Output**
```
3
5
4
5
```

---

## What this problem really is

Online max of inserted lines: after any sequence of adds, answer `max_i (a_i * x + b_i)` at a given integer `x`. Lines stay forever once added.

## What to use

| Tool | Why |
|------|-----|
| Li Chao tree on `[0..1e5]` | Insert arbitrary `(a,b)`, query any `x`, fully online |
| 64-bit eval | `a*x + b` needs wide integers |
| CHT (optional) | Works if you batch/sort; Li Chao matches the online statement directly |

## Approach

**Root idea:** the answer is always the upper envelope of a growing set of infinite lines. You need a dynamic structure for “add line / query max at `x`”.

**Mental picture:** keep throwing new infinite rulers onto the plane; whenever someone asks “how high at this column?”, report the top ruler at that column.

**How that becomes the method**

1. Domain of `x` is `{0..1e5}` — build Li Chao over that discrete interval.
2. Type `1 a b`: insert line `y = a*x + b` into the Li Chao tree (at each node, keep the line that is better on the larger half of the node range; recurse the other half).
3. Type `2 x`: query the tree at `x` and print.

First query is an add, so you never query an empty set.

**Data structures:** Li Chao with node field `(a, b)`; static tree size `O(X)` or dynamic nodes `O(n log X)` with `X = 1e5+1`.

## Walkthrough (on a sample)

- Add `1*x+2`. Query `x=1` → `3`; `x=3` → `5`.  
- Add `0*x+4` (horizontal). At `x=1`: `max(3,4)=4`; at `x=3`: `max(5,4)=5`.

## Pitfalls

- Overflow in `a*x + b`.  
- Using floats.  
- Assuming slopes arrive sorted (they do not — plain CHT without Li Chao / multiset tricks fails online).

## Complexity

- Time: `O(n log X)` with `X = 1e5`  
- Space: `O(X)` or `O(n log X)`

## Key takeaway

**Online add-line / max-at-x = Li Chao tree** over the x-domain.
