# [3219 — Sliding Window Mex](https://cses.fi/problemset/task/3219)

**Platform:** CSES  
**Source:** CSES Problem Set — Sliding Window Problems  
**Tags:** sliding window, mex, frequency  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array of `n` integers. Your task is to calculate the mex of each window of `k` elements, from left to right.

The mex is the smallest nonnegative integer that does not appear in the array. For example, the mex for `[3,1,4,3,0,5]` is `2`.

### Input

The first line contains two integers `n` and `k`: the number of elements and the size of the window.

Then there are `n` integers `x1, x2, ..., xn`: the contents of the array.

### Output

Print `n-k+1` values: the mex values.

### Constraints

- `1 <= k <= n <= 2e5`
- `0 <= xi <= 1e9`

### Examples

**Input**
```
8 3
1 2 1 0 5 1 1 0
```

**Output**
```
0 3 2 2 0 2
```

---

## What this problem really is

Mex of every length-`k` window. Values can be huge, but mex of a `k`-window is always in `{0,1,...,k}` (at most `k` distinct nonnegative ints fit, so some value `<=k` is missing).

## What to use

| Tool | Why |
|------|-----|
| Freq only for values `<= k` | Larger values never affect mex |
| Count of “present” in `0..k` | Mex = smallest with freq `0` |
| Set of missing / walk pointer | Fast find next gap after updates |

## Approach

**Root idea:** clamp attention to `0..k`. Maintain frequencies of those values in the window; mex is the minimal index with frequency `0`.

**Mental picture:** a row of slots `0,1,2,...,k`. Each window fills some slots. The leftmost empty slot is the mex.

**How that becomes the method**

1. `freq[0..k]` array (size `k+2`).
2. On add `v`: if `v <= k`, `freq[v]++`.
3. On rem `v`: if `v <= k`, `freq[v]--`.
4. After each slide, scan from `0` upward for first `freq[i]==0` (or keep a set of zeros / start from previous mex and adjust — scanning `O(k)` once per window is `O(nk)` too slow; instead maintain `set` of currently-zero indices in `0..k`, or only rescan from 0 but amortize carefully).

Efficient: store `set<int> zeros` initially `{0,1,...,k}`. When `freq[v]` goes `0→1`, erase `v` from `zeros`; when `1→0`, insert. Mex = `*zeros.begin()`.

**Data structures:** `freq[k+2]`; `set` of missing values `0..k`.

## Walkthrough (on a sample)

`[1,2,1]` misses `0` → mex `0`. `[2,1,0]` has `0,1,2` → mex `3`. And so on.

## Pitfalls

- Considering mex could be `>k` (impossible for window size `k`).  
- Tracking huge `xi` in a map unnecessarily for mex.  
- `O(nk)` linear scans every window.

## Complexity

- Time: `O(n log k)` with the zero-set  
- Space: `O(k)`

## Key takeaway

**Window mex = track presence of `0..k` only**; maintain the set of missing small integers.
