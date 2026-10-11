# [2229 — Permutation Inversions](https://cses.fi/problemset/task/2229)

**Platform:** CSES  
**Source:** CSES Problem Set — Counting Problems  
**Tags:** DP, inversions, combinatorics  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to count the number of permutations of `1,2,...,n` that have exactly `k` inversions (i.e., pairs of elements in the wrong order).

For example, when `n=4` and `k=3`, there are 6 such permutations:

- `[1,4,3,2]`
- `[2,3,4,1]`
- `[2,4,1,3]`
- `[3,1,4,2]`
- `[3,2,1,4]`
- `[4,1,2,3]`

### Input

The only input line has two integers `n` and `k`.

### Output

Print the answer modulo `1e9+7`.

### Constraints

- `1 <= n <= 500`
- `0 <= k <= n*(n-1)/2`

### Examples

**Input**
```
4 3
```

**Output**
```
6
```

---

## What this problem really is

Mahonian numbers: `T(n,k)` = #perms of `n` with `k` inversions, mod `1e9+7`.

## What to use

| Tool | Why |
|------|-----|
| DP `dp[i][j]` | Ways to place `1..i` with `j` inversions |
| Insert `i` in `i` slots | Adding value `i` into a perm of `i-1` creates between `0` and `i-1` new inversions |
| Prefix sums | Transition in `O(1)` per state |

## Approach

**Root idea:** build the perm by inserting numbers from `1` to `n`. When inserting `i`, choose how many of the already placed (smaller) numbers sit after it — that choice adds exactly that many inversions.

**Mental picture:** grow the sequence; each new largest element can slide left past some neighbors, paying one inversion per neighbor it passes.

**How that becomes the method**

1. `dp[0][0] = 1`.  
2. For `i = 1..n`: for `j = 0..min(k, i*(i-1)/2)`:  
   `dp[i][j] = sum_{t=0..min(i-1,j)} dp[i-1][j-t]`.  
3. Speed with prefix sums of `dp[i-1]`: each `dp[i][j]` is a window sum of length `i`.  
4. Answer `dp[n][k]`.

**Data structures:** rolling `dp` arrays size `k+1`; prefix array; mod arithmetic.

## Walkthrough (on a sample)

`T(4,3) = 6` as listed.

## Pitfalls

- Letting new inversions exceed `i-1`.  
- `O(n^2 k)` without prefix sums may TLE at `n=500`, `k~n^2/2`.  
- Not taking mod.

## Complexity

- Time: `O(n^2 k / n)` → `O(n k)` with prefix windows… actually `O(n * min(k,n^2))` with prefix  
- Space: `O(k)`

## Key takeaway

**#perms with `k` inversions = DP inserting `n` with `0..n-1` new inversions**, prefix-optimized.
