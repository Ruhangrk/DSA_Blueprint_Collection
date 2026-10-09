# [1636 — Coin Combinations II](https://cses.fi/problemset/task/1636)

**Platform:** CSES  
**Source:** CSES Problem Set — Dynamic Programming  
**Tags:** dynamic programming, counting, combinations  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Consider a money system consisting of `n` coins. Each coin has a positive integer value. Your task is to calculate the number of distinct ordered ways you can produce a money sum `x` using the available coins.

For example, if the coins are `{2, 3, 5}` and the desired sum is `9`, there are `3` ways:

- `2+2+5`
- `3+3+3`
- `2+2+2+3`

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
3
```

---

## What this problem really is

Count **unordered** multisets of coins that sum to `x` (unlimited supply of each value).  
`2+5+2` and `5+2+2` are the **same** way — only which values you use (and how many of each) matters, not the order you write them.  

(CSES wording says “ordered,” but the sample lists only `3` ways, not every permutation of those bags. Treat it as **combinations**.) Answer modulo `1e9+7`.

## What to use

| Tool | Why |
|------|-----|
| 1D ways DP `dp[s]` | `dp[s]` = number of combinations that make sum `s` |
| Outer loop on coin types | Each combination is built in one fixed coin order, so permutations are not double-counted |
| Modulo `1e9+7` | Counts grow huge |

## Approach

**Root idea:** if order does not matter, invent each multiset **exactly once**. Do that by processing coin types one-by-one: when you handle coin `c`, you only add `c` onto amounts already formed from **earlier coin types** (and any copies of `c` itself).

Picture the coins in a fixed list order, say `2`, then `3`, then `5`. Every valid bag is assembled by deciding “how many `2`s?”, then “how many `3`s?”, then “how many `5`s?”. DP walks that same order:

```
dp[0] = 1
for each coin c:
    for s = c .. x:
        dp[s] += dp[s - c]   (mod 1e9+7)
```

- `dp[0] = 1`: one empty combination.
- Inner loop on `s` lets you use coin `c` many times.
- Outer loop on coins is what makes the count **unordered**.

If you swapped the loops (sum outer, coins inner), you would count **different write-orders** as different answers — wrong for this problem’s sample.

**Steps:**

1. `dp[0] = 1`, rest `0`.
2. For each coin `c`, for `s = c .. x`: `dp[s] = (dp[s] + dp[s - c]) % MOD`.
3. Answer `dp[x]`.

## Walkthrough (on a sample)

Coins `{2, 3, 5}`, `x = 9`.

**After only coin `2`:** only multiples of `2` — `dp[2]=1`, `dp[4]=1`, `dp[6]=1`, `dp[8]=1`, …

**Then coin `3`:** mix `2`s and `3`s. Now `9` can be reached as `3+3+3` and as `2+2+2+3`.

**Then coin `5`:** also `2+2+5`.

Final bags for `9`: `{2,2,5}`, `{3,3,3}`, `{2,2,2,3}` → **3**.

Note `2+5+2` is **not** a fourth answer — it is the same bag as `{2,2,5}`.

## Pitfalls

- **Wrong loop order** (for `s`, then for each coin): counts permutations; sample would look like `8`, not `3`.
- **`dp[0] = 0`** → everything stays `0`.
- **Coins `> x`** — skip; never index `s - c < 0`.
- **Modulo** on every addition.

## Complexity

- Time: `O(n * x)`  
- Space: `O(x)`

## Key takeaway

**Unordered coin combinations:** loop **coins outside**, sums inside. That forces one canonical build order per multiset so permutations are not counted twice.
