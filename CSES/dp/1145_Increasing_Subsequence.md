# [1145 — Increasing Subsequence](https://cses.fi/problemset/task/1145)

**Platform:** CSES  
**Source:** CSES Problem Set — Dynamic Programming  
**Tags:** dynamic programming, LIS, binary search  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array containing `n` integers. Your task is to determine the longest increasing subsequence in the array, i.e., the longest subsequence where every element is larger than the previous one.

A subsequence is a sequence that can be derived from the array by deleting some elements without changing the order of the remaining elements.

### Input

The first line contains an integer `n`: the size of the array.

After this there are `n` integers `x1, x2, ..., xn`: the contents of the array.

### Output

Print the length of the longest increasing subsequence.

### Constraints

- `1 <= n <= 2e5`
- `1 <= xi <= 1e9`

### Examples

**Input**
```
8
7 3 5 3 6 2 9 8
```

**Output**
```
4
```

---

## What this problem really is

Longest **strictly** increasing subsequence (LIS) length in an array of size up to `2e5` — need better than `O(n^2)`.

## What to use

| Tool | Why |
|------|-----|
| Patience / tails array `t` | `t[len]` = smallest tail value of any increasing subsequence of length `len` |
| Binary search | Place each `x` into `t` in `O(log n)` |
| Length of `t` | Equals the LIS length |

## Approach

**Root idea:** among all increasing subsequences of the same length, you only need the one with the **smallest ending value** — it is easiest to extend later. Maintain those best tails.

**Mental picture:** piles (patience sorting). Each new number `x` either starts a longer subsequence (new pile) or replaces the first pile-top that is `>= x`, keeping tops minimal.

**Data structures**

- Vector `t` (initially empty). Conceptually 1-based lengths; in C++ store as 0-based vector where `t[k]` = best tail for length `k+1`.

**For each value `x` in the array**

1. Binary-search the first position in `t` with `t[pos] >= x` (lower_bound for strict increase).
2. If `pos == t.size()`, push `x` (LIS grows by 1).
3. Else replace `t[pos] = x` (same length, better/smaller tail).

**Answer:** `t.size()`.

**Why strict:** using `lower_bound` on `>= x` means equal values cannot extend; they only replace. That matches **strictly** increasing.

**Steps**

1. Read `n` and the array.
2. Process each `x` with lower_bound updates on `t`.
3. Print `t.size()`.

## Walkthrough (on a sample)

`7 3 5 3 6 2 9 8`

| `x` | `t` after | note |
|-----|-----------|------|
| 7 | `[7]` | length 1 |
| 3 | `[3]` | better tail for len 1 |
| 5 | `[3,5]` | extend |
| 3 | `[3,5]` | `3` replaces first |
| 6 | `[3,5,6]` | extend |
| 2 | `[2,5,6]` | better len-1 tail |
| 9 | `[2,5,6,9]` | extend → length **4** |
| 8 | `[2,5,6,8]` | better len-4 tail |

LIS length **4** (e.g. `3,5,6,9` or `3,5,6,8`).

## Pitfalls

- **`n = 2e5`** — classic `O(n^2)` `dp[i] = 1 + max dp[j]` TLEs.
- **Strict vs non-decreasing** — for strict use `lower_bound`; for non-decreasing you’d use `upper_bound`.
- **`t` is not itself an LIS** — only the **length** is correct; tails are a compressed structure.
- Values up to `1e9` — fine; compare values directly.

## Complexity

- Time: `O(n log n)`  
- Space: `O(n)` for `t` in the worst case

## Key takeaway

**LIS in `O(n log n)`:** keep the smallest possible tail for every length; binary-search where each new value goes. Answer = number of tails.
