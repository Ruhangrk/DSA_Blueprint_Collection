# [1648 — Dynamic Range Sum Queries](https://cses.fi/problemset/task/1648)

**Platform:** CSES  
**Source:** CSES Problem Set — Range Queries  
**Tags:** Fenwick tree, segment tree, point update  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an array of `n` integers, your task is to process `q` queries of the following types:

1. update the value at position `k` to `u`
2. what is the sum of values in range `[a,b]`?

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
1 3 1
2 1 4
```

**Output**
```
14
2
11
```

---

## What this problem really is

Array with **point updates** and **range sum** queries. Prefix sums alone fail after updates — need a dynamic structure.

## What to use

| Tool | Why |
|------|-----|
| Fenwick (BIT) or segment tree | `O(log n)` point update + prefix/range sum |
| `long long` | Large sums |
| Point set to `u` | Update by delta `u - old[k]` on Fenwick, or set leaf on segtree |

## Approach

**Root idea:** store the array in a tree that can add at an index and read prefix sums quickly. Range `[a,b]` = prefix(`b`) − prefix(`a-1`).

**Mental picture:** a scoreboard you can rewrite one cell of; totals of any segment still come from two prefix reads.

**Data structures**

- Fenwick `bit[1..n]` supporting `add(i,delta)`, `sumPrefix(i)`.
- Keep `x[k]` to compute deltas on type-`1` updates.

**Steps**

1. Build structure from initial `x` (`add(i, x[i])` for all `i`).
2. Type `1 k u`: `add(k, u - x[k])`; `x[k]=u`.
3. Type `2 a b`: print `sumPrefix(b) - sumPrefix(a-1)`.

## Walkthrough (on a sample)

Initial `[1..4]` sum `14`; `[5..6]` sum `2`; set position `3` to `1`; `[1..4]` becomes `11`.

## Pitfalls

- Forgetting to apply **delta** (adding `u` instead of `u-old`).
- 0-based vs 1-based Fenwick indexing.
- Using static prefix only — wrong after updates.

## Complexity

- Time: `O((n + q) log n)`  
- Space: `O(n)`

## Key takeaway

**Dynamic range sum = Fenwick/segment tree.** Point update + prefix difference for `[a,b]`.
