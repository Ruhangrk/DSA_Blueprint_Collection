# [3421 — Distinct Values Subsequences](https://cses.fi/problemset/task/3421)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** combinatorics, frequency, modular arithmetic  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an array of `n` integers, count the number of subsequences where each element is distinct.

A subsequence is a sequence of array elements from left to right that may have gaps.

### Input

The first line has an integer `n`: the array size.

The second line has `n` integers `x1, x2, ..., xn`: the array contents.

### Output

Print the number of subsequences with distinct elements. The answer can be large, so print it modulo `1e9+7`.

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
11
```

Explanation: The subsequences are `[1]` (two times), `[2]`, `[3]`, `[1,2]`, `[1,3]` (two times), `[2,1]`, `[2,3]`, `[1,2,3]` and `[2,1,3]`.

---

## What this problem really is

Count subsequences with **no duplicate values**. Order is fixed by array positions; the empty subsequence is typically **not** counted here (sample total 11, while `2^4-1=15` would count all nonempty without the distinct constraint).

## What to use

| Tool | Why |
|------|-----|
| Frequency of each value | For a fixed set of distinct values, choices multiply |
| Product over values | For value `v` with count `c`, pick it `0` or `1` time in `c` ways if once |
| Mod `1e9+7` | Large product |

## Approach

**Root idea:** in a distinct-valued subsequence each value appears at most once. For a value that occurs `c` times in the array, you either skip it, or pick **exactly one** of its `c` occurrences. Choices for different values are independent (positions automatically order the subsequence).

So if frequencies are `c1, c2, ..., cm` for the distinct values present:

```
answer = product over v of (1 + c_v)  -  1
```

The `-1` removes the all-skip (empty) subsequence. `(1 + c_v)` = skip + pick any one occurrence.

**Mental picture:** each color of ball is a bin — take at most one ball of that color, and you may choose which copy; multiply the options, subtract “take nothing”.

**Data structures**

- `map` / hash map: value → frequency.
- `long long` / modular multiply.

**Steps**

1. Count frequencies of all `xi`.
2. `ans = 1`; for each freq `c`: `ans = ans * (c + 1) % MOD`.
3. `ans = (ans - 1 + MOD) % MOD`.
4. Print `ans`.

## Walkthrough (on a sample)

Values: `1` appears twice, `2` once, `3` once.

```
(2+1) * (1+1) * (1+1) - 1 = 3 * 2 * 2 - 1 = 11
```

Matches the sample.

## Pitfalls

- Forgetting to subtract the empty subsequence.
- Treating this like sub**arrays** (contiguous) — different problem.
- Using `2^{distinct} - 1` — wrong when a value appears more than once (multiple ways to pick it).
- Overflow before mod.

## Complexity

- Time: `O(n)` expected with hash  
- Space: `O(n)`

## Key takeaway

**Distinct-value subsequences = `product(freq[v] + 1) - 1` (mod `1e9+7`)** — per value, skip or pick one of its occurrences.
