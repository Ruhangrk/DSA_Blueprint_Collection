# [1713 — Counting Divisors](https://cses.fi/problemset/task/1713)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** sieve, number of divisors  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given `n` integers, your task is to report for each integer the number of its divisors.

For example, if `x=18`, the correct answer is `6` because its divisors are `1,2,3,6,9,18`.

### Input

The first input line has an integer `n`: the number of integers.

After this, there are `n` lines, each containing an integer `x`.

### Output

For each integer, print the number of its divisors.

### Constraints

- `1 <= n <= 1e5`
- `1 <= x <= 1e6`

### Examples

**Input**
```
3
16
17
18
```

**Output**
```
5
2
6
```

---

## What this problem really is

For many values `x <= 1e6`, compute `d(x)` = number of positive divisors. Per-query `O(sqrt x)` can pass but a sieve precomputation is cleaner and faster.

## What to use

| Tool | Why |
|------|-----|
| Divisor sieve | For `i`, add `1` to `cnt[j]` for multiples `j = i, 2i, ...` |
| Or SPF factorization | `d(p1^a1 * ...) = (a1+1)(a2+1)...` |
| Cap `MAX = 1e6` | Fits memory |

## Approach

**Root idea:** every divisor `i` contributes `+1` to the divisor-count of each multiple of `i`. One harmonic loop fills all answers up to `MAX`.

**Mental picture:** for each possible divisor, stamp +1 on every multiple — like a sieve that counts instead of crossing out primes.

**Steps**

1. Allocate `cnt[0..MAX] = 0`.
2. For `i = 1..MAX`: for `j = i; j <= MAX; j += i`: `cnt[j]++`.
3. For each query `x`, print `cnt[x]`.

**Factorization view:** factor `x`, multiply `(e+1)` over prime exponents.

## Walkthrough (on a sample)

`16 = 2^4` → `5` divisors. `17` prime → `2`. `18 = 2 * 3^2` → `(1+1)(2+1)=6`.

## Pitfalls

- `O(n sqrt MAX)` if you factor each query slowly without care (usually still OK, but sieve is safer).
- Off-by-one at `x=1` (exactly one divisor).

## Complexity

- Time: `O(MAX log MAX + n)` for the sieve  
- Space: `O(MAX)`

## Key takeaway

**Many divisor-count queries on small range = sieve of multiples** — `cnt[j]++` for each divisor `i` of `j`.
