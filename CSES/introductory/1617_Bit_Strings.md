# [1617 — Bit Strings](https://cses.fi/problemset/task/1617)

**Platform:** CSES  
**Source:** CSES Problem Set — Introductory Problems  
**Tags:** modular exponentiation, combinatorics  
**Difficulty / Rating:** Easy  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to calculate the number of bit strings of length `n`.

For example, if `n=3`, the correct answer is `8`, because the possible bit strings are `000`, `001`, `010`, `011`, `100`, `101`, `110`, and `111`.

### Input

The only input line has an integer `n`.

### Output

Print the result modulo `1e9+7`.

### Constraints

- `1 <= n <= 1e6`

### Examples

**Input**
```
3
```

**Output**
```
8
```

---

## What this problem really is

Count binary strings of length `n` → exactly `2^n`, taken modulo `1e9+7`.

## What to use

| Tool | Why |
|------|-----|
| Fast pow mod | Compute `2^n % MOD` in `O(log n)` |
| Or iterative multiply | `O(n)` also fine for `n <= 1e6` |
| `MOD = 1e9+7` | Required by the statement |

## Approach

**Root idea:** each of `n` positions is independently `0` or `1` → `2^n` strings.

**Mental picture:** a row of `n` light switches; count all on/off patterns.

**Steps**

1. Compute `pow(2, n, MOD)` (binary exponentiation) or loop-multiply.
2. Print it.

## Walkthrough (on a sample)

`n=3` → `2^3 = 8`.

## Pitfalls

- Forgetting the modulo.
- Using floating `pow` — precision / not modular.

## Complexity

- Time: `O(log n)` with fast pow  
- Space: `O(1)`

## Key takeaway

**Bit strings of length `n` = `2^n mod 1e9+7`.**
