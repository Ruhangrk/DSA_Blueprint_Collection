# [2206 — Pizzeria Queries](https://cses.fi/problemset/task/2206)

**Platform:** CSES  
**Source:** CSES Problem Set — Range Queries  
**Tags:** segment tree, transformation  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` buildings on a street, numbered `1, 2, ..., n`. Each building has a pizzeria and an apartment.

The pizza price in building `k` is `pk`. If you order a pizza from building `a` to building `b`, its price (with delivery) is `pa + |a - b|`.

Your task is to process two types of queries:

1. The pizza price `pk` in building `k` becomes `x`.
2. You are in building `k` and want to order a pizza. What is the minimum price?

### Input

The first input line has two integers `n` and `q`: the number of buildings and queries.

The second line has `n` integers `p1, p2, ..., pn`: the initial pizza price in each building.

Finally, there are `q` lines that describe the queries. Each line is either `"1 k x"` or `"2 k"`.

### Output

Print the answer for each query of type `2`.

### Constraints

- `1 <= n, q <= 2e5`
- `1 <= pi, x <= 1e9`
- `1 <= k <= n`

### Examples

**Input**
```
6 3
8 6 4 5 7 5
2 2
1 5 1
2 2
```

**Output**
```
5
4
```

---

## What this problem really is

Minimize `p[a] + |a - k|` over all buildings `a`, with point updates to `p`. Split absolute value into left/right cases.

## What to use

| Tool | Why |
|------|-----|
| Two segment trees (or one with pairs) | Left: min of `p[i]-i`; right: min of `p[i]+i` |
| Point updates | Change `p[k]` updates both trees at index `k` |
| Query split at `k` | Best from left of `k` + best from right of `k` |

## Approach

**Root idea:**

```
p[a] + |a-k| = { p[a] - a + k    if a <= k
               { p[a] + a - k    if a >= k
```

So for fixed `k`:

```
ans = min(
  k + min_{a<=k}(p[a]-a),
  -k + min_{a>=k}(p[a]+a)
)
```

**Mental picture:** rewrite delivery so each pizzeria has a static score `p±index`; you only add/subtract your position `k`.

**Data structures**

- Segtree `L`: stores `p[i]-i`, query range min on `[1..k]`
- Segtree `R`: stores `p[i]+i`, query range min on `[k..n]`

**Steps**

1. Build both trees from initial `p`.
2. Type `1 k x`: set `L[k]=x-k`, `R[k]=x+k`.
3. Type `2 k`: print `min(k + L.query(1,k), -k + R.query(k,n))`.

## Walkthrough (on a sample)

At building `2`, best pizza costs **`5`**. After building `5`’s price becomes `1`, ordering at `2` drops to **`4`**.

## Pitfalls

- One tree isn’t enough — need both sides of `k`.
- Overflow: use `long long` (`p+i` up to ~`2e9` initially, fine in 64-bit with headroom).
- Off-by-one when `k` is at an endpoint (one side empty → ignore that side).

## Complexity

- Time: `O((n + q) log n)`  
- Space: `O(n)`

## Key takeaway

**`min (p[a]+|a-k|)` = two range mins** on transformed arrays `p[i]-i` (left) and `p[i]+i` (right).
