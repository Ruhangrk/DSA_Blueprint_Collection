# [1628 — Meet in the Middle](https://cses.fi/problemset/task/1628)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Techniques  
**Tags:** meet in the middle, subset sum, enumeration  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array of `n` numbers. In how many ways can you choose a subset of the numbers with sum `x`?

### Input

The first input line has two numbers `n` and `x`: the array size and the required sum.

The second line has `n` integers `t1, t2, ..., tn`: the numbers in the array.

### Output

Print the number of ways you can create the sum `x`.

### Constraints

- `1 <= n <= 40`
- `1 <= x <= 1e9`
- `1 <= ti <= 1e9`

### Examples

**Input**
```
4 5
1 2 3 2
```

**Output**
```
3
```

---

## What this problem really is

Count subsets with sum exactly `x`. `n <= 40` kills plain `2^n`, and `x` / values up to `1e9` kill classic knapsack DP.

## What to use

| Tool | Why |
|------|-----|
| Split into two halves | `2^(n/2)` is ~1e6, feasible |
| Sorted list / map of left sums | Match each right sum `s` with left count of `x - s` |
| 64-bit answer | Many subsets can hit `x` |

## Approach

**Root idea:** every subset is a left half plus a right half. Enumerate all subset sums on each half, then count complementary pairs.

**Mental picture:** two bags of ~20 items. List every possible total you can pack from bag A and from bag B; for each B-total, ask how many A-totals make `x` together.

**How that becomes the method**

1. Split `t[0..n)` into `L = t[0..n/2)`, `R = t[n/2..n)`.
2. Generate all `2^|L|` subset sums of `L` into a vector; sort it (or put frequencies in a `map` / sorted unique + counts).
3. For each subset sum `s` of `R`, add the number of left sums equal to `x - s` (binary search lower/upper bound, or map lookup).
4. Include empty subsets on both sides (sum `0`).

**Data structures:** two vectors of subset sums; sorted left vector for `equal_range`, or `unordered_map`/`map` of frequencies.

## Walkthrough (on a sample)

`{1,2,3,2}`, `x=5`. Ways: `{2,3}`, `{3,2}` (the other 2), `{1,2,2}`. Meet-in-the-middle finds the same three complementary pairs across the split.

## Pitfalls

- Overflow when summing (use 64-bit sums; skip / break if sum already `> x` optionally).  
- Forgetting the empty subset.  
- Using `int` for the way count.  
- `O(2^n)` without splitting.

## Complexity

- Time: `O(2^(n/2) * n)` generate + `O(2^(n/2) log 2^(n/2))` sort/match  
- Space: `O(2^(n/2))`

## Key takeaway

**Subset-sum count with `n~40` = meet in the middle** — enumerate both halves, match complementary sums.
