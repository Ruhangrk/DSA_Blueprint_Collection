# [2209 — Counting Necklaces](https://cses.fi/problemset/task/2209)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** Burnside, group actions, number theory  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to count the number of different necklaces that consist of `n` pearls and each pearl has `m` possible colors.

Two necklaces are considered to be different if it is not possible to rotate one of them so that they look the same.

### Input

The only input line has two numbers `n` and `m`: the number of pearls and colors.

### Output

Print one integer: the number of different necklaces modulo `1e9+7`.

### Constraints

- `1 <= n, m <= 1e6`

### Examples

**Input**
```
4 3
```

**Output**
```
24
```

---

## What this problem really is

Count colorings of `n` beads with `m` colors **up to rotation** (cyclic group `C_n`). Flips/reflections are **not** identified — only rotations.

## What to use

| Tool | Why |
|------|-----|
| Burnside’s lemma | Average number of colorings fixed by each rotation |
| `gcd` + cycle decomposition | Rotation by `k` positions has `gcd(k, n)` cycles |
| Modular inverse of `n` | Divide the Burnside sum by `|G| = n` |
| Fast pow `m^e % MOD` | Fixed colorings per group element |

## Approach

**Root idea (Burnside):** number of distinct necklaces = `(1 / n) * sum over rotations R of (colorings fixed by R)`.

**Mental picture:** spinning the necklace. Two colorings look the same if one spin turns one into the other. Burnside says: for each spin, count colorings that look identical after that spin, then average.

A rotation by `k` positions (beads move `i -> (i+k) % n`) splits the beads into `gcd(k, n)` cycles. A coloring is fixed iff every cycle is monochrome → `m ^ gcd(k, n)` fixed colorings.

So

```
ans = (1/n) * sum_{k=0..n-1}  m^{gcd(k,n)}   (mod 1e9+7)
```

**How that becomes the method**

1. Sum `pow(m, gcd(k, n))` for `k = 0 .. n-1` (or `k = 1 .. n` with `gcd(n,n)=n` same set of gcds).
2. Multiply by modular inverse of `n`.
3. Print.

**Faster rewrite (same answer):** group by `d = gcd(k,n)`:

```
ans = (1/n) * sum_{d|n}  phi(n/d) * m^d
```

or equivalently `(1/n) * sum_{d|n} phi(d) * m^{n/d}`. With `n <= 1e6` the plain `O(n log)` loop is usually enough; the divisor form is nicer when you already factor `n`.

## Walkthrough (on a sample)

`n = 4`, `m = 3`. Rotations `k = 0,1,2,3`:

| k | gcd(k,4) | fixed |
|---|----------|-------|
| 0 | 4 | `3^4 = 81` |
| 1 | 1 | `3^1 = 3` |
| 2 | 2 | `3^2 = 9` |
| 3 | 1 | `3^1 = 3` |

Sum `81+3+9+3 = 96`, divide by `4` → `24`.

## Pitfalls

- Counting linear strings `m^n` (no rotations).
- Using full dihedral Burnside (flips) — this problem is rotations only.
- Wrong cycle count: rotation by `k` has `gcd(k,n)` cycles, not `n/gcd`.
- Integer `/ n` instead of `* inv(n)` mod `1e9+7`.
- `k = 0`: `gcd(0,n) = n` — include it (identity: all `m^n` colorings fixed).

## Complexity

- Time: `O(n log MOD)` with per-`k` pow, or much less with divisor + phi form  
- Space: `O(1)` extra beyond input

## Key takeaway

**Necklaces under rotation = Burnside:** `(1/n) * sum_k m^{gcd(k,n)}` mod `1e9+7`.
