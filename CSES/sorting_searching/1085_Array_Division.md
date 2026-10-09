# [1085 — Array Division](https://cses.fi/problemset/task/1085)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** binary search on answer, greedy  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array containing `n` positive integers.

Your task is to divide the array into `k` subarrays so that the maximum sum in a subarray is as small as possible.

### Input

The first input line contains two integers `n` and `k`: the size of the array and the number of subarrays in the division.

The next line contains `n` integers `x1, x2, ..., xn`: the contents of the array.

### Output

Print one integer: the maximum sum in a subarray in the optimal division.

### Constraints

- `1 <= n <= 2e5`
- `1 <= k <= n`
- `1 <= xi <= 1e9`

### Examples

**Input**
```
5 3
2 4 7 3 5
```

**Output**
```
8
```

Explanation: An optimal division is `[2,4], [7], [3,5]` where the sums of the subarrays are `6,7,8`. The largest sum is the last sum `8`.

---

## What this problem really is

Split the array into **exactly `k` contiguous pieces**; minimize the **maximum piece sum** (classic “split array largest sum” / book allocation).

## What to use

| Tool | Why |
|------|-----|
| Binary search on the max-sum limit `M` | Feasibility is monotonic |
| Greedy check | Can we cover the array with `<= k` pieces each summing to `<= M`? |
| `long long` | Sums up to `n * 1e9` |

## Approach

**Root idea:** ask “if no piece may exceed sum `M`, do we need at most `k` pieces?” Scan left to right, packing greedily into the current piece; start a new piece when the next element wouldn’t fit. If you need more than `k` pieces, `M` is too small.

**Mental picture:** fill boxes of capacity `M`; binary search the smallest capacity that still needs only `k` boxes.

**Bounds**

- `lo = max(xi)` (one element alone must fit)
- `hi = sum(xi)` (one big piece)

**Steps**

1. Binary search `M` in `[lo, hi]`.
2. `check(M)`: `pieces = 1`, `cur = 0`; for each `v`: if `cur + v > M` then `pieces++`, `cur = v`; else `cur += v`. Return `pieces <= k`.
3. Smallest `M` with `check` true is the answer.

## Walkthrough (on a sample)

`k=3`, array `2 4 7 3 5`. Answer **8**.

- `M=7`: try pack → `[2,4], [7], [3], [5]` needs 4 pieces > 3 — fail.
- `M=8`: `[2,4], [7], [3,5]` — 3 pieces — ok.

## Pitfalls

- Setting `lo = 0` or `lo = 1` — a single `xi > M` makes check impossible.
- Allowing non-contiguous subsets — must be sub**arrays**.
- Using `int` for sums / `M`.

## Complexity

- Time: `O(n log (sum))`  
- Space: `O(n)`

## Key takeaway

**Minimax contiguous split into `k` parts = binary search on max sum + greedy pack check.**
