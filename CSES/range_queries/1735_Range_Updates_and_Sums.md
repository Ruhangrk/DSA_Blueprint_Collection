# [1735 — Range Updates and Sums](https://cses.fi/problemset/task/1735)

**Platform:** CSES  
**Source:** CSES Problem Set — Range Queries  
**Tags:** lazy segment tree, range add, range assign, range sum  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to maintain an array of `n` values and efficiently process the following types of queries:

1. Increase each value in range `[a,b]` by `x`.
2. Set each value in range `[a,b]` to `x`.
3. Calculate the sum of values in range `[a,b]`.

### Input

The first input line has two integers `n` and `q`: the array size and the number of queries.

The next line has `n` values `t1, t2, ..., tn`: the initial contents of the array.

Finally, there are `q` lines describing the queries. The format of each line is one of the following: `"1 a b x"`, `"2 a b x"`, or `"3 a b"`.

### Output

Print the answer to each sum query.

### Constraints

- `1 <= n, q <= 2e5`
- `1 <= ti, x <= 1e6`
- `1 <= a <= b <= n`

### Examples

**Input**
```
6 5
2 3 1 1 5 3
3 3 5
1 2 4 2
3 3 5
2 2 4 5
3 3 5
```

**Output**
```
7
11
15
```

---

## What this problem really is

One structure must support **range add**, **range assign**, and **range sum**. Assign overrides prior adds on that segment; later adds stack on top of an assign.

## What to use

| Tool | Why |
|------|-----|
| Lazy segment tree with two tag kinds | Add and set don’t compose the same way |
| Tag priority | A new **set** clears old add/set on that node; **add** stacks on current value |
| `long long` | Sums up to roughly `n *` large values |

## Approach

**Root idea:** store in each node the current `sum`. Lazy tags are either “add `v` to everyone” or “set everyone to `v`”. Composition rules:

- Apply **set `x`** on a node covering `len`: `sum = x * len`; replace lazy with set `x` (drop any previous add/set).
- Apply **add `x`**: `sum += x * len`; if lazy was set `s`, it becomes set `s+x`; if lazy was add `a`, it becomes add `a+x`; else lazy = add `x`.

**Mental picture:** each pending tag is a sticky note on a segment. “Paint all cells `x`” throws away older notes on that segment. “Add `x`” revises whatever paint is already there.

**Data structures**

- Segtree: `sum[node]`, lazy type `{NONE, ADD, SET}` + value.
- Push before going to children; pull `sum = left.sum + right.sum` after child updates.

**Steps**

1. Build from `t`.
2. Type `1`: range apply ADD `x`.
3. Type `2`: range apply SET `x`.
4. Type `3`: range query sum (pushing on the path).

## Walkthrough (on a sample)

Array `2 3 1 1 5 3`. Sum `[3..5] = 1+1+5 = 7`.

Add `2` on `[2..4]` → `2 5 3 3 5 3`. Sum `[3..5] = 3+3+5 = 11`.

Set `[2..4]` to `5` → `2 5 5 5 5 3`. Sum `[3..5] = 5+5+5 = 15`.

When the set hits a node that still had a pending add, the set wins and the add is discarded on that node.

## Pitfalls

- Composing add after set incorrectly (must become set `s+x`, not keep a separate add that fights the set).
- Applying set without updating `sum` to `x * len`.
- Pushing in the wrong order when both children need the parent tag.
- Using only one lazy “add” field — cannot represent assign.

## Complexity

- Time: `O((n + q) log n)`  
- Space: `O(n)`

## Key takeaway

**Range add + range assign + range sum = lazy segtree with two tag types**, where **set overrides** and **add composes** onto the current tag.
