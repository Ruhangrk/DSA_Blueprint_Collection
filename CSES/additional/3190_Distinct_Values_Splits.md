# [3190 — Distinct Values Splits](https://cses.fi/problemset/task/3190)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems I  
**Tags:** DP, two pointers, prefix sums  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array of `n` integers. Your task is to count the number of ways to split the array into continuous segments such that all segments consist of distinct values.

### Input

The first line has an integer `n`: the size of the array.

The next line has `n` integers `x1, x2, ..., xn`: the contents of the array.

### Output

Print one integer: the answer to the problem modulo `1e9+7`.

### Constraints

- `1 <= n <= 2e5`
- `1 <= xi <= 1e9`

### Examples

**Input**
```
4
1 2 1 3
```

**Output**
```
6
```

Explanation: six valid splits are listed in the statement (segments may be longer when values stay unique).

---

## What this problem really is

Count ordered partitions of the array into contiguous pieces where each piece has all-unique elements (mod `1e9+7`).

## What to use

| Tool | Why |
|------|-----|
| DP `dp[i]` | Ways to split the prefix of length `i` |
| Two pointers | Largest window ending at `i` with distinct values |
| Prefix sums of `dp` | Range-sum transitions in `O(1)` |

## Approach

**Root idea:** after fixing the end of the last segment at `i`, that segment must be `a[j+1..i]` with all distinct values, and the prefix `1..j` must be a valid split. Sum over all legal `j`.

**Mental picture:** grow a right endpoint. Maintain the leftmost index `L` so that `a[L..i]` still has unique values (slide `L` right when a duplicate enters). Then the last segment can start anywhere in `L..i`, i.e. previous split end `j` ranges from `L-1` to `i-1`.

**How that becomes the method**

1. `dp[0] = 1`; `pref[0] = 1` (`pref[i] = dp[0]+...+dp[i]`).
2. Maintain `last[value]` last index; two-pointer `left = 1` (1-based start of distinct window).
3. For `i = 1..n`:
   - If `x[i]` occurred at `p >= left`, set `left = p + 1`.
   - Update `last[x[i]] = i`.
   - Legal previous ends `j` are `left-1 .. i-1` (0-based / 1-based consistently).  
     `dp[i] = pref[i-1] - pref[left-2]` (handle `left=1` → subtract `0`), mod `1e9+7`.
   - `pref[i] = pref[i-1] + dp[i]`.
4. Answer `dp[n]`.

**Data structures:** `dp[n+1]`, `pref[n+1]`; `map`/`hash` for last positions; `MOD = 1e9+7`.

## Walkthrough (on a sample)

`[1,2,1,3]`:

- `i=1`: window `[1]`, `dp=1`
- `i=2`: window `[1,2]`, `dp = dp0+dp1 = 2`
- `i=3`: value `1` forces `left` after first `1` → window starts at `2`; `dp = dp1+dp2`
- `i=4`: extend; sum gives total `6`.

## Pitfalls

- Allowing duplicates inside a segment.
- Forgetting modulo when subtracting prefix sums (add `MOD` before `%`).
- Off-by-one on the two-pointer `left`.

## Complexity

- Time: `O(n)`
- Space: `O(n)`

## Key takeaway

Splits into distinct-value segments → `dp[i] =` sum of `dp` over the sliding window of valid last-segment starts, via prefix sums.
