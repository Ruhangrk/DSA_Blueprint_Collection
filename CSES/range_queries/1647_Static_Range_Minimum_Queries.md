# [1647 — Static Range Minimum Queries](https://cses.fi/problemset/task/1647)

**Platform:** CSES  
**Source:** CSES Problem Set — Range Queries  
**Tags:** sparse table, RMQ  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an array of `n` integers, your task is to process `q` queries of the form: what is the minimum value in range `[a,b]`?

### Input

The first input line has two integers `n` and `q`: the number of values and queries.

The second line has `n` integers `x1, x2, ..., xn`: the array values.

Finally, there are `q` lines describing the queries. Each line has two integers `a` and `b`: what is the minimum value in range `[a,b]`?

### Output

Print the result of each query.

### Constraints

- `1 <= n, q <= 2e5`
- `1 <= xi <= 1e9`
- `1 <= a <= b <= n`

### Examples

**Input**
```
8 4
3 2 4 5 1 1 5 3
2 4
5 6
1 8
3 3
```

**Output**
```
2
1
1
4
```

---

## What this problem really is

Static array; many range-minimum queries. Need fast RMQ (`n,q` up to `2e5`) — no updates.

## What to use

| Tool | Why |
|------|-----|
| Sparse table `st[k][i]` | `st[k][i]` = min on `[i, i+2^k - 1]` |
| Idempotent `min` | Overlapping blocks OK — no need for disjoint segments |
| `O(1)` query | Two blocks of length `2^k` covering `[a,b]` |

## Approach

**Root idea:** precompute mins on all dyadic intervals. Any `[a,b]` is the min of two overlapping power-of-two windows.

**Mental picture:** a ladder of “min over 1, 2, 4, 8, …” length blocks; pick the right rung for the query length.

**Data structures**

- `st[0][i] = x[i]`
- `st[k][i] = min(st[k-1][i], st[k-1][i+2^(k-1)])`
- `lg[len]` = floor(log2(len))

**Query `[a,b]` (1-based):**

```
k = lg[b-a+1]
ans = min(st[k][a], st[k][b - (1<<k) + 1])
```

**Steps**

1. Build sparse table `O(n log n)`.
2. Answer each query in `O(1)`.

## Walkthrough (on a sample)

`[2,4]` on `2,4,5` → min **`2`**; `[5,6]` → **`1`**; full array → **`1`**; `[3,3]` → **`4`**.

## Pitfalls

- Using a Fenwick for min incorrectly (prefix mins don’t subtract).
- Off-by-one in the second block index.
- Segment tree also works but sparse table is ideal when static.

## Complexity

- Time: `O(n log n + q)`  
- Space: `O(n log n)`

## Key takeaway

**Static RMQ = sparse table.** Precompute dyadic mins; answer with `min` of two overlapping `2^k` blocks.
