# [3154 — System of Linear Equations](https://cses.fi/problemset/task/3154)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** Gaussian elimination, modular arithmetic, linear algebra  
**Difficulty / Rating:** Medium–Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given `n * (m+1)` coefficients `a[i][j]` and `b[i]` which form the following `n` linear equations:

- `a[1][1]*x[1] + a[1][2]*x[2] + ... + a[1][m]*x[m] = b[1]` (mod `1e9+7`)
- `a[2][1]*x[1] + a[2][2]*x[2] + ... + a[2][m]*x[m] = b[2]` (mod `1e9+7`)
- …
- `a[n][1]*x[1] + a[n][2]*x[2] + ... + a[n][m]*x[m] = b[n]` (mod `1e9+7`)

Your task is to find any `m` integers `x[1], x[2], ..., x[m]` that satisfy the given equations.

### Input

The first line has two integers `n` and `m`: the number of equations and variables.

The next `n` lines each have `m+1` integers `a[i][1], a[i][2], ..., a[i][m], b[i]`: the coefficients of the `i`-th equation.

### Output

Print `m` integers `x[1], x[2], ..., x[m]`: the values of the variables that satisfy the equations. The values must also satisfy `0 <= x[i] < 1e9+7`. You can print any valid solution. If no solution exists print only `-1`.

### Constraints

- `1 <= n, m <= 500`
- `0 <= a[i][j], b[i] < 1e9+7`

### Examples

**Input**
```
3 3
2 0 1 7
1 2 0 0
1 3 1 2
```

**Output**
```
2 1000000006 3
```

---

## What this problem really is

Solve `A x = b` over the field `Z / MOD` with `MOD = 1e9+7` (a prime). Return any particular solution, or report inconsistency.

## What to use

| Tool | Why |
|------|-----|
| Augmented matrix `[A | b]` | Row ops act on equations and RHS together |
| Gaussian elimination mod prime | Turn the system into echelon / RREF form |
| Modular inverse | Scale pivot rows (`MOD` is prime ⇒ every nonzero has inverse) |
| Free variables = 0 | “Any” solution: set non-pivots to 0, back-substitute pivots |

## Approach

**Root idea:** elementary row operations do not change the solution set. Clear variables column by column until each pivot column has a single equation that pins that variable (or you discover `0 = nonzero`).

**Mental picture:** each row is a weighted balance of the `x[j]`’s equal to `b[i]`. Swap and subtract rows until you can read variables one at a time from the bottom (or from RREF).

**How that becomes the method**

1. Build `n x (m+1)` matrix: first `m` cols = `a`, last col = `b`.
2. For column `col = 0 .. m-1`:
   - Find a row `r >= cur` with nonzero `mat[r][col]`; if none, this variable is free — skip (no pivot).
   - Swap that row with `cur`.
   - Multiply row `cur` by `inv(mat[cur][col])` so the pivot becomes `1`.
   - Eliminate this column in all other rows: `row -= mat[row][col] * pivot_row`.
   - `cur++`.
3. After elimination, scan for a bad row: all variable coeffs `0` but RHS nonzero → print `-1`.
4. Otherwise assign: for each pivot column, `x[col] =` that row’s RHS; free variables `x[j] = 0`.
5. Print the `m` values.

With `n,m <= 500`, full Gauss is `O(min(n,m) * n * m)` ≈ acceptable.

## Walkthrough (on a sample)

System:

```
2*x1 + 0*x2 + 1*x3 = 7
1*x1 + 2*x2 + 0*x3 = 0
1*x1 + 3*x2 + 1*x3 = 2
```

One solution is `x = (2, MOD-1, 3)` i.e. `(2, 1000000006, 3)` — check:

- `2*2 + 3 = 7`
- `2 + 2*(MOD-1) = 2 - 2 = 0` (mod MOD)
- `2 + 3*(MOD-1) + 3 = 2 - 3 + 3 = 2`

## Pitfalls

- Treating `MOD` as not prime / using gcd instead of inverse (here it is prime — inverse always exists for nonzero).
- Missing inconsistent rows `0 = nonzero` after elimination.
- Not handling free variables (underdetermined systems still need some solution).
- Integer overflow before `% MOD` in elimination.
- Forgetting to eliminate above the pivot if you aim for RREF (forward-only + back-sub also works).

## Complexity

- Time: `O(n * m * min(n, m))`  
- Space: `O(n * m)`

## Key takeaway

**Modular linear system → Gaussian elimination over `Z/MOD`:** pivot, invert, eliminate; free vars → 0; inconsistent row → `-1`.
