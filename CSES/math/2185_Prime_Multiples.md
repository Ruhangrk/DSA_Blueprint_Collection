# [2185 — Prime Multiples](https://cses.fi/problemset/task/2185)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** inclusion-exclusion, bitmasks  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given `k` distinct prime numbers `a1, a2, ..., ak` and an integer `n`.

Your task is to calculate how many of the first `n` positive integers are divisible by at least one of the given prime numbers.

### Input

The first input line has two integers `n` and `k`.

The second line has `k` prime numbers `a1, a2, ..., ak`.

### Output

Print one integer: the number of integers within the interval `1, 2, ..., n` that are divisible by at least one of the prime numbers.

### Constraints

- `1 <= n <= 1e18`
- `1 <= k <= 20`
- `2 <= ai <= n`

### Examples

**Input**
```
20 2
2 5
```

**Output**
```
12
```

Explanation: the 12 numbers are `2,4,5,6,8,10,12,14,15,16,18,20`.

---

## What this problem really is

Count integers in `1..n` divisible by at least one of `k` given primes. Union of arithmetic progressions — inclusion-exclusion. `n` is huge; `k <= 20` so `2^k` subsets are fine.

## What to use

| Tool | Why |
|------|-----|
| Inclusion-exclusion | `|union multiples of ai|` |
| Bitmask over subsets | Product of primes in subset = LCM (they’re distinct primes) |
| Careful multiply | LCM product may exceed `1e18` — stop if overflow |

## Approach

**Root idea:** for a nonempty set `S` of the primes, numbers divisible by all of them are multiples of `P = product(S)`. There are `floor(n/P)` such numbers. Add when `|S|` odd, subtract when even.

**Mental picture:** Venn diagram of “divisible by p_i”; IE stitches the overlaps.

**Steps**

1. `ans = 0`.
2. For `mask = 1 .. (1<<k)-1`:
   - Multiply primes in `mask` into `P` (abort this mask if `P > n` / overflow).
   - If `__builtin_popcount(mask)` odd: `ans += n/P`; else `ans -= n/P`.
3. Print `ans`.

## Walkthrough (on a sample)

`n=20`, primes `2,5`:

- `|2|`: `10`, `|5|`: `4`, `|2∩5|`: `2`
- `10+4-2 = 12`

## Pitfalls

- Overflow when multiplying many primes (`__int128` or check `P > n/a[i]` before multiply).
- Including the empty set.
- Using LCM formulas for non-primes — here product equals LCM because primes are distinct.

## Complexity

- Time: `O(2^k * k)`  
- Space: `O(k)`

## Key takeaway

**Count multiples of any of k primes = inclusion-exclusion over subset products** `floor(n / Π S)`.
