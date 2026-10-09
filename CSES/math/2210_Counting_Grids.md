# [2210 — Counting Grids](https://cses.fi/problemset/task/2210)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** Burnside, rotations, combinatorics  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to count the number of different `n x n` grids whose each square is black or white.

Two grids are considered to be different if it is not possible to rotate one of them so that they look the same.

### Input

The only input line has an integer `n`: the size of the grid.

### Output

Print one integer: the number of grids modulo `1e9+7`.

### Constraints

- `1 <= n <= 1e9`

### Examples

**Input**
```
4
```

**Output**
```
16456
```

---

## What this problem really is

Count 2-colorings of an `n x n` board **up to rotation** (group `C_4`: 0°, 90°, 180°, 270°). Reflections are not identified.

## What to use

| Tool | Why |
|------|-----|
| Burnside’s lemma | Average fixed colorings over the 4 rotations |
| Cycle count per rotation | A coloring is fixed iff each cycle is monochrome → `2^(#cycles)` |
| Fast pow mod `1e9+7` | Exponents up to `n*n` with `n <= 1e9` |

## Approach

**Root idea:** distinct grids = `(1/4) * (Fix(0°) + Fix(90°) + Fix(180°) + Fix(270°))`.

**Mental picture:** spinning the square. Only colorings that look the same after a given spin count toward that spin’s fixed set. Each orbit of cells under the spin must be one color.

**Cycle sizes**

- `0°` (identity): `n*n` singleton cycles → `Fix = 2^(n*n)`.
- `90°` / `270°`: cells move in 4-cycles (plus the center if `n` odd).  
  - even `n`: `n*n / 4` cycles  
  - odd `n`: `(n*n + 3) / 4` cycles  
  Same count for 90° and 270°.
- `180°`: 2-cycles (plus center if odd).  
  - even: `n*n / 2`  
  - odd: `(n*n + 1) / 2`

Compact exponents (let `A = n*n`):

```
e0  = A
e90 = (A + 3*(n%2)) / 4
e180 = (A + (n%2)) / 2
```

**Steps**

1. Compute those three exponents (careful with 64-bit: `n*n` fits in `long long` for `n <= 1e9`).
2. `ans = (pow(2,e0) + 2*pow(2,e90) + pow(2,e180)) % MOD`
3. Multiply by `inv(4)` mod `MOD`.

## Walkthrough (on a sample)

`n = 4`: `e0 = 16`, `e90 = 4`, `e180 = 8`.  
`(2^16 + 2*2^4 + 2^8) / 4 = (65536 + 32 + 256) / 4 = 16456`.

## Pitfalls

- Including reflections (dihedral) — wrong problem.
- Wrong odd-`n` formulas (forgetting the fixed center).
- Overflow when forming `n*n` in 32-bit ints.
- Dividing by 4 with integer `/` instead of modular inverse.

## Complexity

- Time: `O(log (n*n))` for modular pows  
- Space: `O(1)`

## Key takeaway

**2-color grids up to rotation = Burnside on `C_4`:** average `2^(cycles)` over 0°/90°/180°/270°, with odd/even cycle formulas.
