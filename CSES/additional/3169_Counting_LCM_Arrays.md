# [3169 — Counting LCM Arrays](https://cses.fi/problemset/task/3169)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems I  
**Tags:** Number theory, divisors, prime factorization, multiplicative functions, matrix exponentiation  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given two integers `n` and `k`, count the number of arrays `a[1], a[2], ..., a[n]` of positive integers such that `lcm(a[i], a[i+1]) = k` for every `1 <= i < n`.

### Input

- The first line has an integer `t`: the number of test cases.
- The next `t` lines each have two integers `n` and `k`.

### Output

Print `t` integers: the answer to each test case modulo `1e9 + 7`.

### Constraints

- `1 <= t <= 1000`
- `2 <= n <= 1e9`
- `1 <= k <= 1e9`

### Examples

**Input**
```
3
3 4
4 6
1337 42
```

**Output**
```
11
64
602746233
```

**Explanation:** For `n = 3, k = 4` the valid arrays are `[1,4,1], [1,4,2], [1,4,4], [2,4,1], [2,4,2], [2,4,4], [4,1,4], [4,2,4], [4,4,1], [4,4,2], [4,4,4]`.

---

## What this problem really is

Count walks of length `n - 1` on the divisors of `k`, where two adjacent divisors are linked exactly when their lcm is `k`. The graph is large, but it has a product structure over the primes of `k`, which makes the count easy.

## What to use

| Tool | Why |
|------|-----|
| Trial-division factorization of `k` | `k <= 1e9`, so `sqrt(k) ~ 3.2e4` and `t <= 1000` is fast enough |
| Exponent view per prime | `lcm = k` is decided prime by prime through `max(exponents) = e` |
| Multiplicativity of walk counts | Independent primes multiply, so each prime is counted on its own |
| Small matrix power, `(e+1) x (e+1)` | `e <= 29` for `k <= 1e9`, and `log n <= 30` steps |

## Approach

**Root idea:** a divisor `d` of `k` is determined by its exponent vector `(x_p)` over primes `p | k`, with `0 <= x_p <= e_p`. The condition `lcm(d1, d2) = k` says that for each prime `p`, `max(x_p(d1), x_p(d2)) = e_p`. This is a condition on each coordinate separately. So the whole transition structure is a tensor product, and walk counts factor over primes.

**Mental picture:** imagine `k` as several independent dials, one per prime. Each dial has its own small state machine: exponent `0..e`, with allowed moves where at least one side hits `e`. A full array is a walk through all dials at once. Since the dials do not interact, the total number of walks is the product of the individual dial counts.

**How that becomes the method**

1. Factor `k = p1^e1 * p2^e2 * ...` by trial division.
2. For each prime with exponent `e`, build the `(e+1) x (e+1)` matrix `M[x][y] = 1` if `max(x, y) = e`, else 0.
3. For that prime, count the walks of length `n - 1` starting anywhere: `c_p = sum of all entries of M^(n-1)`. Equivalently, start with the all-ones vector `v`, apply `M` exactly `n - 1` times, and sum the entries of the result.
4. Answer = product of `c_p` over all primes, modulo `1e9 + 7`. If `k = 1`, there are no primes, the product is 1, and the only array is all ones.

**Data structures:**
- Prime list of `k` with exponents.
- For each prime: a small matrix of size `e+1` used in fast exponentiation (or direct vector iteration when `e` is small).

## Walkthrough (on a sample)

**Test `n = 3, k = 4`.** `4 = 2^2`, so one prime with `e = 2`. States `{0,1,2}`. Allowed moves `x -> y` where `max(x,y) = 2`:

- From `0`: only to `2`.
- From `1`: only to `2`.
- From `2`: to `0`, `1`, or `2`.

Start with the all-ones vector over states. Apply `M` once (one step, giving counts of length-2 walks ending at each state, read from the out-degrees):

- Out-degrees (number of ways to continue one more step from each state): `deg(0) = 1`, `deg(1) = 1`, `deg(2) = 3`.
- Walks of length 2 (3 array elements) starting at each state: from `0`: `deg(2) = 3`. From `1`: `deg(2) = 3`. From `2`: `deg(0) + deg(1) + deg(2) = 1 + 1 + 3 = 5`.
- Total = `3 + 3 + 5 = 11`. Matches the expected output.

**Test `n = 4, k = 6`.** `6 = 2^1 * 3^1`, two primes, each with `e = 1`. States `{0,1}`, allowed moves: `(0,1), (1,0), (1,1)`, so `M = [[0,1],[1,1]]`. Count walks of length 3 (`n - 1 = 3`):

- Start `v = (1,1)`.
- `M v = (1, 2)`.
- `M (1,2) = (2, 3)`.
- `M (2,3) = (3, 5)`.
- Sum = `8`.

Two independent primes, so `8 * 8 = 64`. Matches the expected output.

## Pitfalls

- Treating the answer as a walk on all divisors without the product split. That state space can have up to 1344 divisors and is wasteful.
- Forgetting `k = 1`: the product over zero primes is 1, which is correct, since every element must be 1.
- Counting `n - 1` steps instead of `n - 1` transitions. An array of length `n` has `n - 1` adjacent pairs, which is `n - 1` matrix applications from the starting vector.
- Overflow in modular arithmetic: multiply in 64-bit and reduce modulo `1e9 + 7` after each product.
- Using `e` for the matrix size instead of `e + 1` states (exponents `0..e`).
- Trial division up to `sqrt(k)` only; do not loop to `k`.

## Complexity

- Time: `O(sqrt(k))` for factorization plus `O(omega(k) * e^3 * log n)` for the matrix powers, where `omega(k) <= 9` for `k <= 1e9` and `e <= 29`. Per test case this is well inside the limit with `t <= 1000`.
- Space: `O(e^2)` per prime.

## Key takeaway

When a condition like `lcm = k` splits prime by prime, the counting problem factors into independent per-prime walk counts that multiply. Matrix power on the small exponent chain then handles huge `n`.
