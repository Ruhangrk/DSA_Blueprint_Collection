# [1079 — Binomial Coefficients](https://cses.fi/problemset/task/1079)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** factorials, modular inverse, combinatorics  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to calculate `n` binomial coefficients modulo `1e9+7`.

A binomial coefficient `C(a, b)` can be calculated using the formula `a! / (b! * (a-b)!)`. We assume that `a` and `b` are integers and `0 <= b <= a`.

### Input

The first input line contains an integer `n`: the number of calculations.

After this, there are `n` lines, each of which contains two integers `a` and `b`.

### Output

Print each binomial coefficient modulo `1e9+7`.

### Constraints

- `1 <= n <= 1e5`
- `0 <= b <= a <= 1e6`

### Examples

**Input**
```
3
5 3
8 1
9 5
```

**Output**
```
10
8
126
```

---

## What this problem really is

Many queries of `C(a,b) mod MOD` with `a <= 1e6`. Precompute factorials and inverse factorials.

## What to use

| Tool | Why |
|------|-----|
| `fact[i]`, `invfact[i]` | `C(a,b) = fact[a] * invfact[b] * invfact[a-b]` |
| Modular inverse | `MOD` prime → Fermat or linear inverse sieve |
| Cap `MAX = 1e6` | Fits memory |

## Approach

**Root idea:** precompute all factorials mod `MOD`, then all inverse factorials (from `invfact[MAX] = modInverse(fact[MAX])`, then `invfact[i] = invfact[i+1] * (i+1)`).

**Mental picture:** binomial as “permute then cancel order inside the chosen set and the leftover.”

**Steps**

1. Build `fact[0..MAX]`, `invfact[0..MAX]`.
2. For each query: print `fact[a] * invfact[b] % MOD * invfact[a-b] % MOD`.

## Walkthrough (on a sample)

`C(5,3)=10`, `C(8,1)=8`, `C(9,5)=126`.

## Pitfalls

- Computing inverse factorial per query with `O(a)` — TLE at `1e5` queries.
- Forgetting `C(a,0)=1`.

## Complexity

- Time: `O(MAX + n)`  
- Space: `O(MAX)`

## Key takeaway

**Many `C(a,b) mod p` = precompute `fact` and `invfact`, then three multiplies per query.**
