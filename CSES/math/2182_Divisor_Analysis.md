# [2182 — Divisor Analysis](https://cses.fi/problemset/task/2182)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** prime factorization, modular inverse, divisor functions  
**Difficulty / Rating:** Medium–Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an integer, your task is to find the number, sum and product of its divisors. As an example, let us consider the number `12`:

- the number of divisors is `6` (they are `1, 2, 3, 4, 6, 12`)
- the sum of divisors is `1+2+3+4+6+12=28`
- the product of divisors is `1 * 2 * 3 * 4 * 6 * 12 = 1728`

Since the input number may be large, it is given as a prime factorization.

### Input

The first line has an integer `n`: the number of parts in the prime factorization.

After this, there are `n` lines that describe the factorization. Each line has two numbers `x` and `k` where `x` is a prime and `k` is its power.

### Output

Print three integers modulo `1e9+7`: the number, sum and product of the divisors.

### Constraints

- `1 <= n <= 1e5`
- `2 <= x <= 1e6`
- each `x` is a distinct prime
- `1 <= k <= 1e9`

### Examples

**Input**
```
2
2 2
3 1
```

**Output**
```
6 28 1728
```

---

## What this problem really is

Number `N = product p_i ^ k_i`. Compute `tau(N)`, `sigma(N)`, and product of all divisors — all mod `1e9+7`, with huge exponents.

## What to use

| Tool | Why |
|------|-----|
| Multiplicative formulas | Closed forms from prime powers |
| Modpow + mod inverse | Geometric sums; half exponents for products |
| `MOD = 1e9+7` prime | Inverses exist for needed denominators |

## Approach

**Root idea:** for `N = product p^k`:

1. **Number of divisors:** `tau = product (k_i + 1)` (mod `MOD`).

2. **Sum of divisors:** `sigma` is multiplicative:
   ```
   sigma(p^k) = 1 + p + ... + p^k = (p^(k+1) - 1) * inv(p-1)  (mod MOD)
   ```
   Total `sigma(N) = product sigma(p_i^{k_i})`.

3. **Product of divisors:** for any `N`, product of all divisors is `N^(tau(N)/2)`.
   - If `tau` even: `pow(N, tau/2, MOD)`.
   - If `tau` odd (`N` perfect square): `pow(N, (tau-1)/2, MOD) * sqrt(N)` — still equals `N^(tau/2)` in integers; carefully compute exponent of each prime in the product modulo `MOD-1` (for Euler on the outer pow), or use:
     ```
     product = N^(tau/2) = product over primes p_i^( k_i * tau / 2 )
     ```
     Exponent `k_i * tau / 2` must be handled in integers: since `tau = product (k_j+1)`, when `tau` even you can divide the even factor by 2 before multiplying by `k_i`, then `modpow(p_i, that_exp, MOD)` and multiply.

**Mental picture:** divisors form a grid from the exponents; count cells, sum geometric series per prime, and product is “each divisor pairs with `N/d`” → `N^(tau/2)`.

**Steps**

1. Read all `(p,k)`; compute `tau`, `sigma`, `prod` with the formulas above using modpow.
2. Print three numbers mod `MOD`.

## Walkthrough (on a sample)

`N = 2^2 * 3^1 = 12`.

- `tau = 3*2 = 6`
- `sigma = (1+2+4)*(1+3) = 7*4 = 28`
- product = `12^(6/2) = 12^3 = 1728`

## Pitfalls

- Computing `tau/2` in modular arithmetic incorrectly (need exact integer half).
- Using `sigma = (p^{k+1}-1)/(p-1)` without modular inverse.
- Exponents `k` up to `1e9` — must use modpow, not loops.

## Complexity

- Time: `O(n log MOD + n log k)`  
- Space: `O(n)`

## Key takeaway

**From prime factorization: `tau = π(k+1)`, `sigma = π (p^{k+1}-1)/(p-1)`, product = `N^(tau/2)`** — all modular.
