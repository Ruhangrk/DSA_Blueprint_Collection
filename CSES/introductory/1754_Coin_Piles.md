# [1754 — Coin Piles](https://cses.fi/problemset/task/1754)

**Platform:** CSES  
**Source:** CSES Problem Set — Introductory Problems  
**Tags:** math, invariants  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You have two coin piles containing `a` and `b` coins. On each move, you can either remove one coin from the left pile and two coins from the right pile, or two coins from the left pile and one coin from the right pile.

Your task is to efficiently find out if you can empty both the piles.

### Input

The first input line has an integer `t`: the number of tests.

After this, there are `t` lines, each of which has two integers `a` and `b`: the numbers of coins in the piles.

### Output

For each test, print `"YES"` if you can empty the piles and `"NO"` otherwise.

### Constraints

- `1 <= t <= 1e5`
- `0 <= a, b <= 1e9`

### Examples

**Input**
```
3
2 1
2 2
3 3
```

**Output**
```
YES
NO
YES
```

---

## What this problem really is

Decide if `(a,b)` can reach `(0,0)` by moves `(-1,-2)` and `(-2,-1)` (only when piles stay non-negative). Large `a,b` → need a closed form, not BFS.

## What to use

| Tool | Why |
|------|-----|
| Linear combination | `x` moves of type A and `y` of type B: `x+2y=a`, `2x+y=b` |
| Totals | Each move removes `3` coins → `a+b` must be divisible by `3` |
| Balance | Neither pile more than twice the other |

## Approach

**Root idea:** suppose `x` moves take `(1,2)` and `y` moves take `(2,1)`. Then:

```
x + 2y = a
2x + y = b
```

Solving: `x = (2b - a)/3`, `y = (2a - b)/3` must be non-negative integers.

Equivalently (same conditions):

1. `(a + b) % 3 == 0`
2. `a <= 2*b` and `b <= 2*a`

(When `(a,b)=(0,0)`, YES.)

**Mental picture:** every move burns three coins with a 1:2 split; you can’t empty if one pile is more than double the other (you’d run out of the small side first).

**Steps**

1. For each test: if `(a+b)%3==0` and `a<=2*b` and `b<=2*a` → `YES`, else `NO`.
2. Use `long long` only if needed; `a,b` fit in 32-bit but doubling is fine in 64-bit.

## Walkthrough (on a sample)

`(2,1)`: sum `3`, `2<=2`, `1<=4` → **YES** (one `(2,1)` move).

`(2,2)`: sum `4` not ÷3 → **NO**.

`(3,3)`: sum `6`, and `3<=6` → **YES** (one of each move type).

## Pitfalls

- Only checking sum % 3 (misses `(1,1)`: sum 2… actually 2%3≠0; `(4,2)` works; `(5,1)` sum 6 but `5>2*1` → NO).
- Simulating with a loop for `a,b` up to `1e9` — TLE.
- Allowing negative piles mid-way in a wrong simulation.

## Complexity

- Time: `O(t)`  
- Space: `O(1)`

## Key takeaway

**Empty both piles with `(1,2)/(2,1)` moves iff `(a+b)%3==0` and neither pile exceeds twice the other.**
