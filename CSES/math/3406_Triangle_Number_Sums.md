# [3406 — Triangle Number Sums](https://cses.fi/problemset/task/3406)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** triangular numbers, Gauss Eureka, number theory  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A triangle number is a positive integer of the form `1+2+...+k`. The first triangle numbers are `1`, `3`, `6`, `10` and `15`.

Every positive integer can be represented as a sum of triangle numbers. For example, `42 = 21+21` and `1337 = 1326+10+1`.

Given a positive integer `n`, determine the smallest number of triangle numbers that sum to `n`.

### Input

The first line has an integer `t`: the number of tests.

After that, each line has a positive integer `n`.

### Output

For each test, print the smallest number of triangle numbers.

### Constraints

- `1 <= t <= 100`
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
1
2
1
2
3
```

---

## What this problem really is

Write `n` as a sum of the fewest **positive** triangular numbers `T_k = k*(k+1)/2` (repeats allowed). The answer is always in `{1, 2, 3}`.

## What to use

| Tool | Why |
|------|-----|
| Gauss / Fermat polygonal (triangular case) | Every positive integer is a sum of **at most three** triangular numbers |
| Triangular test: `8*n+1` perfect square | Decide answer `1` |
| Scan / set of `T_k <= n` | Decide whether answer `2` |
| Else answer `3` | Forced by the theorem |

## Approach

**Root idea:** you never need more than three summands. So check “is one?”, then “is two?”, else three.

**Mental picture:** triangular numbers sit on the diagonal of the addition table of `{1,2,3,...}`. Packing `n` with as few of those piles as possible — Gauss proved three always enough.

**How that becomes the method**

1. **One:** `n` is triangular iff `8*n + 1` is a perfect square (and the root is odd). If yes → print `1`.
2. **Two:** for each triangular `T = k*(k+1)/2 <= n` (`k` up to about `sqrt(2*n)`), check whether `n - T` is triangular (same square test). If any hit → print `2`.  
   With `n <= 1e12`, about `~1.4e6` values of `k` — fine for `t <= 100`.
3. Otherwise print `3`.

**Quick filters (optional):** triangular numbers mod 9 are only in `{0,1,3,6}`. If `n % 9` is `2` or `5`… actually numbers needing three include all `n ≡ 5 or 8 (mod 9)` (cannot be one or two). Useful for early `3`, but the scan above already covers correctness.

## Walkthrough (on a sample)

| n | reason | ans |
|---|--------|-----|
| 1 | `T_1` | 1 |
| 2 | `1+1` | 2 |
| 3 | `T_2` | 1 |
| 42 | `21+21` | 2 |
| 1337 | needs three (`1326+10+1`) | 3 |

## Pitfalls

- Forbidding repeats — `2 = 1+1` must be allowed.
- Using `0` as a triangular summand — statement uses positive triangle numbers; for the count problem, `0` would only fake shorter sums incorrectly if misused.
- `O(n)` per test — TLE at `1e12`; stay `O(sqrt(n))`.
- Integer overflow: compute `k*(k+1)/2` in 64-bit.

## Complexity

- Time: `O(t * sqrt(n))`  
- Space: `O(1)`

## Key takeaway

**Fewest triangular summands ∈ {1,2,3}** — test triangular, then two-triangular, else three (Gauss).
