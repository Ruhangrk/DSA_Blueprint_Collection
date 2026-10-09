# [3420 — Distinct Values Subarrays](https://cses.fi/problemset/task/3420)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** two pointers, sliding window, counting  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an array of `n` integers, count the number of subarrays where each element is distinct.

### Input

The first line has an integer `n`: the array size.

The second line has `n` integers `x1, x2, ..., xn`: the array contents.

### Output

Print the number of subarrays with distinct elements.

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
8
```

Explanation: The subarrays are `[1]` (two times), `[2]`, `[3]`, `[1,2]`, `[1,3]`, `[2,1]` and `[2,1,3]`.

---

## What this problem really is

Count contiguous subarrays with **all unique** elements (same window idea as longest unique segment, but sum counts instead of max length).

## What to use

| Tool | Why |
|------|-----|
| Two pointers `[L,R]` | Largest valid window ending at `R` |
| Frequency map / set | Know when a duplicate appears |
| Counting trick | If `[L,R]` is the longest good window ending at `R`, then all `[i,R]` for `i in L..R` are good |

## Approach

**Root idea:** expand `R` from left to right, shrink `L` until `[L,R]` has distinct values. Every subarray ending at `R` that starts at `L..R` is valid — add `(R - L + 1)` to the answer.

**Mental picture:** for each right endpoint, how far left can you go without repeating a song? That many subarrays end here.

**Data structures**

- `map` / `unordered_map` counts (or last index), pointer `L`.
- `long long ans`.

**Steps**

1. `L = 0`. For `R = 0..n-1`:
   - Add `x[R]` into the window (increment freq).
   - While freq of `x[R] > 1`: remove `x[L]`, `L++`.
   - `ans += (R - L + 1)`.
2. Print `ans`.

## Walkthrough (on a sample)

`1 2 1 3`

- `R=0`: window `[1]` → +1  
- `R=1`: `[1,2]` → +2 (total 3)  
- `R=2`: duplicate `1` → shrink to `[2,1]` → +2 (total 5)  
- `R=3`: `[2,1,3]` → +3 (total **8**)

## Pitfalls

- Adding `1` per window instead of `(R-L+1)`.
- Counting subsequences (non-contiguous) — wrong problem.
- `int` overflow: up to `n(n+1)/2` ≈ `2e5^2 / 2`.
- Not shrinking until the duplicate is gone.

## Complexity

- Time: `O(n)` expected with hash / `O(n log n)` with map  
- Space: `O(n)`

## Key takeaway

**Count distinct-element subarrays = sliding window**; for each `R`, add the number of valid left starts `R-L+1`.
