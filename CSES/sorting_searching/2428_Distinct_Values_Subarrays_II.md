# [2428 — Distinct Values Subarrays II](https://cses.fi/problemset/task/2428)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** two pointers, sliding window, frequency map  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an array of `n` integers, your task is to calculate the number of subarrays that have at most `k` distinct values.

### Input

The first input line has two integers `n` and `k`.

The next line has `n` integers `x1, x2, ..., xn`: the contents of the array.

### Output

Print one integer: the number of subarrays.

### Constraints

- `1 <= k <= n <= 2e5`
- `1 <= xi <= 1e9`

### Examples

**Input**
```
5 2
1 2 3 1 1
```

**Output**
```
10
```

---

## What this problem really is

Count contiguous subarrays whose number of **distinct** elements is `<= k` (not “exactly all distinct”).

## What to use

| Tool | Why |
|------|-----|
| Two pointers `[L,R]` | Longest window ending at `R` with `<= k` distinct values |
| Frequency map | Track how many distinct are in the window |
| Counting trick | If `[L,R]` is maximal good, all `[i,R]` for `i in L..R` are good |

## Approach

**Root idea:** expand `R`; when distinct count exceeds `k`, advance `L` until it’s `<= k` again. Then every start in `L..R` gives a valid subarray ending at `R` — add `(R - L + 1)`.

**Mental picture:** grow a window that may hold up to `k` different colors; when a `(k+1)`-st color appears, slide the left edge until you’re back to `k` colors; count all subarrays sharing that right end.

**Data structures**

- `map` / hash map: value → count in window; `distinct` = number of keys with positive count.
- `long long ans`, pointer `L`.

**Steps**

1. For `R = 0..n-1`:
   - Add `x[R]` (if new, `distinct++`).
   - While `distinct > k`: remove `x[L]`; if its count hits 0, `distinct--`; `L++`.
   - `ans += (R - L + 1)`.
2. Print `ans`.

## Walkthrough (on a sample)

`k=2`, `1 2 3 1 1`. Total subarrays `15`; five of them have 3 distinct (`[1,2,3]`, `[1,2,3,1]`, `[1,2,3,1,1]`, `[2,3,1]`, `[2,3,1,1]`) → `15-5=10`.

## Pitfalls

- Counting “exactly `k`” instead of “at most `k`”.
- Not decreasing `distinct` when a value’s frequency hits 0.
- `int` for answer (`n(n+1)/2`).

## Complexity

- Time: `O(n)` expected with hash / `O(n log n)` with map  
- Space: `O(n)`

## Key takeaway

**At most `k` distinct subarrays = sliding window** — keep `<= k` colors, add `R-L+1` at each right end.
