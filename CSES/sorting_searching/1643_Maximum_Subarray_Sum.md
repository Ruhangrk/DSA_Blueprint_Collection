# [1643 — Maximum Subarray Sum](https://cses.fi/problemset/task/1643)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** Kadane, prefix minima, dynamic programming  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an array of `n` integers, your task is to find the maximum sum of values in a contiguous, nonempty subarray.

### Input

The first input line has an integer `n`: the size of the array.

The second line has `n` integers `x1, x2, ..., xn`: the array values.

### Output

Print one integer: the maximum subarray sum.

### Constraints

- `1 <= n <= 2e5`
- `-1e9 <= xi <= 1e9`

### Examples

**Input**
```
8
-1 3 -2 5 3 -5 2 2
```

**Output**
```
9
```

---

## What this problem really is

Classic **maximum nonempty subarray sum** (Kadane). Negatives allowed; empty subarray is **not** allowed.

## What to use

| Tool | Why |
|------|-----|
| Kadane (`best_ending_here`) | One left-to-right pass, `O(n)` |
| Or prefix sums + min prefix so far | `sum[l..r] = pref[r] - pref[l-1]`; maximize by minimizing `pref[l-1]` |
| `long long` | Partial sums can be large in magnitude |

## Approach

**Root idea:** the best subarray ending at index `i` is either just `x[i]`, or `x[i]` glued onto the best subarray ending at `i-1` — but only if that previous best is positive. Track the global max of those.

**Mental picture:** a running “streak sum”; if the streak goes negative, drop it and start fresh at the next element. Remember the highest streak you’ve seen.

**Data structures**

- Scalars: `cur` (best sum ending here), `ans` (global best).
- No arrays beyond the input needed.

**Steps (Kadane)**

1. `cur = ans = x[1]` (1-based) / `x[0]` (0-based).
2. For each later `v`: `cur = max(v, cur + v)`; `ans = max(ans, cur)`.
3. Print `ans`.

**Prefix view:** `pref[0]=0`, `pref[i]=x[1]+...+x[i]`. For each `r`, best is `pref[r] - min(pref[0..r-1])`.

## Walkthrough (on a sample)

Array: `-1, 3, -2, 5, 3, -5, 2, 2`.

- After `3`: cur `3`
- `-2`: cur `1`
- `5`: cur `6`
- `3`: cur `9` → ans **9** (`3-2+5+3`)
- later dips don’t beat `9`

## Pitfalls

- Initializing `ans = 0` — wrong when all values are negative (must pick the largest single element).
- Allowing empty subarray (`max(0, …)`) — not this problem.
- `int` overflow on sums.

## Complexity

- Time: `O(n)`  
- Space: `O(1)` extra

## Key takeaway

**Max nonempty subarray sum = Kadane** — `cur = max(v, cur+v)`, track the global max; init from the first element, not `0`.
