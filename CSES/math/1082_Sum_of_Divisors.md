# [1082 — Sum of Divisors](https://cses.fi/problemset/task/1082)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** Dirichlet convolution, floor blocks, modular arithmetic  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Let `sigma(n)` denote the sum of divisors of an integer `n`. For example, `sigma(12) = 1+2+3+4+6+12 = 28`.

Your task is to calculate the sum `sum_{i=1}^n sigma(i)` modulo `1e9+7`.

### Input

The only input line has an integer `n`.

### Output

Print `sum_{i=1}^n sigma(i)` modulo `1e9+7`.

### Constraints

- `1 <= n <= 1e12`

### Examples

**Input**
```
5
```

**Output**
```
21
```

---

## What this problem really is

Compute `sum_{i=1}^n sigma(i)` for huge `n` (up to `1e12`). Cannot loop to `n`. Use the identity that switches the order of summation.

## What to use

| Tool | Why |
|------|-----|
| Rewrite | `sum_i sigma(i) = sum_d d * floor(n/d)` |
| Floor blocks | `floor(n/d)` constant on ranges of `d` |
| Mod `1e9+7` | Sum of arithmetic series mod prime |
| `__int128` / careful mul | Products before mod |

## Approach

**Root idea:** every divisor pair `(d, k)` with `d*k <= n` contributes `d` once for each multiple. Equivalently:

```
sum_{i=1}^n sigma(i) = sum_{d=1}^n d * floor(n/d)
```

For each `d`, `d` is added once for every multiple of `d` up to `n` — that’s `floor(n/d)` times.

**Mental picture:** for each possible divisor value `d`, stamp `d` onto every multiple; total ink is the answer.

**Floor-block trick:** values of `floor(n/d)` are constant for consecutive `d` in `[l,r]`. On each block, sum `d` from `l` to `r` is an arithmetic series: `(l+r)*(r-l+1)/2`, multiply by the constant `floor(n/l)`.

**Steps**

1. `ans = 0`, `d = 1`.
2. While `d <= n`:
   - `q = n/d`
   - `r = n/q` (last index with same `floor`)
   - `ans += q * sum(d..r)  (mod MOD)`
   - `d = r+1`
3. Print `ans`.

Number of blocks is `O(sqrt n)`.

## Walkthrough (on a sample)

`n=5`: `sigma(1)+...+sigma(5) = 1+3+4+7+6 = 21`.

## Pitfalls

- Computing `/2` mod MOD without modular inverse of `2`.
- Overflow when multiplying `q * sum` before `% MOD`.
- Off-by-one on block end `r = n/(n/d)`.

## Complexity

- Time: `O(sqrt n)`  
- Space: `O(1)`

## Key takeaway

**`sum sigma(i) = sum d*floor(n/d)`** — evaluate with floor blocks in `O(sqrt n)`.
