# [1618 — Trailing Zeros](https://cses.fi/problemset/task/1618)

**Platform:** CSES  
**Source:** CSES Problem Set — Introductory Problems  
**Tags:** number theory, factorial, factors of 5  
**Difficulty / Rating:** Easy  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to calculate the number of trailing zeros in the factorial `n!`.

For example, `20! = 2432902008176640000` and it has `4` trailing zeros.

### Input

The only input line has an integer `n`.

### Output

Print the number of trailing zeros in `n!`.

### Constraints

- `1 <= n <= 1e9`

### Examples

**Input**
```
20
```

**Output**
```
4
```

---

## What this problem really is

Count factors of `10` in `n!`. Since `10 = 2 * 5` and there are always more factors of `2` than of `5`, this equals the exponent of `5` in `n!`.

## What to use

| Tool | Why |
|------|-----|
| de Polignac / Legendre formula | `floor(n/5) + floor(n/25) + floor(n/125) + ...` |
| Loop while `p <= n` | `n` up to `1e9` — cannot compute `n!` |

## Approach

**Root idea:** every multiple of `5` contributes a trailing zero (paired with a `2`); multiples of `25` contribute an extra `5`, and so on.

**Mental picture:** stack the powers of five like nested jars — count how many numbers ≤ `n` sit in each jar.

**Steps**

1. `ans = 0`, `p = 5`.
2. While `p <= n`: `ans += n / p`; if `p > n/5` break carefully to avoid overflow, else `p *= 5`.
3. Print `ans`.

## Walkthrough (on a sample)

`n=20`: `floor(20/5)=4`, `floor(20/25)=0` → **4**.

## Pitfalls

- Computing `n!` directly — impossible at `1e9`.
- Stopping after only `/5` (misses `25, 125, ...`).
- `p *= 5` overflowing `int` — use `long long` or stop when `p > n`.

## Complexity

- Time: `O(log_5 n)`  
- Space: `O(1)`

## Key takeaway

**Trailing zeros of `n!` = number of times `5` divides `n!`** = `n/5 + n/25 + n/125 + ...`.
