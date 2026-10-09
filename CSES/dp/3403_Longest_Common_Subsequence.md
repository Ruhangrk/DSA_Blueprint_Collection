# [3403 — Longest Common Subsequence](https://cses.fi/problemset/task/3403)

**Platform:** CSES  
**Source:** CSES Problem Set — Dynamic Programming  
**Tags:** dynamic programming, LCS, reconstruction  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given two arrays of integers, find their longest common subsequence.

A subsequence is a sequence of array elements from left to right that can contain gaps. A common subsequence is a subsequence that appears in both arrays.

### Input

The first line has two integers `n` and `m`: the sizes of the arrays.

The second line has `n` integers `a1, a2, ..., an`: the contents of the first array.

The third line has `m` integers `b1, b2, ..., bm`: the contents of the second array.

### Output

First print the length of the longest common subsequence.

After that, print an example of such a sequence. If there are several solutions, you can print any of them.

### Constraints

- `1 <= n, m <= 1000`
- `1 <= ai, bi <= 1e9`

### Examples

**Input**
```
8 6
3 1 3 2 7 4 8 2
6 5 1 2 3 4
```

**Output**
```
3
1 2 4
```

---

## What this problem really is

Find a longest sequence that appears in both arrays in order (not necessarily contiguous). Print its **length** and **one example** subsequence.

## What to use

| Tool | Why |
|------|-----|
| `dp[n+1][m+1]` | `dp[i][j]` = LCS length of `a[1..i]` and `b[1..j]` |
| Parent / traceback | Need an actual sequence, not only the length |
| `O(n*m)` | `n, m <= 1000` → about `1e6` cells |

## Approach

**Root idea:** compare the ends of the two prefixes. If `a[i] == b[j]`, you may append that value to an LCS of the shorter prefixes. If they differ, the LCS of `(i,j)` is the better of skipping `a[i]` or skipping `b[j]`.

**Mental picture:** a grid. Moving **diagonal** means “take this matching pair”; moving **left** or **up** means “skip one side.” An LCS path is a route from `(n,m)` back to `(0,0)` that picks diagonals whenever they improve the length.

**Data structures**

- Arrays `a[1..n]`, `b[1..m]`.
- `dp[0..n][0..m]` for lengths (`dp[0][*] = dp[*][0] = 0`).
- Optional: store moves, or recompute during traceback by comparing neighbors.

**Length recurrence**

```
if a[i] == b[j]:
    dp[i][j] = dp[i-1][j-1] + 1
else:
    dp[i][j] = max(dp[i-1][j], dp[i][j-1])
```

**Reconstruction**

1. Start at `(i,j) = (n,m)`, empty list.
2. While `i > 0` and `j > 0`:
   - If `a[i] == b[j]` and `dp[i][j] == dp[i-1][j-1] + 1`: append `a[i]`, then `i--, j--`.
   - Else if `dp[i-1][j] >= dp[i][j-1]`: `i--` (came from above).
   - Else: `j--` (came from left).
3. Reverse the collected values.
4. Print `dp[n][m]`, then the values.

**Steps**

1. Build `dp` bottom-up.
2. Traceback one path.
3. Output length and the sequence.

## Walkthrough (on a sample)

`a = [3,1,3,2,7,4,8,2]`, `b = [6,5,1,2,3,4]`.

One LCS is `1,2,4` (length `3`):

- `1` appears in both  
- then `2` later in both  
- then `4` later in both  

In the DP grid, traceback from `(8,6)` eventually picks those three diagonal matches (among other possible LCS of length `3`). Any valid length-`3` sequence is accepted.

## Pitfalls

- **Substring vs subsequence** — gaps are allowed; do not require contiguous blocks.
- **Only printing length** — this problem also needs the actual values.
- **Traceback without checking equality** — only take a diagonal when the characters match and the scores agree.
- Values up to `1e9` — compare by equality only; no value-indexed arrays.

## Complexity

- Time: `O(n * m)`  
- Space: `O(n * m)` for DP (needed for easy traceback at this size)

## Key takeaway

**LCS length is a classic grid DP; the sequence is a path.** Match → diagonal `+1`; mismatch → take `max` from skip-left / skip-up, then walk back to recover one answer.
