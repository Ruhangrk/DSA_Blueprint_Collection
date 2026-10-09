# [1633 — Dice Combinations](https://cses.fi/problemset/task/1633)

**Platform:** CSES  
**Source:** CSES Problem Set — Dynamic Programming  
**Tags:** dynamic programming, counting, modulo arithmetic  
**Difficulty / Rating:** Easy (introductory DP)  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to count the number of ways to construct sum `n` by throwing a dice one or more times. Each throw produces an outcome between `1` and `6`.

For example, if `n = 3`, there are `4` ways:

- `1+1+1`
- `1+2`
- `2+1`
- `3`

### Input

The only input line has an integer `n`.

### Output

Print the number of ways modulo `1e9+7`.

### Constraints

- `1 <= n <= 1e6`

### Examples

**Input**
```
3
```

**Output**
```
4
```

---

## What this problem really is

Count **ordered** ways to write `n` as a sum of numbers from `{1, 2, 3, 4, 5, 6}`.  
`1+2` and `2+1` are different sequences (different throw orders). Answer modulo `1e9+7`.

## What to use

| Tool | Why |
|------|-----|
| 1D DP | Each sum `s` is built from a few smaller sums — store answers bottom-up |
| Last-throw split | Only 6 choices for the final face → 6 previous states to add |
| Modulo `1e9+7` | Number of sequences grows huge; problem requires it |

## Approach

**Root idea:** every valid sequence that sums to `s` has a **last throw**. That last face is some `f` in `{1..6}`, and everything before it must sum to exactly `s - f`.

Picture the target sum as a line of length `s`. The last die covers the final `f` units; the prefix of length `s - f` can be filled in `dp[s - f]` ways. So:

```
dp[s] = dp[s-1] + dp[s-2] + dp[s-3] + dp[s-4] + dp[s-5] + dp[s-6]
```

(ignore any term where `s - f < 0`).

**Why `dp[0] = 1`:** there is one way to make sum `0` — throw nothing. That empty prefix is the base you attach the first face onto (e.g. a single throw of `3` is “empty prefix + face `3`”).

**Steps:**

1. Set `dp[0] = 1`.
2. For `s = 1 .. n`, set `dp[s]` to the sum above, modulo `1e9+7`.
3. Answer is `dp[n]`.

## Walkthrough (on a sample)

`n = 3`. Build small sums first so larger ones can reuse them.

| `s` | Last face | Comes from | Sequences | `dp[s]` |
|-----|-----------|------------|-----------|---------|
| 0 | — | — | `(empty)` | 1 |
| 1 | 1 | `dp[0]` | `(1)` | 1 |
| 2 | 1 or 2 | `dp[1] + dp[0]` | `(1,1)`, `(2)` | 2 |
| 3 | 1, 2, or 3 | `dp[2] + dp[1] + dp[0]` | `(1,1,1)`, `(2,1)`, `(1,2)`, `(3)` | `2+1+1 = 4` |

For `s = 3`, the four sequences are exactly “any way to make `2`, then throw `1`” + “any way to make `1`, then throw `2`” + “any way to make `0`, then throw `3`”. That is the recurrence in one picture.

## Pitfalls

- **Order matters** — do not treat this like unordered partitions (do not “dedupe” `1+2` and `2+1`).
- **Wrong base** — if `dp[0] = 0`, every later value stays `0`.
- **Modulo late** — reduce after each addition; waiting until the end overflows for large `n`.
- **Too slow** — `n = 1e6` needs `O(n)` (six adds per `s`); naive recursion without memo TLEs.

## Complexity

- Time: `O(n)`  
- Space: `O(n)`, or `O(1)` if you keep only the last 6 values

## Key takeaway

**Split on the last choice.** Ways to reach `s` = sum of ways to reach each legal previous state. Here the last die face is that choice.
