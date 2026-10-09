# [1634 — Minimizing Coins](https://cses.fi/problemset/task/1634)

**Platform:** CSES  
**Source:** CSES Problem Set — Dynamic Programming  
**Tags:** dynamic programming, unbounded knapsack, shortest path on sums  
**Difficulty / Rating:** Easy–Medium (classic coin DP)  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Consider a money system consisting of `n` coins. Each coin has a positive integer value. Your task is to produce a sum of money `x` using the available coins in such a way that the number of coins is minimal.

For example, if the coins are `{1, 5, 7}` and the desired sum is `11`, an optimal solution is `5+5+1` which requires `3` coins.

### Input

The first input line has two integers `n` and `x`: the number of coins and the desired sum of money.

The second line has `n` distinct integers `c1, c2, ..., cn`: the value of each coin.

### Output

Print one integer: the minimum number of coins. If it is not possible to produce the desired sum, print `-1`.

### Constraints

- `1 <= n <= 100`
- `1 <= x <= 1e6`
- `1 <= ci <= 1e6`

### Examples

**Input**
```
3 11
1 5 7
```

**Output**
```
3
```

---

## What this problem really is

Unlimited supply of each coin value. Find the **fewest coins** whose values sum to `x` (or report impossible).

## What to use

| Tool | Why |
|------|-----|
| 1D DP on sum | Optimal coin count for sum `s` comes from smaller sums |
| `INF` sentinel | Mark unreachable sums so they never win a `min` |
| Unbounded style | Same coin type can be used many times |

## Approach

**Root idea:** if you already know the best way to make every smaller sum, then for sum `s` the last coin is some `c`, so best for `s` is `1 + best for (s - c)` — take the minimum over all coins `c <= s`.

Picture climbing to height `x`. Each coin is a allowed step size. `dp[s]` = fewest steps to reach height `s`.

```
dp[0] = 0
dp[s] = min over coins c (if s >= c):  1 + dp[s - c]
```

If no coin works, `dp[s]` stays `INF` (impossible).

**Steps:**

1. Set `dp[0] = 0`, and `dp[1..x] = INF` (e.g. a value `> x`, since you never need more than `x` coins of size `1`).
2. For `s = 1 .. x`, try every coin `c`: if `s >= c` and `dp[s - c]` is not `INF`, update `dp[s] = min(dp[s], dp[s - c] + 1)`.
3. Answer is `dp[x]`, or `-1` if still `INF`.

## Walkthrough (on a sample)

Coins `{1, 5, 7}`, `x = 11`.

| `s` | Best last coin | `dp[s]` | Example |
|-----|----------------|---------|---------|
| 0 | — | 0 | empty |
| 1 | 1 | 1 | `1` |
| 5 | 5 | 1 | `5` |
| 6 | 1 after 5 | 2 | `5+1` |
| 7 | 7 | 1 | `7` |
| 10 | 5 after 5 | 2 | `5+5` |
| 11 | 1 after 10 | 3 | `5+5+1` |

`dp[11] = 3` matches the sample. (Using `7+...` is worse: `7+1+1+1+1` needs 5.)

## Pitfalls

- **Greedy by largest coin fails** on many systems — DP is required in general.
- **Forget impossible** — must output `-1`, not `INF` or `0`.
- **`dp[0]` must be `0`**, not `INF` — otherwise nothing builds.
- **`x = 1e6`, `n = 100`** → `O(n * x)` is the intended bound; slower nested approaches TLE.

## Complexity

- Time: `O(n * x)`  
- Space: `O(x)`

## Key takeaway

**Min coins = shortest path on the number line**, where edges are “subtract one coin value.” Same skeleton as unbounded knapsack with `min` instead of `sum`/`max`.
