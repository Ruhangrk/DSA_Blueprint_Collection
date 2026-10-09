# [3396 — Next Prime](https://cses.fi/problemset/task/3396)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** primality test, Miller-Rabin  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given a positive integer `n`, find the next prime number after it.

### Input

The first line has an integer `t`: the number of tests.

After that, each line has a positive integer `n`.

### Output

For each test, print the next prime after `n`.

### Constraints

- `1 <= t <= 20`
- `1 <= n <= 1e12`

### Examples

**Input**
```
5
1
2
3
42
1337
```

**Output**
```
2
3
5
43
1361
```

---

## What this problem really is

Strictly next prime `p > n` for `n` up to `1e12`. Gaps are small enough to trial candidates, but each primality check must be fast (not trial-divide to `sqrt(1e12)` naively every time without care — Miller-Rabin is the robust choice).

## What to use

| Tool | Why |
|------|-----|
| Deterministic Miller-Rabin for 64-bit | Fast probable-prime test that is exact on `[1, 2^64)` with fixed bases |
| Scan `n+1, n+2, ...` | Next prime is not far |
| Skip evens | After handling `2` |

## Approach

**Root idea:** start at `n+1` (or `2` if `n < 2`) and test successive odd numbers until Miller-Rabin says prime.

**Mental picture:** walk forward on the number line checking “prime?” with a fast fingerprint test.

**Miller-Rabin sketch:** write `x-1 = 2^s * d`. For each base `a` in a proven set for 64-bit (e.g. `2,3,5,7,11,13,23` or the known 7-base set), check the strong probable prime conditions via modular squaring.

**Steps**

1. For each `n`: `x = n+1`; if `x < 2` set `x = 2`.
2. While `!is_prime(x)`: `x++` (or `+=2` after `2`).
3. Print `x`.

## Walkthrough (on a sample)

`n=42` → try 43 → prime → **43**. `n=1337` → **1361**.

## Pitfalls

- Returning `n` if `n` is already prime — need **strictly greater**.
- Fermat test alone — pseudoprimes exist; use Miller-Rabin.
- Overflow in `a*a % x` during witnesses — use mulmod.

## Complexity

- Time: per test `O(gap * log^2 n)` with MR; gaps near `1e12` are small in practice  
- Space: `O(1)`

## Key takeaway

**Next prime after huge `n` = increment + deterministic Miller-Rabin** (strictly `> n`).
