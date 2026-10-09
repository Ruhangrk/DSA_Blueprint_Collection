# [1096 — Throwing Dice](https://cses.fi/problemset/task/1096)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** matrix exponentiation, linear recurrence, DP  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to calculate the number of ways to get a sum `n` by throwing dice. Each throw yields an integer between `1` and `6`.

For example, if `n = 10`, some possible ways are `3+3+4`, `1+4+1+4` and `1+1+6+1+1`.

### Input

The only input line contains an integer `n`.

### Output

Print the number of ways modulo `1e9+7`.

### Constraints

- `1 <= n <= 1e18`

### Examples

**Input**
```
8
```

**Output**
```
125
```

---

## What this problem really is

Count ordered sequences of numbers in `{1..6}` that sum to `n` (order matters — dice throws are sequential). Same recurrence as classic unbounded dice DP, but `n` is up to `1e18`.

## What to use

| Tool | Why |
|------|-----|
| Recurrence `dp[i] = sum dp[i-1]..dp[i-6]` | Last throw is one of 1..6 |
| `dp[0] = 1` | One way to make sum 0 (throw nothing) |
| 6×6 matrix exponentiation | Jump the linear recurrence to index `n` in `O(log n)` |

## Approach

**Root idea:** ways to make sum `i` = ways that end with a 1, plus end with a 2, …, plus end with a 6 → `dp[i] = dp[i-1] + ... + dp[i-6]` (treat missing indices as 0).

**Mental picture:** same “last throw” split as Dice Combinations-style DP, but you cannot fill an array of length `1e18`. Pack six consecutive values into a state vector and advance it with a matrix.

**Transition matrix** (state = `[dp[k], dp[k-1], ..., dp[k-5]]^T`):

```
[ 1 1 1 1 1 1 ]   // new dp[k+1] = sum of previous six
[ 1 0 0 0 0 0 ]   // shift window
[ 0 1 0 0 0 0 ]
[ 0 0 1 0 0 0 ]
[ 0 0 0 1 0 0 ]
[ 0 0 0 0 1 0 ]
```

Raising this matrix to power `n` (with a suitable initial vector built from `dp[0..5]`) yields `dp[n]`. A common coding pattern: start from identity, multiply by the transition `n` times via binary exponentiation; with the init above, `dp[n]` appears as entry `[0][0]` of `M^n` when `dp[0]=1` is encoded that way.

**Steps**

1. Build the 6×6 companion matrix of the recurrence.
2. Matrix-pow to `n` mod `1e9+7`.
3. Extract `dp[n]`.

For tiny `n` you can verify against a direct `O(n)` DP.

## Walkthrough (on a sample)

`dp[0] = 1`.  
`dp[1]=1`, `2=2`, `3=4`, `4=8`, `5=16`, `6=32`,  
`dp[7] = 32+16+8+4+2+1 = 63`,  
`dp[8] = 63+32+16+8+4+2 = 125` — matches the sample.

## Pitfalls

- Counting unordered partitions (compositions vs partitions) — order matters here.
- Wrong base: forget `dp[0] = 1`.
- Off-by-one in matrix power vs initial vector.
- `O(n)` DP — TLE at `1e18`.

## Complexity

- Time: `O(6^3 log n)`  
- Space: `O(1)` for 6×6 mats

## Key takeaway

**Dice-sum ways = order-6 linear recurrence; for huge `n`, matrix-exponentiate the 6×6 companion.**
