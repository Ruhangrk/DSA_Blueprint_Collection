# [1112 — Required Substring](https://cses.fi/problemset/task/1112)

**Platform:** CSES  
**Source:** CSES Problem Set — String Algorithms  
**Tags:** KMP, DP on automaton, counting strings  
**Difficulty / Rating:** Medium–Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to calculate the number of strings of length `n` having a given pattern of length `m` as their substring. All strings consist of characters `A`–`Z`.

### Input

The first input line has an integer `n`: the length of the final string.

The second line has a pattern of length `m`.

### Output

Print the number of strings modulo `1e9+7`.

### Constraints

- `1 <= n <= 1000`
- `1 <= m <= 100`

### Examples

**Input**
```
6
ABCDB
```

**Output**
```
52
```

Explanation: The final string will be of the form `ABCDB x` or `x ABCDB` where `x` is any character between `A`–`Z`.

---

## What this problem really is

Count length-`n` strings over alphabet size 26 that contain pattern `P` as a contiguous substring, mod `1e9+7`.

## What to use

| Tool | Why |
|------|-----|
| KMP prefix function of `P` | Automaton of “current matched prefix length” |
| DP `dp[i][j]` | Ways to fill `i` chars with match progress `j` (`j = m` = already found) |
| Absorbing success state | Once `j == m`, any continuation works (`* 26` each step) |

## Approach

**Root idea:** build the string left to right while tracking how much of `P` you currently match. Transitions on the next letter follow KMP failure links. Hitting progress `m` means the pattern has appeared; stay in an accepting sink afterward.

**Mental picture:** a progress bar for `P`. Each new letter moves the bar forward, or snaps it back to a shorter border via KMP. Once the bar fills, it locks.

**Steps**

1. Compute `pi` for `P`.
2. Precompute or on the fly: from state `j < m` and letter `c`, the next state (KMP walk).
3. `dp[0][0] = 1`.
4. For `i = 0 .. n-1`, for each state `j`:
   - If `j == m`: `dp[i+1][m] += dp[i][m] * 26`.
   - Else: for each of 26 letters, go to `nj` and add `dp[i][j]` into `dp[i+1][nj]`.
5. Answer = `dp[n][m]`.

(You can also count all `26^n` minus strings that never complete `P` — same DP without the absorbing success mass.)

## Walkthrough (on a sample)

`n = 6`, `P = ABCDB` (`m = 5`). Pattern must sit in positions `1..5` or `2..6` → `26 + 26 = 52` (the sample’s explanation; overlaps / extra occurrences are already handled correctly by the automaton DP for general cases).

## Pitfalls

- Resetting match progress to `0` on mismatch instead of following `pi`.
- Double-counting or forbidding overlaps incorrectly — the automaton handles them.
- Alphabet size wrong (`a-z` vs `A-Z` — here 26 uppercase).
- Forgetting the absorbing `j == m` state.

## Complexity

- Time: `O(n * m * 26)` (or better with precomputed transitions `O(m*26)`)  
- Space: `O(n * m)` or rolling `O(m)`

## Key takeaway

**Strings containing a pattern = DP on the KMP match automaton** — absorb once progress hits `m`.
