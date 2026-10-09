# [1093 — Two Sets II](https://cses.fi/problemset/task/1093)

**Platform:** CSES  
**Source:** CSES Problem Set — Dynamic Programming  
**Tags:** dynamic programming, subset sum, counting  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to count the number of ways numbers `1, 2, ..., n` can be divided into two sets of equal sum.

For example, if `n = 7`, there are four solutions:

- `{1,3,4,6}` and `{2,5,7}`
- `{1,2,5,6}` and `{3,4,7}`
- `{1,2,4,7}` and `{3,5,6}`
- `{1,6,7}` and `{2,3,4,5}`

### Input

The only input line contains an integer `n`.

### Output

Print the answer modulo `1e9+7`.

### Constraints

- `1 <= n <= 500`

### Examples

**Input**
```
7
```

**Output**
```
4
```

---

## What this problem really is

Partition `{1, 2, ..., n}` into two subsets with the **same sum**. Count unordered partitions (swapping the two sets does not create a new way). Modulo `1e9+7`.

## What to use

| Tool | Why |
|------|-----|
| Subset-sum counting DP | Number of subsets with sum `T` |
| Target `T = n(n+1)/4` | Equal halves only if total sum is even |
| Divide by 2 at the end | Each partition counted twice (once per which set you built) |

## Approach

**Root idea:** total sum is `S = n(n+1)/2`. Equal sets exist only if `S` is even; each set must sum to `T = S/2`. Count subsets of `{1..n}` with sum `T`, then divide by `2` because choosing set A vs its complement is the same partition.

**Mental picture:** fill a knapsack with numbers `1..n`, each at most once. `dp[s]` = ways to make sum `s`. You care about `dp[T] / 2`.

**Data structures**

- `dp[0..T]` of modular integers (`long long` or int mod `1e9+7`).
- Init `dp[0] = 1`.

**Update:** for `x = 1 .. n`, for `s = T` down to `x`:

```
dp[s] = (dp[s] + dp[s - x]) % MOD
```

**Answer**

1. If `S` is odd → `0`.
2. Else `(dp[T] * inv2) % MOD` where `inv2` is modular inverse of `2` (or since `MOD` is prime, `dp[T] * (MOD+1)/2` carefully — better: multiply by `500000004`, or compute `dp[T]/2` only when you know it’s even in integers before mod — safest is modular inverse of 2).

**Steps**

1. Compute `S`. If odd, print `0`.
2. `T = S/2`, run 0/1 counting DP with items `1..n`.
3. Print `dp[T] / 2` modulo `MOD`.

## Walkthrough (on a sample)

`n = 7`, `S = 28`, `T = 14`.

Subsets summing to `14` include `{1,3,4,6}`, `{1,2,5,6}`, `{1,2,4,7}`, `{1,6,7}`, and also each of their complements (same partitions). The DP finds `8` subset ways → divide by `2` → **`4`** partitions, matching the statement.

## Pitfalls

- **Forgetting `/2`** — double-counts every partition.
- **Odd `S`** — impossible, answer `0` (e.g. `n = 3`, sum `6` is even; `n = 1` sum `1` odd → `0`).
- **Including empty/full incorrectly** — for `T > 0`, empty set is not a solution; DP still fine because `T = S/2 > 0` when `n >= 1` and `S` even implies positive `T` for `n >= 3`.
- Modulo before dividing — use inverse of 2, don’t do integer `/` after `%`.

## Complexity

- Time: `O(n * T)` with `T = O(n^2)` → `O(n^3)` but `n <= 500` so about `500 * 500^2 / 4` ≈ `3e7`, OK  
- Space: `O(T)`

## Key takeaway

**Equal partition count = subset-sum ways to `total/2`, then divide by 2.** Same 0/1 knapsack counting skeleton as coin/subset problems.
