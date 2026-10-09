# [1635 — Coin Combinations I](https://cses.fi/problemset/task/1635)

**Platform:** CSES  
**Source:** CSES Problem Set — Dynamic Programming  
**Tags:** dynamic programming, counting, permutations of coins  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Consider a money system consisting of `n` coins. Each coin has a positive integer value. Your task is to calculate the number of distinct ways you can produce a money sum `x` using the available coins.

For example, if the coins are `{2, 3, 5}` and the desired sum is `9`, there are `8` ways:

- `2+2+5`
- `2+5+2`
- `5+2+2`
- `3+3+3`
- `2+2+2+3`
- `2+2+3+2`
- `2+3+2+2`
- `3+2+2+2`

### Input

The first input line has two integers `n` and `x`: the number of coins and the desired sum of money.

The second line has `n` distinct integers `c1, c2, ..., cn`: the value of each coin.

### Output

Print one integer: the number of ways modulo `1e9+7`.

### Constraints

- `1 <= n <= 100`
- `1 <= x <= 1e6`
- `1 <= ci <= 1e6`

### Examples

**Input**
```
3 9
2 3 5
```

**Output**
```
8
```

---

## What this problem really is

Count **ordered** sequences of coin values that sum to `x` (unlimited supply of each value).  
`2+5+2` and `5+2+2` are **different** ways. Answer modulo `1e9+7`.

## What to use

| Tool | Why |
|------|-----|
| 1D ways DP `dp[s]` | `dp[s]` = number of ordered sequences that sum to `s` |
| Last-coin split | Every sequence ends with some coin `c`; add up ways to make `s - c` |
| Outer loop on sum | Builds sequences left-to-right so different orders stay distinct |
| Modulo `1e9+7` | Counts explode |

## Approach

**Root idea:** every valid sequence that sums to `s` has a **last coin** `c`. The prefix must sum to `s - c`. So:

```
dp[0] = 1
dp[s] = sum over coins c (s >= c):  dp[s - c]   (mod 1e9+7)
```

Picture a line of length `s`. The last coin covers the final `c` units; the prefix of length `s - c` can be filled in `dp[s - c]` ordered ways.

**Why `dp[0] = 1`:** one empty sequence — the base you attach the first coin onto.

**Loop order:**

1. Set `dp[0] = 1`, rest `0`.
2. For `s = 1 .. x`:
   - For each coin `c`: if `s >= c`, add `dp[s - c]` into `dp[s]` (mod).
3. Answer `dp[x]`.

Outer = sum, inner = coins → you choose the **last** coin for each sum, so write-orders are counted separately.

If you put coins in the outer loop instead, different orders of the same multiset collapse into one count — wrong for **this** problem (the sample would become `3`, not `8`).

## Walkthrough (on a sample)

Coins `{2, 3, 5}`, target `9`.

| `s` | Last coin options | `dp[s]` (idea) |
|-----|-------------------|----------------|
| 0 | — | 1 |
| 2 | last `2` from 0 | 1 → `(2)` |
| 3 | last `3` from 0 | 1 → `(3)` |
| 4 | last `2` from 2 | 1 → `(2,2)` |
| 5 | last `2`/`3`/`5` from 3, 2, 0 | 3 |
| … | … | … |
| 9 | last `2`, `3`, or `5` from 7, 6, 4 | **8** |

Those eight values are exactly the eight sequences in the statement — including all permutations of `{2,2,5}` and of `{2,2,2,3}`.

## Pitfalls

- **Wrong loop order** (coins outer, sum inner) → counts unordered bags; sample becomes `3` instead of `8`.
- **`dp[0] = 0`** → all zeros.
- **Coins larger than `x`** — skip; don’t index negative.
- **Modulo** on every add.

## Complexity

- Time: `O(n * x)`  
- Space: `O(x)`

## Key takeaway

**Ordered coin ways:** for each sum `s`, split on the **last coin** and add. Outer loop on the sum keeps permutations distinct.
