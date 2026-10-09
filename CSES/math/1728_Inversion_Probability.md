# [1728 — Inversion Probability](https://cses.fi/problemset/task/1728)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** expected value, linearity, inversions  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

An array has `n` integers `x[1], x[2], ..., x[n]`, and each of them has been randomly chosen between `1` and `r[i]`. An inversion is a pair `(a, b)` where `a < b` and `x[a] > x[b]`.

What is the expected number of inversions in the array?

### Input

The first input line contains an integer `n`: the size of the array.

The second line contains `n` integers `r[1], r[2], ..., r[n]`: the range of possible values for each array position.

### Output

Print the expected number of inversions rounded to six decimal places (rounding half to even).

### Constraints

- `1 <= n <= 100`
- `1 <= r[i] <= 100`

### Examples

**Input**
```
3
5 2 7
```

**Output**
```
1.057143
```

---

## What this problem really is

Each `x[i]` is independent uniform on `{1..r[i]}`. Expected number of pairs `(i < j)` with `x[i] > x[j]`.

## What to use

| Tool | Why |
|------|-----|
| Linearity of expectation | `E[#inv] = sum_{i<j} P(x[i] > x[j])` — no correlation headaches |
| Double count / closed form per pair | `r[i], r[j] <= 100` so `O(r)` or `O(1)` per pair is fine |
| Exact fractions / high precision | Half-to-even rounding can fail with naive `double` on nasty cases |

## Approach

**Root idea:** indicator for each pair. Sum the inversion probabilities; independence of distinct positions makes each pair easy.

**Mental picture:** for fixed `(i, j)`, draw two independent values in rectangles `[1..r[i]] x [1..r[j]]` and measure the fraction of cells above the diagonal `x[i] > x[j]`.

**How to get `P(x[i] > x[j])`**

Enumerate (or formula):

```
P = (1 / (r[i] * r[j])) * (number of pairs (u,v) with 1<=u<=r[i], 1<=v<=r[j], u>v)
```

Loop `u = 1..r[i]`: add `min(u-1, r[j])` (values `v` strictly below `u`).

Closed forms exist by cases `r[i] <= r[j]` vs `>` (count cells under the diagonal of the rectangle), but the `O(r)` loop is clear and fast enough: `O(n^2 * 100)`.

**Steps**

1. `ans = 0`.
2. For every `i < j`, add `P(x[i] > x[j])`.
3. Print `ans` to six decimals with half-to-even.

**Precision warning:** some official tests sit extremely close to a rounding boundary (e.g. `...5000` with tiny extra). Prefer exact rational arithmetic (common denominator involving products of `r`'s) or carefully scaled integers, then round — pure `double`/`long double` can WA.

## Walkthrough (on a sample)

`r = [5,2,7]`. Three pairs:

- `(1,2)`: `P(x1 > x2)` over `5*2 = 10` cells  
- `(1,3)`, `(2,3)` similarly  

Sum of the three probabilities = `1.057143` after rounding.

## Pitfalls

- Defining inversion as `x[a] >= x[b]` (ties are not inversions).
- Forgetting `a < b` (index order).
- Floating rounding near half-uptick — the famous hard part of this task.
- Trying to simulate all arrays (`product of r[i]` explodes).

## Complexity

- Time: `O(n^2 * R)` with `R <= 100`  
- Space: `O(n)`

## Key takeaway

**Expected inversions = sum of per-pair `P(x[i] > x[j])`** — linearity; compute each rectangle carefully and round exactly.
