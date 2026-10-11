# [3214 — Water Containers Queries](https://cses.fi/problemset/task/3214)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems I  
**Tags:** Number theory, gcd, Bezout-style reachability, feasibility check  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Two containers: A has volume `a`, B has volume `b`. Both start empty. Using fill, empty, and move operations, can container A hold exactly `x` units at the end?

### Input

- The first line has an integer `n`: the number of tests.
- The next `n` lines each have three integers `a`, `b`, `x`.

### Output

For each test, print `YES` if it is possible, otherwise `NO`.

### Constraints

- `1 <= n <= 1000`
- `1 <= a, b, x <= 1e9`

### Examples

**Input**
```
7
5 3 4
1 1 1
1 1 2
2 2 1
123 456 42
1000 999 123
1000 998 123
```

**Output**
```
YES
YES
NO
NO
YES
YES
NO
```

---

## What this problem really is

From the previous problem's state model, decide whether state `(x, *)` is reachable. The answer depends only on two simple facts about `a`, `b`, `x`. No search is needed.

## What to use

| Tool | Why |
|------|-----|
| `g = gcd(a, b)` | Every amount of water ever present in either container is a multiple of `g`, since all operations add or remove `a`, `b`, or existing water |
| Capacity check `x <= a` | Container A cannot hold more than `a` |
| Modulus check `x % g == 0` | Water amounts in A are multiples of `g` |

## Approach

**Root idea:** a container can only ever hold amounts that are integer combinations of `a` and `b`, which means multiples of `g = gcd(a, b)`. Conversely, every multiple of `g` in `[0, a]` can be reached by the standard pour cycle. So `x` is reachable in A iff `x <= a` and `g` divides `x`.

**Mental picture:** imagine a cycle of pours between two buckets. Repeatedly filling B, pouring into A, and emptying A moves water in steps of `gcd(a, b)`. The pouring rule chops amounts into pieces that always add up to multiples of `g`. A cannot exceed its own capacity, so `x <= a` is required. Within that range, the multiples of `g` are exactly the amounts the pouring can produce.

**How that becomes the method**

1. Let `g = gcd(a, b)`.
2. If `x > a`, answer `NO`.
3. Else if `x % g != 0`, answer `NO`.
4. Else answer `YES`.

**Data structures:** none beyond integers.

## Walkthrough (on a sample)

Take `1000 998 123`. `g = gcd(1000, 998) = 2`. `x = 123 <= 1000` passes the capacity check. `123 % 2 = 1 != 0`, so the modulus check fails. Answer `NO`. Matches.

Take `123 456 42`. `g = gcd(123, 456)`. `123 = 3 * 41`, `456 = 2^3 * 3 * 19`, so `g = 3`. `x = 42 <= 123` passes. `42 % 3 = 0`. Answer `YES`. Matches.

Take `1 1 2`. `x = 2 > a = 1`, fails the capacity check. Answer `NO`. Matches. (This is the case where `x` is a multiple of `g` but still too large for A.)

Take `2 2 1`. `g = 2`, `x = 1` is not a multiple of 2. Answer `NO`. Matches.

Take `5 3 4`. `g = 1`, `x = 4 <= 5`. Answer `YES`. Matches.

## Pitfalls

- Checking `x <= max(a, b)` instead of `x <= a`. Container A is the only one that must hold `x`, so its capacity is what counts. Example: `a = 1`, `b = 5`, `x = 3` is `NO`.
- Forgetting the divisibility check when `x <= a`. Example: `2 2 1` passes the capacity check but fails divisibility.
- Using `x == 0`. Constraints say `x >= 1`, so that case does not arise.
- Using `int` for the gcd computation with values up to `1e9`. `int` is enough for `1e9`, but products should use `long long` if you extend the logic.

## Complexity

- Time: `O(log(min(a, b)))` per test for the gcd, `O(n log)` overall.
- Space: `O(1)`.

## Key takeaway

Reachability in a two-container pouring system is governed by `gcd(a, b)` plus the capacity of the target container. Check those two numbers instead of simulating.
