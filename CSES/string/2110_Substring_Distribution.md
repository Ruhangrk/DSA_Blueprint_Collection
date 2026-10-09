# [2110 — Substring Distribution](https://cses.fi/problemset/task/2110)

**Platform:** CSES  
**Source:** CSES Problem Set — String Algorithms  
**Tags:** suffix array, LCP, difference array  
**Difficulty / Rating:** Medium–Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a string of length `n`. For every integer between `1 ... n` you need to print the number of distinct substrings of that length.

### Input

The only input line has a string of length `n` that consists of characters `a`–`z`.

### Output

For each integer between `1 ... n` print the number of distinct substrings of that length.

### Constraints

- `1 <= n <= 1e5`

### Examples

**Input**
```
abab
```

**Output**
```
2 2 2 1
```

---

## What this problem really is

For each length `L`, count how many unique substrings of length exactly `L` appear in `s`.

## What to use

| Tool | Why |
|------|-----|
| Suffix array + LCP | Each suffix contributes a contiguous band of new lengths |
| Difference array | Add `+1` on `[lcp+1, suffix_len]` in `O(1)` per suffix |
| Prefix sums | Turn difference array into answers for all `L` |

## Approach

**Root idea:** suffix `sa[i]` owns the new distinct prefixes of lengths `lcp[i]+1, lcp[i]+2, ..., n-sa[i]`. Every distinct substring is such a prefix for exactly one (the lexicographically first) suffix that contains it.

**Mental picture:** same SA walk as counting all distinct substrings, but instead of summing how many, histogram them by length.

**Steps**

1. Build `sa` and `lcp`.
2. `diff[0..n+1] = 0`.
3. For each `i`, let `L = lcp_with_previous + 1`, `R = n - sa[i]`.  
   If `L <= R`: `diff[L]++`, `diff[R+1]--`.
4. `ans = 0`; for `len = 1 .. n`: `ans += diff[len]`; print `ans`.

**SAM view:** state `v` contributes `1` to each length in `(len[link[v]], len[v]]` — same difference-array idea over states.

## Walkthrough (on a sample)

`abab`:

- length 1: `a`,`b` → 2  
- length 2: `ab`,`ba` → 2  
- length 3: `aba`,`bab` → 2  
- length 4: `abab` → 1  

## Pitfalls

- Counting all (non-distinct) substrings of length `L` → always `n-L+1`.
- Updating one length at a time in `O(n)` per suffix → `O(n^2)`.
- Wrong `lcp` pairing with `sa[i]`.

## Complexity

- Time: `O(n log n)` SA + `O(n)` difference  
- Space: `O(n)`

## Key takeaway

**Distinct substrings by length = difference-array over SA fresh ranges `[lcp+1, suffix_len]`.**
