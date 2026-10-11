# [1654 — SOS Bit Problem](https://cses.fi/problemset/task/1654)

**Platform:** CSES  
**Source:** CSES Problem Set — Bitwise Operations  
**Tags:** SOS DP, subset sum over bits, zeta transform  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given a list of `n` integers, your task is to calculate for each element `x`:

1. the number of elements `y` such that `x | y = x`
2. the number of elements `y` such that `x & y = x`
3. the number of elements `y` such that `x & y != 0`

### Input

The first line has an integer `n`: the size of the list.

The next line has `n` integers `x1, x2, ..., xn`: the elements of the list.

### Output

Print `n` lines: for each element the required values.

### Constraints

- `1 <= n <= 2e5`
- `1 <= xi <= 1e6`

### Examples

**Input**
```
5
3 7 2 9 2
```

**Output**
```
3 2 5
4 1 5
2 4 4
1 1 3
2 4 4
```

---

## What this problem really is

For every list value `x`, three population counts over the multiset:

1. `y` is submask of `x` (`x|y=x` ⇔ `y` bits ⊆ `x` bits)  
2. `x` is submask of `y` (`x&y=x`)  
3. `y` shares a bit with `x`

## What to use

| Tool | Why |
|------|-----|
| Frequency on `0..MAX` | `MAX=1e6`, fit in arrays |
| SOS / zeta on submasks | Count submasks / supermasks fast |
| Complements | Query (3) from totals and disjoint counts |

## Approach

**Root idea:** rewrite the three predicates as submask/supermask queries on bitmasks, then answer all of them with Sum-Over-Subsets DP on the frequency array.

**Mental picture:** each number is a set of bits. (1) counts how many listed sets sit inside `x`; (2) how many contain `x`; (3) how many stab `x`. SOS fills “how many in my subset family?” for every mask at once.

**How that becomes the method**

1. Let `MAX = 1<<20` (or next power covering `1e6`). `cnt[v]++` for each `xi`.
2. **Submask zeta:** `f[mask] = sum of cnt[sub]` over `sub ⊆ mask`. Then for query (1) on `x`, answer is `f[x]` (includes itself).
3. **Supermask zeta:** `g[mask] = sum of cnt[super]` over `super ⊇ mask`. Query (2) = `g[x]`.
4. Query (3): `n -` (number of `y` with `x & y = 0`). Numbers disjoint from `x` are submasks of the global complement universe restricted to bits outside `x` — count via `f` on `((MAX-1) ^ x)` carefully (only values that are submasks of `~x`), i.e. `f[~x & (MAX-1)]` if all values `< MAX`, but only counting y whose bits lie in `~x`. Yes: `y & x = 0` ⇔ `y ⊆ (~x)`. So disjoint count = `f[(MAX-1) ^ x]` if we built `f` as submask sums — wait, `f[U]` sums submasks of `U`, so set `U = (~x) & (MAX-1)`, disjoint = `f[U]`. Then answer (3) = `n - f[U]`.

Build both zetas in `O(MAX * B)`.

**Data structures:** `cnt`, `f`, `g` arrays of size `MAX`.

## Walkthrough (on a sample)

For `x=3` (`11`b): submasks among the list give `3`; supermasks give `2`; all five meet bit-share test as printed `3 2 5`.

## Pitfalls

- Off-by-one on including `x` itself.  
- `O(n^2)` pairwise checks.  
- Wrong universe size for complements.

## Complexity

- Time: `O(MAX * B + n)`  
- Space: `O(MAX)`

## Key takeaway

**Per-value submask/supermask counts = SOS DP on frequencies**; disjointness gives the third via complement.
