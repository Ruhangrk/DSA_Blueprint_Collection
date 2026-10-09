# [1732 — Finding Borders](https://cses.fi/problemset/task/1732)

**Platform:** CSES  
**Source:** CSES Problem Set — String Algorithms  
**Tags:** KMP, borders, prefix function  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A border of a string is a prefix that is also a suffix of the string but not the whole string. For example, the borders of `abcababcab` are `ab` and `abcab`.

Your task is to find all border lengths of a given string.

### Input

The only input line has a string of length `n` consisting of characters `a`–`z`.

### Output

Print all border lengths of the string in increasing order.

### Constraints

- `1 <= n <= 1e6`

### Examples

**Input**
```
abcababcab
```

**Output**
```
2 5
```

---

## What this problem really is

List every length `k` (`1 <= k < n`) such that `s[0..k) == s[n-k..n)` — all proper borders of `s`.

## What to use

| Tool | Why |
|------|-----|
| KMP prefix function `pi` (LPS) | `pi[i]` = longest proper border of the prefix `s[0..i]` |
| Border chain | If `k` is a border length of `s`, then `pi[k-1]` is the next shorter border of `s` |
| Collect then reverse | Chain yields decreasing lengths; print increasing |

## Approach

**Root idea:** the longest proper border of the whole string is exactly `pi[n-1]`. Every border of a border is again a border of the original string, so following `k -> pi[k-1]` enumerates **all** border lengths without checking each `k` by hand.

**Mental picture:** nested Russian dolls of prefix=suffix. Pull off the longest doll, then the next, until nothing remains.

**Steps**

1. Compute `pi[0..n-1]` for `s` in `O(n)` (standard KMP prefix function).
2. `k = pi[n-1]`; while `k > 0`: store `k`, set `k = pi[k-1]`.
3. Reverse the stored list (or insert front) and print.

Hashing every prefix/suffix pair also works in `O(n)` after `O(n)` prep, but the `pi`-chain is the clean combinatorial solution.

## Walkthrough (on a sample)

`s = abcababcab` (`n = 10`).  
Longest border length `5` (`abcab`); next `pi[4]` gives `2` (`ab`); next is `0`.  
Printed increasing: `2 5`.

## Pitfalls

- Including length `n` (the whole string is not a border by definition).
- Printing in decreasing order.
- Recomputing string compares for each `k` — `O(n^2)` TLE.
- Confusing `pi[i]` (border of prefix `i+1`) with 1-based indexing.

## Complexity

- Time: `O(n)`  
- Space: `O(n)`

## Key takeaway

**All border lengths = the KMP `pi`-chain from `pi[n-1]` down to 0** — print them sorted ascending.
