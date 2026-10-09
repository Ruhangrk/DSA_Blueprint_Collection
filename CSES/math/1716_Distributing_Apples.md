# [1716 — Distributing Apples](https://cses.fi/problemset/task/1716)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** stars and bars, binomial coefficients  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` children and `m` apples that will be distributed to them. Your task is to count the number of ways this can be done.

For example, if `n=3` and `m=2`, there are 6 ways: `[0,0,2]`, `[0,1,1]`, `[0,2,0]`, `[1,0,1]`, `[1,1,0]` and `[2,0,0]`.

### Input

The only input line has two integers `n` and `m`.

### Output

Print the number of ways modulo `1e9+7`.

### Constraints

- `1 <= n, m <= 1e6`

### Examples

**Input**
```
3 2
```

**Output**
```
6
```

---

## What this problem really is

Number of nonnegative integer solutions to `x1 + ... + xn = m` (children distinguishable, apples identical). Classic stars and bars: `C(m + n - 1, n - 1)`.

## What to use

| Tool | Why |
|------|-----|
| Stars and bars | Identical apples, distinct children, zeros allowed |
| Binomial mod prime | Precompute fact/invfact up to `m+n` |
| `MOD = 1e9+7` | Required |

## Approach

**Root idea:** place `m` identical apples and `n-1` dividers in a line — `C(m+n-1, n-1)` arrangements.

**Mental picture:** apples in a row; dividers split them among children; empty segments = zero apples.

**Steps**

1. Need `C(m+n-1, n-1)` or equivalently `C(m+n-1, m)`.
2. Precompute factorials up to `m+n-1` (≤ `2e6`).
3. Print `fact[m+n-1] * invfact[m] * invfact[n-1] % MOD`.

## Walkthrough (on a sample)

`n=3,m=2` → `C(4,2)=6` — matches the listed tuples.

## Pitfalls

- Using `C(m, n)` (wrong model — that would be distinct apples / at most one each).
- Forbidding zero (that would be `C(m-1, n-1)` for positive only).
- `n=1` → only one way.

## Complexity

- Time: `O(m+n)`  
- Space: `O(m+n)`

## Key takeaway

**Distribute `m` identical apples to `n` distinct kids (zeros OK) = `C(m+n-1, n-1)`.**
