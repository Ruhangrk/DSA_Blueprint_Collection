# [2064 — Bracket Sequences I](https://cses.fi/problemset/task/2064)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** Catalan numbers, combinatorics  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to calculate the number of valid bracket sequences of length `n`. For example, when `n=6`, there are 5 sequences:

- `()()()`
- `()(())`
- `(())()`
- `((()))`
- `(()())`

### Input

The only input line has an integer `n`.

### Output

Print the number of sequences modulo `1e9+7`.

### Constraints

- `1 <= n <= 1e6`

### Examples

**Input**
```
6
```

**Output**
```
5
```

---

## What this problem really is

Count balanced `()`-strings of length `n`. If `n` is odd, answer is `0`. If `n = 2*m`, the answer is the `m`-th Catalan number `C_m`.

## What to use

| Tool | Why |
|------|-----|
| Catalan formula `C_m = binom(2*m, m) / (m+1)` | Exact count of length-`2*m` balanced brackets |
| `fact` / `invfact` up to `n` | Compute binomial + divide by `m+1` mod `1e9+7` |
| Parity check | Odd length can never balance |

## Approach

**Root idea:** every nonempty balanced string splits uniquely as `(A)B` where `A` and `B` are balanced. That recurrence is the Catalan recurrence; the closed form is `C_m = (1/(m+1)) * binom(2*m, m)`.

**Mental picture:** walk on a line: `(` is +1, `)` is −1. You need to end at 0 after `2*m` steps and never go below 0 (Dyck path). Total paths end at 0: `binom(2*m, m)`. Paths that touch −1 are killed by the reflection principle, leaving exactly the Catalan count.

**How that becomes the method**

1. If `n` is odd → print `0`.
2. Else `m = n/2`. Precompute factorials and inverse factorials up to `2*m`.
3. `ans = binom(2*m, m) * inv(m+1) % MOD`  
   (equivalently `binom(2*m, m) - binom(2*m, m-1)` with `binom(..., -1) = 0`).
4. Print `ans`.

## Walkthrough (on a sample)

`n = 6` → `m = 3`.  
`C_3 = binom(6,3) / 4 = 20 / 4 = 5` — matches the five listed strings.

## Pitfalls

- Treating `n` as the number of pairs instead of the string length (`C_n` vs `C_{n/2}`).
- Odd `n` → must output `0`, not a Catalan number.
- Dividing by `m+1` with integer `/` instead of modular inverse.
- Forgetting `MOD = 1e9+7`.

## Complexity

- Time: `O(n)` preprocess factorials  
- Space: `O(n)`

## Key takeaway

**Balanced brackets of length `2*m` = Catalan `C_m = binom(2*m, m) / (m+1)`** — odd length is impossible.
