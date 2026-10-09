# [1621 — Distinct Numbers](https://cses.fi/problemset/task/1621)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** sorting, set, uniqueness  
**Difficulty / Rating:** Easy (introductory sorting/searching)  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a list of `n` integers, and your task is to calculate the number of distinct values in the list.

### Input

The first input line has an integer `n`: the number of values.

The second line has `n` integers `x1, x2, ..., xn`.

### Output

Print one integer: the number of distinct values.

### Constraints

- `1 <= n <= 2e5`
- `1 <= xi <= 1e9`

### Examples

**Input**
```
5
2 3 2 2 3
```

**Output**
```
2
```

---

## What this problem really is

Count how many **unique** integers appear in an array. Values can be huge (`1e9`), and `n` is large (`2e5`), so a frequency array indexed by value is impossible.

## What to use

| Tool | Why |
|------|-----|
| Sort + scan | After sorting, equal values sit next to each other; one linear pass counts runs |
| Hash set / ordered set | Insert all values; size of the set is the answer |
| Why not a value-indexed array? | `xi` up to `1e9` — cannot allocate that much memory |

## Approach

1. Read `n` and the list `x`.
2. Put every value into a set (or sort a copy of the array).
3. The number of distinct values is the set size (or the number of positions where `x[i] != x[i-1]` after sorting, plus one for the first element).

Either way works under the constraints; sorting is `O(n log n)` and uses no hashing.

## Walkthrough (on a sample)

Input: `2 3 2 2 3`

**Sort path:** sort → `2, 2, 2, 3, 3`. Walk left to right: start `count = 1` for the first `2`; next two `2`s are duplicates; first `3` increments to `2`; last `3` is a duplicate. Answer `2`.

**Set path:** insert `2`, `3`, `2`, `2`, `3` → set contains `{2, 3}` → size `2`.

## Pitfalls

- **`n = 1`**: answer is always `1` — handle empty-run edge cases carefully if you hand-roll the scan.
- **All equal / all unique**: both are valid; do not assume mixed values.
- **Naive `O(n^2)` “check if seen earlier”**: too slow for `n = 2e5`.
- **Hash collisions / slow hash** (rare in practice here, but sorting avoids the issue entirely).

## Complexity

- Time: `O(n log n)` with sort or balanced set; expected `O(n)` with a hash set  
- Space: `O(n)` for the set / sorted copy

## Key takeaway

**Distinct count = set size.** When values are sparse or huge, sort-and-scan or a set replaces a frequency array.
