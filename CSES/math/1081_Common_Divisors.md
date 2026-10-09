# [1081 — Common Divisors](https://cses.fi/problemset/task/1081)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** GCD, frequency, sieve-style  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array of `n` positive integers. Your task is to find two integers such that their greatest common divisor is as large as possible.

### Input

The first input line has an integer `n`: the size of the array.

The second line has `n` integers `x1, x2, ..., xn`: the contents of the array.

### Output

Print the maximum greatest common divisor.

### Constraints

- `2 <= n <= 2e5`
- `1 <= xi <= 1e6`

### Examples

**Input**
```
5
3 14 15 7 9
```

**Output**
```
7
```

---

## What this problem really is

Maximize `gcd(xi, xj)` over `i != j`. Pairwise GCDs are too slow; values are capped at `1e6`.

## What to use

| Tool | Why |
|------|-----|
| Frequency of values | Know how many array elements are multiples of `d` |
| Scan `d` from large to small | First `d` with ≥2 multiples is the answer |
| Harmonic loop | Count multiples of `d` in `O(MAX/d)` total |

## Approach

**Root idea:** the answer is the largest `d` such that at least two array values are divisible by `d` (then those two have gcd ≥ `d`, and some pair achieves gcd exactly that max).

**Mental picture:** for each possible divisor height `d`, ask “do ≥2 numbers sit on multiples of `d`?” — take the tallest yes.

**Steps**

1. Mark/count occurrences of each value in `cnt[1..MAX]`.
2. For `d = MAX..1`:
   - `multiples = 0`; for `m = d; m <= MAX; m += d`: `multiples += cnt[m]`
   - if `multiples >= 2`: print `d` and stop.

## Walkthrough (on a sample)

`7` divides both `14` and `7` → two multiples → answer **7** (larger candidates like `9` only hit one array value as a multiple pair for gcd 9, etc.).

## Pitfalls

- Taking `max(gcd of adjacent after sort)` — wrong.
- Stopping at the first `d` that divides any single max value.
- `O(n^2)` pairwise gcd — TLE.

## Complexity

- Time: `O(MAX log MAX)` harmonic  
- Space: `O(MAX)`

## Key takeaway

**Max pairwise GCD = largest `d` with at least two multiples in the array** — scan `d` downward with a frequency table.
