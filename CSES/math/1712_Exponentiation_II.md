# [1712 — Exponentiation II](https://cses.fi/problemset/task/1712)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** modular exponentiation, Euler's theorem, phi  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to efficiently calculate values `a^(b^c)` modulo `1e9+7`.

Note that in this task we assume that `0^0 = 1`.

### Input

The first input line has an integer `n`: the number of calculations.

After this, there are `n` lines, each containing three integers `a`, `b` and `c`.

### Output

Print each value `a^(b^c)` modulo `1e9+7`.

### Constraints

- `1 <= n <= 1e5`
- `0 <= a, b, c <= 1e9`

### Examples

**Input**
```
3
3 7 1
15 2 2
3 4 5
```

**Output**
```
2187
50625
763327764
```

---

## What this problem really is

Compute `a^(b^c) mod MOD` with `MOD = 1e9+7`. You cannot expand `b^c` as a normal integer when it is huge — reduce the exponent modulo `phi(MOD)`.

## What to use

| Tool | Why |
|------|-----|
| Euler / Carmichael | For prime `MOD`, `a^k ≡ a^(k mod (MOD-1)) (mod MOD)` when `gcd(a,MOD)=1` |
| Nested modpow | `exp = modpow(b, c, MOD-1)`, then `modpow(a, exp, MOD)` |
| Edge cases | `a ≡ 0`, `b=0`, `c=0` / `0^0=1` |

## Approach

**Root idea:** `MOD` is prime, so `phi(MOD) = MOD-1`. By Euler’s theorem, exponents of `a` modulo `MOD` can be taken modulo `MOD-1` (with care when `a` is divisible by `MOD` — then for positive exponents the power is `0`).

Compute:

```
e = b^c  mod  (MOD-1)    // via modpow(b, c, MOD-1)
answer = a^e  mod  MOD   // via modpow(a, e, MOD)
```

**Caveat:** Euler’s reduction `a^k = a^(k mod phi)` assumes `gcd(a, MOD)=1`. When `a % MOD == 0`:
- if the exponent is `0` → `1` (by task’s `0^0` and `a^0`)
- if exponent `> 0` → `0`

Also `b^c mod (MOD-1)`: when using Euler for the outer pow, if `gcd(a,MOD)=1` it’s fine to reduce; standard CSES approach still computes `modpow(b,c,MOD-1)` then `modpow(a, that, MOD)` and handles zeros separately.

**Mental picture:** the exponent carousel only has `MOD-1` distinct ticks for units modulo a prime; compress `b^c` onto that carousel before powering `a`.

**Steps**

1. Special-case zeros / `0^0`.
2. `e = modpow(b, c, MOD-1)`.
3. Print `modpow(a, e, MOD)`.

## Walkthrough (on a sample)

`3^(7^1) = 3^7 = 2187`.

`15^(2^2) = 15^4 = 50625`.

`3^(4^5)` needs modular reduction of the huge exponent modulo `1e9+6`.

## Pitfalls

- Computing `b^c` without mod — overflow / impossible.
- Reducing exponent mod `MOD` instead of `MOD-1`.
- Forgetting `a^0 = 1` even when `a=0`.

## Complexity

- Time: `O(n log (max exponent))`  
- Space: `O(1)`

## Key takeaway

**`a^(b^c) mod p` (p prime) = `a^( (b^c mod (p-1)) ) mod p`**, with zero-edge cases handled explicitly.
