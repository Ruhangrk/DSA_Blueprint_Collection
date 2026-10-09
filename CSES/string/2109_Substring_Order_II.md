# [2109 — Substring Order II](https://cses.fi/problemset/task/2109)

**Platform:** CSES  
**Source:** CSES Problem Set — String Algorithms  
**Tags:** suffix array, LCP, k-th substring with duplicates  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a string of length `n`. If all of its substrings (not necessarily distinct) are ordered lexicographically, what is the `k` th smallest of them?

### Input

The first input line has a string of length `n` that consists of characters `a`–`z`.

The second input line has an integer `k`.

### Output

Print the `k` th smallest substring in lexicographical order.

### Constraints

- `1 <= n <= 1e5`
- `1 <= k <= n*(n+1)/2`

### Examples

**Input**
```
baabaa
10
```

**Output**
```
ab
```

Explanation: The 10 smallest substrings in order are `a`, `a`, `a`, `a`, `aa`, `aa`, `aab`, `aaba`, `aabaa`, and `ab`.

---

## What this problem really is

Same lex order as listing every substring occurrence (duplicates kept). Find the `k`-th in that multiset order. Total count is always `n*(n+1)/2`.

## What to use

| Tool | Why |
|------|-----|
| Suffix array | Lex order of suffixes orders their prefixes |
| Occurrence counts via LCP / RMQ | A prefix of length `L` of `sa[i]` appears in a contiguous SA range |
| Binary search on SA range | How many times a candidate prefix occurs |

## Approach

**Root idea:** substrings sort primarily by content. All copies of the same string sit together. You can process suffixes in SA order and, for each new minimal occurrence of prefixes, count how many times each length appears across the SA (using LCP), then skip `k` through that multiplicity.

**Mental picture:** the multiset sort lists `a` four times before `aa`, etc. SA + LCP tells you the width of each “same prefix” band.

**Practical method**

1. Build SA and LCP; build RMQ on LCP for range minimum queries.
2. Scan `i` over SA. Only start a “block” when this suffix is the leftmost place a certain set of prefixes is minimal (often when `lcp[i] >= lcp[i-1]` style filters — see editorials).
3. For a prefix of `sa[i]` of length `L`, its occurrence count is the largest `r` such that `LCP(sa[i], sa[j]) >= L` for all `j` in `[i, r]` (binary search + RMQ).
4. Subtract counts from `k` for lengths / blocks until you hit the answer substring.

**Simpler special case intuition:** if you only needed distinct (Order I), each suffix adds `n-sa[i]-lcp` once. Here each distinct string is weighted by its number of starting positions.

A fully correct implementation is delicate; the SA+LCP+RMQ binary-search approach from the CSES string editorial is the standard route.

## Walkthrough (on a sample)

`baabaa`: four single-`a` substrings, then two `aa`, then longer `a...` prefixes, then `ab` as the 10th entry.

## Pitfalls

- Treating it like Order I (unique only) — wrong answer on duplicates.
- Counting `n-sa[i]` per suffix without LCP weighting — overcounts overlapping identical strings in the wrong order.
- `O(n^2)` generation of all substrings.

## Complexity

- Time: `O(n log n)` with SA + carefully charged binary searches  
- Space: `O(n)`

## Key takeaway

**k-th substring with duplicates = SA order weighted by occurrence counts from LCP ranges** — not the distinct-only fresh-prefix walk.
