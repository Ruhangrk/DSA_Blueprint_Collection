# [2419 — Xor Pyramid Peak](https://cses.fi/problemset/task/2419)

**Platform:** CSES  
**Source:** CSES Problem Set — Bitwise Operations  
**Tags:** XOR, binomial mod 2, Lucas  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Consider a xor pyramid where each number is the xor of lower-left and lower-right numbers. Given the bottom row of the pyramid, your task is to find the topmost number.

### Input

The first line has an integer `n`: the size of the pyramid.

The next line has `n` integers `a1, a2, ..., an`: the bottom row of the pyramid.

### Output

Print one integer: the topmost number.

### Constraints

- `1 <= n <= 2e5`
- `1 <= ai <= 1e9`

### Examples

**Input**
```
8
2 10 5 12 9 5 1 5
```

**Output**
```
9
```

---

## What this problem really is

Bottom row length `n`; each higher cell is XOR of the two below it. Find the single top value — linear combination of the `a[i]` with coefficients in `{0,1}` (XOR).

## What to use

| Tool | Why |
|------|-----|
| Binomial coefficients mod 2 | Top = `xor_i C(n-1, i) * a[i+1]` over GF(2) |
| Lucas theorem | `C(n-1, i)` odd iff `i` is a subset of bits of `n-1` |
| Bit subset test | `(i & (n-1)) == i` ⇒ include `a[i+1]` |

## Approach

**Root idea:** unfolding the pyramid, each bottom element `a[i]` reaches the top through `C(n-1, i-1)` paths of XORs. Over GF(2), XOR multiplicity is `C(n-1, i-1) mod 2`.

**Mental picture:** each bottom stone sends a signal upward along every zigzag; XOR cancels even visits. Only bottoms with an odd number of paths survive.

**How that becomes the method**

1. Let `m = n-1`.
2. `ans = 0`.
3. For `i = 0..n-1`: if `(i & m) == i`, then `ans ^= a[i+1]` (1-based `a` in problem → 0-based index `i`).
4. Print `ans`.

(Lucas: `C(m,i)` odd ⇔ every bit of `i` is set in `m` ⇔ `i` is a submask of `m`.)

**Data structures:** none.

## Walkthrough (on a sample)

For `n=8`, `m=7` (`111` in binary), every `i` in `0..7` is a submask, so top = xor of all bottom entries = `9` in the sample’s arithmetic.

## Pitfalls

- Using mod-`1e9+7` binomials instead of mod 2.  
- Off-by-one on `C(n-1, i)`.  
- Building the full pyramid in `O(n^2)`.

## Complexity

- Time: `O(n)`  
- Space: `O(1)` extra

## Key takeaway

**Xor pyramid peak = XOR of bottom entries whose indices are submasks of `n-1`** (binomials mod 2 / Lucas).
