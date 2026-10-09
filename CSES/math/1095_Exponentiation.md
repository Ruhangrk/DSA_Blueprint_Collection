# [1095 — Exponentiation](https://cses.fi/problemset/task/1095)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** modular exponentiation, binary exponentiation  
**Difficulty / Rating:** Easy  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to efficiently calculate values `a^b` modulo `1e9+7`.

Note that in this task we assume that `0^0 = 1`.

### Input

The first input line contains an integer `n`: the number of calculations.

After this, there are `n` lines, each containing two integers `a` and `b`.

### Output

Print each value `a^b` modulo `1e9+7`.

### Constraints

- `1 <= n <= 2e5`
- `0 <= a, b <= 1e9`

### Examples

**Input**
```
3
3 4
2 8
123 123
```

**Output**
```
81
256
921450052
```

---

## What this problem really is

Many queries of modular power `a^b mod MOD` with huge exponents — need `O(log b)` per query, not a loop of `b` multiplies.

## What to use

| Tool | Why |
|------|-----|
| Binary exponentiation | Square-and-multiply using bits of `b` |
| `MOD = 1e9+7` | Prime modulus; use `% MOD` after multiplies |
| `long long` | Intermediate products before reducing |

## Approach

**Root idea:** write `b` in binary. `a^b = product of a^(2^j)` for each set bit. Compute successive squares of the base while scanning bits of `b`.

**Mental picture:** build the power by doubling the exponent (squaring) and occasionally multiplying into the answer when a bit is 1.

**Steps** (`modpow(a,b)`):

1. `res = 1`, `a %= MOD`.
2. While `b > 0`: if `b` odd, `res = res * a % MOD`; `a = a * a % MOD`; `b >>= 1`.
3. Return `res` (handles `0^0` as `res=1` when `b=0`).

## Walkthrough (on a sample)

`3^4`: binary `100` → square to `9`, square to `81` → **81**.

## Pitfalls

- Multiplying without casting to `long long` (overflow).
- Special-casing `0^0` incorrectly as `0`.
- Using floating `pow`.

## Complexity

- Time: `O(n log b)`  
- Space: `O(1)`

## Key takeaway

**`a^b mod M` = binary exponentiation** — square the base, multiply into the result on set bits of `b`.
