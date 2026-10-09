# [1660 — Subarray Sums I](https://cses.fi/problemset/task/1660)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** two pointers, sliding window, prefix sums  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an array of `n` positive integers, your task is to count the number of subarrays having sum `x`.

### Input

The first input line has two integers `n` and `x`: the size of the array and the target sum `x`.

The next line has `n` integers `a1, a2, ..., an`: the contents of the array.

### Output

Print one integer: the required number of subarrays.

### Constraints

- `1 <= n <= 2e5`
- `1 <= x, ai <= 1e9`

### Examples

**Input**
```
5 7
2 4 1 2 7
```

**Output**
```
3
```

---

## What this problem really is

Count contiguous subarrays with sum exactly `x`. All `ai > 0`, so window sums are monotonic when the right end advances — two pointers work.

## What to use

| Tool | Why |
|------|-----|
| Two pointers `[L,R]` | Positive elements ⇒ expanding `R` only increases sum |
| Running sum | Maintain sum of `[L,R]` in `O(1)` |
| (Alt.) Prefix + map | Also works; needed when negatives appear (see II) |

## Approach

**Root idea:** move `R` from left to right, add `a[R]`. While the window sum exceeds `x`, advance `L`. Whenever sum equals `x`, count one subarray ending at `R` (with positives, at most one `L` gives exact `x` for each `R` in the basic “shrink while > x” pattern — actually after shrinking, check equality once; multiple equal windows need care only with zeros, which don’t appear here).

With strictly positive `ai`, for each `R` there is at most one `L` making sum exactly `x` among windows ending at `R` that you’ve shrunk to `<= x`… Wait: subarrays ending at `R` with sum `x` — with positives there’s at most one such `L`. The algorithm:

```
L = 0, sum = 0, ans = 0
for R in 0..n-1:
  sum += a[R]
  while sum > x: sum -= a[L]; L++
  if sum == x: ans++
```

**Mental picture:** a caterpillar on positive ground — grow the head, pull the tail when too heavy, click whenever the weight hits exactly `x`.

**Steps**

1. Run the two-pointer loop above.
2. Print `ans`.

## Walkthrough (on a sample)

`2 4 1 2 7`, target `7`.

- `[2,4,1]` sum 7 → +1  
- `[4,1,2]` sum 7 → +1  
- `[7]` sum 7 → +1  

Total **3**.

## Pitfalls

- Using this exact two-pointer when negatives exist — wrong (use Subarray Sums II method).
- `long long` for sum (`ai` up to `1e9`, length `2e5`).
- Off-by-one when shrinking.

## Complexity

- Time: `O(n)`  
- Space: `O(1)` extra

## Key takeaway

**Exact subarray sum with positive ints = two pointers** — grow right, shrink left while sum `> x`, count when sum `== x`.
