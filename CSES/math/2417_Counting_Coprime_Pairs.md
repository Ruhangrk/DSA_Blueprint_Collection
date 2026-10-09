# [2417 — Counting Coprime Pairs](https://cses.fi/problemset/task/2417)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** Möbius function, sieve, counting  
**Difficulty / Rating:** Medium–Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given a list of `n` positive integers, your task is to count the number of pairs of integers that are coprime (i.e., their greatest common divisor is one).

### Input

The first input line has an integer `n`: the number of elements.

The next line has `n` integers `x1, x2, ..., xn`: the contents of the list.

### Output

Print one integer: the answer for the task.

### Constraints

- `1 <= n <= 1e5`
- `1 <= xi <= 1e6`

### Examples

**Input**
```
8
5 4 20 1 16 17 5 15
```

**Output**
```
19
```

---

## What this problem really is

Count unordered (or ordered? — typically unordered pairs `i < j` with `gcd(xi,xj)=1`) pairs. `n=1e5` forbids pairwise gcd. Values ≤ `1e6`.

## What to use

| Tool | Why |
|------|-----|
| Möbius inversion | `sum_{d\|gcd} μ(d) = [gcd=1]` |
| Frequency of multiples | How many `xi` divisible by `d` |
| Linear sieve for `μ` | Precompute Möbius up to `1e6` |

## Approach

**Root idea:**

```
number of ordered pairs with gcd=1
  = sum_{i,j} sum_{d|gcd(xi,xj)} μ(d)
  = sum_d μ(d) * (count of xi divisible by d)^2
```

For unordered pairs `i < j`, use `C(cnt[d], 2)` instead of `cnt^2`, or compute ordered and adjust — usually:

```
ans = sum_d μ(d) * C(f[d], 2)
```

where `f[d]` = number of array elements divisible by `d`.

(If the problem counts unordered pairs including checking sample: 19 pairs from 8 elements → `C(8,2)=28` total unordered, 19 coprime — use `C(f[d],2)`.)

**Mental picture:** IE over shared divisors — μ marks square-free common divisors.

**Steps**

1. Count frequency of each value; build `f[d] = sum of freq[m]` for multiples `m` of `d`.
2. Sieve Möbius `μ[1..MAX]`.
3. `ans = sum_d μ[d] * C(f[d], 2)` (`long long`).
4. Print `ans`.

## Walkthrough (on a sample)

Pairs involving `1` are always coprime; primes coprime to numbers not sharing factors, etc. Total **19**.

## Pitfalls

- Using `f[d]^2` when the problem wants unordered pairs (off by diagonals / factor 2).
- Forgetting μ is 0 on non-square-free numbers.
- `int` overflow on `C(f,2)`.

## Complexity

- Time: `O(MAX log MAX + n)`  
- Space: `O(MAX)`

## Key takeaway

**Coprime pairs = `sum_d μ(d) * C(count of multiples of d, 2)`.**
