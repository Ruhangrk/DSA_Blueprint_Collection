# [3355 — Sum of Four Squares](https://cses.fi/problemset/task/3355)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** number theory, Lagrange, Legendre three-square  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A well known result in number theory is that every non-negative integer can be represented as the sum of four squares of non-negative integers.

You are given a non-negative integer `n`. Your task is to find four non-negative integers `a`, `b`, `c` and `d` such that `n = a*a + b*b + c*c + d*d`.

### Input

The first line has an integer `t`: the number of test cases.

Each of the next `t` lines has an integer `n`.

### Output

For each test case, print four non-negative integers `a`, `b`, `c` and `d` that satisfy `n = a*a + b*b + c*c + d*d`.

### Constraints

- `1 <= t <= 1000`
- `0 <= n <= 1e7`
- the sum of all `n` is at most `1e7`

### Examples

**Input**
```
3
5
30
322266
```

**Output**
```
2 1 0 0
1 2 3 4
314 159 265 358
```

---

## What this problem really is

Constructively find a Lagrange four-square representation of `n` (existence is guaranteed). Constraints allow a careful search, not a heavy randomized number-theory pipeline.

## What to use

| Tool | Why |
|------|-----|
| Lagrange’s four-square theorem | Every `n >= 0` works with four squares |
| Legendre’s three-square theorem | `n` needs four (cannot do with three) iff `n = 4^k * (8*m + 7)` |
| Nested loops + perfect-square check | Build a representation within the sum-`n` budget |
| Factor out `4^k` | Shrink search; scale answers by `2^k` at the end |

## Approach

**Root idea:** most numbers are sums of three squares; only the `4^k*(8m+7)` family forces a genuine fourth square. Search small `a,b` (and maybe `c`) and test whether the remainder is a square (or a sum of two squares).

**Mental picture:** peel perfect squares off `n` until nothing is left — but greedy largest-square fails (e.g. `23`). Use theorems to know when three suffice, then enumerate.

**Practical method (fits `sum n <= 1e7`)**

1. Handle `n = 0` → `0 0 0 0`.
2. Optional speedup: while `n % 4 == 0`, `n /= 4`, remember `k` (later multiply each root by `2^k`).
3. Enumerate `a = 0 .. floor(sqrt(n))`, then `b = 0 .. floor(sqrt(n-a*a))`:
   - `rem = n - a*a - b*b`
   - Enumerate `c = 0 .. floor(sqrt(rem))`; if `rem - c*c` is a perfect square `d*d`, output `a b c d` (and scale if you divided out `4^k`).
4. Break as soon as one tuple is found.

Because of Lagrange, a solution always appears. Using three-square knowledge: if after stripping `4^k` the number is not `8m+7`, you can force `d = 0` and only search three; if it is `8m+7`, try `a = 1` (or small) so the remainder is three-square-representable.

## Walkthrough (on a sample)

`5 = 2*2 + 1*1 + 0 + 0`.  
`30 = 1 + 4 + 9 + 16`.  
`322266` has a larger four-tuple as in the sample output.

## Pitfalls

- Greedy “largest square repeatedly” — wrong for some `n` (classic: `23`).
- 32-bit overflow on `a*a` when `a` near `sqrt(1e7)` is fine; still use 64-bit habit.
- Forgetting `n = 0`.
- Allowed zeros and any order — any valid tuple is OK.

## Complexity

- Time: roughly `O(n)` worst per test with naive triple loops; with early exit + `4^k` reduction and sum-of-`n` bound, fits TL  
- Space: `O(1)` per test (or `O(MAX)` if you precompute `is_square`)

## Key takeaway

**Every `n` is four squares (Lagrange); construct by search** — use three-square / `4^k(8m+7)` structure to shrink the hunt.
