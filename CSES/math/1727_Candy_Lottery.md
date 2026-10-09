# [1727 — Candy Lottery](https://cses.fi/problemset/task/1727)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** expected value, order statistics, CDF  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` children, and each of them independently gets a random integer number of candies between `1` and `k`.

What is the expected maximum number of candies a child gets?

### Input

The only input line contains two integers `n` and `k`.

### Output

Print the expected number rounded to six decimal places (rounding half to even).

### Constraints

- `1 <= n <= 100`
- `1 <= k <= 100`

### Examples

**Input**
```
2 3
```

**Output**
```
2.444444
```

---

## What this problem really is

`n` i.i.d. uniform integers on `{1..k}`; compute `E[max]`.

## What to use

| Tool | Why |
|------|-----|
| CDF of the maximum | `P(max <= x) = (x/k)^n` |
| `P(max = x) = P(max <= x) - P(max <= x-1)` | Discrete expectation |
| Direct sum `sum x * P(max = x)` | Small `k` |

## Approach

**Root idea:** the max is `<= x` iff every child rolled at most `x`. That probability is `(x/k)^n` because each of `n` kids has `x` good values out of `k`.

**Mental picture:** raise the ceiling `x` from 1 to `k`. The probability mass that “first becomes enough” at ceiling `x` is the jump of the CDF.

**Formula**

```
P(max = x) = (x/k)^n - ((x-1)/k)^n
E[max] = sum_{x=1..k} x * P(max = x)
```

(with `(0/k)^n = 0`).

**Steps**

1. For `x = 1 .. k`, compute `pow(x/(double)k, n) - pow((x-1)/(double)k, n)`.
2. Add `x * that` into the answer.
3. Print with six decimals, half-to-even. Floating error on some edge cases may need a tiny epsilon or exact pow via multiplying in a loop / higher precision — watch banker's rounding near `...x5000`.

## Walkthrough (on a sample)

`n = 2`, `k = 3`. All pairs equally likely (`9` outcomes). Max values:

- max 1: only `(1,1)` → `1/9`
- max 2: `(1,2),(2,1),(2,2)` → `3/9`
- max 3: six outcomes → `5/9`

`E = 1*(1/9) + 2*(3/9) + 3*(5/9) = 22/9 ≈ 2.444444`.

## Pitfalls

- Using `P(max = x) = (x/k)^n` without subtracting the `x-1` term.
- Integer division `x/k`.
- Rounding half-away-from-zero instead of half-to-even on knife-edge floats.

## Complexity

- Time: `O(k log n)` with `pow`, or `O(k * n)` multiplying  
- Space: `O(1)`

## Key takeaway

**`E[max]` of i.i.d. uniform `{1..k}` = sum_x x * ((x/k)^n - ((x-1)/k)^n)`.**
