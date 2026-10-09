# [1644 — Maximum Subarray Sum II](https://cses.fi/problemset/task/1644)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** prefix sums, sliding window minimum, deque  
**Difficulty / Rating:** Medium–Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an array of `n` integers, your task is to find the maximum sum of values in a contiguous subarray with length between `a` and `b`.

### Input

The first input line has three integers `n`, `a` and `b`: the size of the array and the minimum and maximum subarray length.

The second line has `n` integers `x1, x2, ..., xn`: the array values.

### Output

Print one integer: the maximum subarray sum.

### Constraints

- `1 <= n <= 2e5`
- `1 <= a <= b <= n`
- `-1e9 <= xi <= 1e9`

### Examples

**Input**
```
8 1 2
-1 3 -2 5 3 -5 2 2
```

**Output**
```
8
```

---

## What this problem really is

Maximum subarray sum with a **length constraint**: the chosen subarray length must lie in `[a,b]`. Plain Kadane is not enough.

## What to use

| Tool | Why |
|------|-----|
| Prefix sums | `sum[L..R] = pref[R] - pref[L-1]` |
| Sliding-window minimum on prefixes | For fixed `R`, minimize `pref[L-1]` over valid `L` |
| Monotonic deque | Maintain candidate `L-1` indices in `O(1)` amortized |

## Approach

**Root idea:** for each right endpoint `R`, the subarray must start at some `L` with `R-b+1 <= L <= R-a+1` (and `L >= 1`). Equivalently minimize `pref[j]` over `j` in `[R-b, R-a]` (with `j >= 0`), then candidate sum is `pref[R] - that min`.

**Mental picture:** at each `R`, look back through a sliding band of prefix altitudes and subtract the lowest one in that band.

**Data structures**

- `pref[0]=0`, `pref[i]=x[1]+...+x[i]`.
- Deque of indices `j` with increasing `pref[j]` (candidates for the minimum).

**Steps**

1. Build prefix sums (`long long`).
2. `ans = -INF`.
3. For `R = a..n`:
   - Insert index `j = R-a` into the deque (this `j` just became a valid left-prefix for length-`a` windows ending at `R`). While deque back has `pref >= pref[j]`, pop back; push `j`.
   - Remove front indices `< R-b` (too old for max length `b`).
   - `ans = max(ans, pref[R] - pref[deque.front])`.
4. Print `ans`.

(Order of insert vs remove must keep the window of `j` exactly `[max(0,R-b), R-a]`.)

## Walkthrough (on a sample)

`a=1,b=2`, array `-1 3 -2 5 3 -5 2 2`.

Best length-1 or 2: e.g. `5+3=8` → **8**.

For `R` at the second `3` (index 5): valid `j` in `[3,4]` among prefixes; `pref[5]-pref[3]` gives sum of `[4..5]=5+3=8`.

## Pitfalls

- Forgetting length bounds (plain Kadane).
- Off-by-one on the `j` interval `[R-b, R-a]`.
- Initializing `ans = 0` — subarray is nonempty and may be all negative.
- `int` overflow on prefixes.

## Complexity

- Time: `O(n)`  
- Space: `O(n)`

## Key takeaway

**Max subarray sum with length in `[a,b]` = prefix sums + sliding-window minimum** of `pref[j]` for `j` in `[R-b, R-a]`.
