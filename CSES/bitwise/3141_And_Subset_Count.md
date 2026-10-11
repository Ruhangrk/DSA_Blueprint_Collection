# [3141 — And Subset Count](https://cses.fi/problemset/task/3141)

**Platform:** CSES  
**Source:** CSES Problem Set — Bitwise Operations  
**Tags:** SOS DP, subset AND, mobius  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array of `n` integers. Your task is to calculate the number of non-empty subsets whose elements' bitwise and is equal to `k` for each `k = 0, 1, ..., n`.

### Input

The first line has an integer `n`: the size of the array.

The next line has `n` integers `a1, a2, ..., an`: the contents of the array.

### Output

Print `n + 1` integers as specified above modulo `1e9+7`.

### Constraints

- `1 <= n <= 2e5`
- `0 <= ai <= n`

### Examples

**Input**
```
4
3 1 3 4
```

**Output**
```
7 4 0 3 1
```

---

## What this problem really is

For every possible AND-value `k` in `{0..n}`, count nonempty subsets with `AND(subset) = k`, mod `1e9+7`.

## What to use

| Tool | Why |
|------|-----|
| Freq of values | `ai <= n` so domain is tiny relative to SOS |
| Count subsets with `AND ⊇ k` / `AND` has all bits of `k` | Easier than exact AND |
| Mobius / inclusion on bits | Turn “AND is supermask of k” into “AND equals k” |

## Approach

**Root idea:** let `f[k]` = number of nonempty subsets whose AND is a **supermask** of `k` (i.e. `AND & k == k`). Those are subsets drawn only from elements that themselves are supermasks of `k`: if `c` such elements, `f[k] = 2^c - 1`. Then exact counts `g[k] = (# with AND == k)` satisfy `f[k] = sum_{t ⊇ k} g[t]`. Invert with mobius on the subset lattice (SOS-style subtraction): `g[k] = sum_{t ⊇ k} mu * f[t]` implemented as high-to-low bit DP.

**Mental picture:** first ask “how many subsets keep at least these bits on in the AND?” — that’s just “all elements still have those bits.” Then peel off the ones that kept extra bits, to leave exact AND `k`.

**How that becomes the method**

1. `cnt[0..n]`: frequency of each value.  
2. For each `k` in `0..n`: `c =` sum of `cnt[v]` over `v` with `(v & k) == k`; `f[k] = pow2[c] - 1` (mod).  
   (Compute all such `c` via SOS: start from freqs, zeta over supermasks.)  
3. Mobius-invert `f → g` on supermasks so `g[k]` = subsets with exact AND `k`.  
4. Print `g[0], g[1], ..., g[n]`.

**Data structures:** arrays size `n+1`; modular `pow2[0..n]`.

## Walkthrough (on a sample)

Exact AND tallies for `k=0..4` become `7 4 0 3 1` after inversion.

## Pitfalls

- Including empty subset (`2^c` without `-1`).  
- Inverting in the wrong direction (submasks vs supermasks).  
- `O(n * 2^{bits})` without using `ai <= n` structure.

## Complexity

- Time: `O(n log n)` SOS-style over `0..n`  
- Space: `O(n)`

## Key takeaway

**Exact AND-subset counts = supermask zeta of `(2^{#supermask elems}-1)`, then mobius invert.**
