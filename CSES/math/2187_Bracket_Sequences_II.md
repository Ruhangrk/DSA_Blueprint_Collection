# [2187 — Bracket Sequences II](https://cses.fi/problemset/task/2187)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** Catalan triangle, ballot theorem, combinatorics  
**Difficulty / Rating:** Medium–Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to calculate the number of valid bracket sequences of length `n` when a prefix of the sequence is given.

### Input

The first input line has an integer `n`.

The second line has a string of `k` characters: the prefix of the sequence.

### Output

Print the number of sequences modulo `1e9+7`.

### Constraints

- `1 <= k <= n <= 1e6`

### Examples

**Input**
```
6
(()
```

**Output**
```
2
```

Explanation: There are two possible sequences: `(())()` and `(()())`.

---

## What this problem really is

Count ways to complete a given prefix into a length-`n` balanced bracket string. Same Dyck-path world as Catalan counts, but you start mid-path with a forced balance and remaining `(` / `)` quotas.

## What to use

| Tool | Why |
|------|-----|
| Prefix balance scan | Reject impossible prefixes early |
| Ballot / Catalan-triangle formula | Count remaining paths that never go below 0 and end at 0 |
| `fact` / `invfact` up to `n` | Fast binomials mod `1e9+7` |

## Approach

**Root idea:** after a valid prefix with `open` opens and `close` closes, you still need exactly `needL = n/2 - open` more `(` and `needR = n/2 - close` more `)`. Among all ways to place those letters in the remaining `rem = n - k` positions, subtract the ones that would dip below balance 0 (reflection / ballot).

**Mental picture:** height starts at `bal = open - close >= 0`. Each remaining `(` climbs, each `)` drops. You must end at height 0 and never go negative. The ballot count for “`needL` ups and `needR` downs from a legal start” collapses to a difference of two binomials.

**How that becomes the method**

1. If `n` is odd → `0` (impossible to balance).
2. Scan the prefix: track `open`, `close`, running balance. If balance ever goes negative, or `open > n/2` → `0`.
3. Let `needL = n/2 - open`, `needR = n/2 - close`, `rem = needL + needR` (must equal `n - k`).
4. If `needL < 0` or `needR < 0` → `0`. If `rem == 0` → `1` (prefix already complete and valid).
5. Total unrestricted fillings with those quotas: `tot = binom(rem, needL)`.
6. Invalid ones (reflection): `bad = binom(rem, needL - 1)` when `needL >= 1`, else `0`.
7. Answer `(tot - bad + MOD) % MOD`.

(This is the same formula used in common AC solutions: `C(rem, needL) - C(rem, needL - 1)`.)

## Walkthrough (on a sample)

Prefix `(()`, `n = 6`: `open = 2`, `close = 1`, balance never negative.  
`needL = 1`, `needR = 2`, `rem = 3`.  
`tot = binom(3,1) = 3`, `bad = binom(3,0) = 1` → `3 - 1 = 2`  
(`(())()` and `(()())`; the third unrestricted filling `())(()` is illegal / not reachable under the ballot subtract).

## Pitfalls

- Not checking that the prefix itself stays non-negative.
- Using Catalan `C_{n/2}` and ignoring the prefix.
- `needL - 1 < 0`: treat `binom(rem, -1)` as `0`, do not wrap.
- Odd `n`, or `open > n/2`, must be `0`.
- Modular subtraction needs `+ MOD` before `%`.

## Complexity

- Time: `O(n)` for factorials + `O(k)` prefix scan  
- Space: `O(n)`

## Key takeaway

**Fixed prefix → ballot / Catalan-triangle:** after checking the prefix, answer is `binom(rem, needL) - binom(rem, needL - 1)` mod `1e9+7`.
