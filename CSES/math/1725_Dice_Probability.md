# [1725 — Dice Probability](https://cses.fi/problemset/task/1725)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** DP, probability, dice  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You throw a dice `n` times, and every throw produces an outcome between `1` and `6`. What is the probability that the sum of outcomes is between `a` and `b`?

### Input

The only input line contains three integers `n`, `a` and `b`.

### Output

Print the probability rounded to six decimal places (rounding half to even).

### Constraints

- `1 <= n <= 100`
- `1 <= a <= b <= 6*n`

### Examples

**Input**
```
2 9 10
```

**Output**
```
0.194444
```

---

## What this problem really is

Fair six-sided die rolled `n` times: probability that the sum lands in `[a, b]`. Equivalent to counting favorable outcomes over `6^n`.

## What to use

| Tool | Why |
|------|-----|
| DP on (throws used, current sum) | Classic dice-sum distribution |
| `ways[i][s]` or rolling array | `n <= 100`, sums `<= 600` |
| Divide by `6^n` | Convert counts to probability |
| Banker's rounding (half to even) | Match the required print format |

## Approach

**Root idea:** after `i` throws the sum distribution is the previous distribution convolved with `{1,2,3,4,5,6}` each with equal weight.

**Mental picture:** a histogram of possible totals. Each new roll smears every bucket into the six buckets `+1..+6`.

**Steps**

1. Let `ways[0][0] = 1` (one way to have sum 0 with 0 throws).
2. For throw `i = 1 .. n`, for each old sum `s`, for face `f = 1..6`:  
   `ways[i][s+f] += ways[i-1][s]`.
3. Favorable = `sum_{s=a..b} ways[n][s]`.
4. Probability = `favorable / 6^n` as a floating value.
5. Print with six decimals using **round half to even** (IEEE banker's rounding). In practice: compute carefully (e.g. scale, or use a round-half-even helper) — naive `printf`/`cout` rounding modes can differ on `.xxxxx5` edges.

You can store probabilities directly (`dp[i][s] += dp[i-1][s-f] / 6.0`) instead of integer ways; same recurrence.

## Walkthrough (on a sample)

`n = 2`, want sums `9` or `10`. Total outcomes `36`.  
Sum 9: `(3,6),(4,5),(5,4),(6,3)` → 4.  
Sum 10: `(4,6),(5,5),(6,4)` → 3.  
`7/36 ≈ 0.194444...` → printed `0.194444`.

## Pitfalls

- Off-by-one in sum bounds (`a..b` inclusive).
- Integer overflow if counting with 32-bit (`6^100` does not fit — use `double` probabilities, or `long double` / big counts only for small `n`). Prefer probability DP in `double` for `n = 100`.
- Wrong rounding mode on half cases.
- Forgetting each face is equally likely.

## Complexity

- Time: `O(n * (6n) * 6) = O(n^2)`  
- Space: `O(n)` with two rolling layers (sums up to `6n`)

## Key takeaway

**Dice-sum probability = convolve the uniform `{1..6}` distribution `n` times** (DP), then sum the `[a,b]` mass and round half-to-even to 6 decimals.
