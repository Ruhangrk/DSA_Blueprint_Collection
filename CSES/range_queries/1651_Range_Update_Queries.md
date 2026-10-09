# [1651 — Range Update Queries](https://cses.fi/problemset/task/1651)

**Platform:** CSES  
**Source:** CSES Problem Set — Range Queries  
**Tags:** Fenwick / difference array / lazy segtree  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an array of `n` integers, your task is to process `q` queries of the following types:

1. increase each value in range `[a,b]` by `u`
2. what is the value at position `k`?

### Input

The first input line has two integers `n` and `q`: the number of values and queries.

The second line has `n` integers `x1, x2, ..., xn`: the array values.

Finally, there are `q` lines describing the queries. The format of each line is either `"1 a b u"` or `"2 k"`.

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
8 3
3 2 4 5 1 1 5 3
2 4
1 2 5 1
2 4
```

**Output**
```
5
6
```

---

## What this problem really is

**Range add** updates + **point** queries. Dual of the usual point-update/range-sum setup.

## What to use

| Tool | Why |
|------|-----|
| Difference array + Fenwick | Range add ↔ two point updates on a diff structure |
| Or lazy segment tree | Range add + point query |
| `long long` | Values grow with many adds |

## Approach

**Root idea:** maintain an array `d` where the real value at `k` is `x[k] + prefix_sum(d[1..k])` (extra added amounts). Adding `u` on `[a,b]` does `d[a]+=u`, `d[b+1]-=u`.

**Mental picture:** paint `+u` starting at `a` and cancel it after `b`.

**Fenwick version**

- BIT stores the difference array.
- Type `1 a b u`: `bit.add(a,u)`; if `b+1<=n` then `bit.add(b+1,-u)`.
- Type `2 k`: print `x[k] + bit.sumPrefix(k)`.

**Steps**

1. Keep original `x`; BIT starts at 0 (or build from zeros).
2. Process range adds as two point updates on BIT.
3. Point query = original + prefix of diffs.

## Walkthrough (on a sample)

Position `4` starts as `5`. Add `1` on `[2,5]` → position `4` becomes **`6`**.

## Pitfalls

- Updating every index in `[a,b]` naively — TLE.
- Forgetting the `-u` at `b+1`.
- Not adding back base `x[k]`.

## Complexity

- Time: `O((n + q) log n)`  
- Space: `O(n)`

## Key takeaway

**Range add + point read = difference array (often in a Fenwick).** `+u` at `a`, `-u` at `b+1`; query prefix of diffs plus original value.
