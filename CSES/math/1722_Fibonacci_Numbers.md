# [1722 — Fibonacci Numbers](https://cses.fi/problemset/task/1722)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** matrix exponentiation, linear recurrence  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

The Fibonacci numbers can be defined as follows:

- `F_0 = 0`
- `F_1 = 1`
- `F_n = F_{n-2} + F_{n-1}`

Your task is to calculate the value of `F_n` for a given `n`.

### Input

The only input line has an integer `n`.

### Output

Print the value of `F_n` modulo `1e9+7`.

### Constraints

- `0 <= n <= 1e18`

### Examples

**Input**
```
10
```

**Output**
```
55
```

---

## What this problem really is

Compute the `n`-th Fibonacci number modulo `1e9+7` for huge `n` (up to `1e18`) — plain `O(n)` DP is impossible.

## What to use

| Tool | Why |
|------|-----|
| Linear recurrence → matrix form | Turn `F_n = F_{n-1} + F_{n-2}` into a 2×2 step |
| Binary exponentiation of matrices | Reach step `n` in `O(log n)` matrix multiplies |
| Modular arithmetic | Required output |

## Approach

**Root idea:** one step of Fibonacci is a linear map on the pair `(F_k, F_{k-1})`. Raising that map to the `n`-th power jumps straight to `F_n`.

**Mental picture:** keep a sliding window of the last two values. Instead of sliding once per index, pack “slide once” into a matrix and square it like fast pow.

**Transition**

```
[ F_k     ]   [ 1 1 ] [ F_{k-1} ]
[ F_{k-1} ] = [ 1 0 ] [ F_{k-2} ]
```

So

```
[ F_n     ]   [ 1 1 ]^n  [ F_0 ]     with careful indexing, or
[ F_{n-1} ] = [ 1 0 ]    [ F_{-1} ]

[ F_{n+1} ]   [ 1 1 ]^n  [ F_1 ] = [ 1 ]
[ F_n     ] = [ 1 0 ]    [ F_0 ]   [ 0 ]
```

Common clean form: compute `M^n` where `M = [[1,1],[1,0]]`; then `F_n = (M^n)[0][1]` (or `(M^n)[1][0]`), and `F_0 = 0` falls out as identity / zero edge.

**Steps**

1. If `n == 0` print `0`.
2. Matrix-pow `M` to power `n` (multiply 2×2 matrices mod `MOD`).
3. Read off `F_n` from the result.

(Doubling identities `F(2k)`, `F(2k+1)` are an equivalent alternative — same `O(log n)` idea.)

## Walkthrough (on a sample)

`n = 10`: matrix pow yields `F_10 = 55`. Small check: `0,1,1,2,3,5,8,13,21,34,55`.

## Pitfalls

- Off-by-one in which matrix entry is `F_n`.
- `n = 0` special case.
- Multiplying without taking `% MOD` (overflow / WA).
- Trying `O(n)` loop — TLE at `1e18`.

## Complexity

- Time: `O(log n)` 2×2 multiplies  
- Space: `O(1)`

## Key takeaway

**Huge Fibonacci index → matrix exponentiation** of the 2×2 companion matrix in `O(log n)`.
