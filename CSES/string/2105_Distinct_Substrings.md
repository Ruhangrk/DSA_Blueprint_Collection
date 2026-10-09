# [2105 — Distinct Substrings](https://cses.fi/problemset/task/2105)

**Platform:** CSES  
**Source:** CSES Problem Set — String Algorithms  
**Tags:** suffix automaton, suffix array, LCP  
**Difficulty / Rating:** Medium–Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Count the number of distinct substrings that appear in a string.

### Input

The only input line has a string of length `n` that consists of characters `a`–`z`.

### Output

Print one integer: the number of substrings.

### Constraints

- `1 <= n <= 1e5`

### Examples

**Input**
```
abaa
```

**Output**
```
8
```

Explanation: the substrings are `a`, `b`, `aa`, `ab`, `ba`, `aba`, `baa` and `abaa`.

---

## What this problem really is

Count unique contiguous pieces of `s`. Total substrings are `n*(n+1)/2`, but duplicates (from repeated content) must be removed. `n = 1e5` forbids putting every substring in a set.

## What to use

| Tool | Why |
|------|-----|
| Suffix automaton (SAM) | Each state represents a set of end-equivalent substrings; easy closed count |
| Or suffix array + LCP | Duplicates between adjacent sorted suffixes are exactly the LCP lengths |
| 64-bit integer | Answer up to `n*(n+1)/2` fits in `long long` |

## Approach

**Root idea (SAM):** every distinct substring corresponds to a unique path from the SAM root (or equivalently lives in exactly one state’s length interval). For state `v` with length `len[v]` and suffix-link parent `link[v]`,

```
contribution(v) = len[v] - len[link[v]]
```

Sum over all states `v != root`.

**Mental picture:** the SAM compresses all substrings into a DAG of “end-set” classes. Each class owns a contiguous range of lengths; count those lengths.

**Suffix-array formula:** build SA and LCP. Every substring is a prefix of some suffix. Sorting suffixes, the LCP with the previous suffix is already counted — so

```
ans = n*(n+1)/2 - sum(LCP[i])
```

**Steps (SAM)**

1. Build SAM of `s`.
2. `ans = 0`; for each state `v > 0`: `ans += len[v] - len[link[v]]`.
3. Print `ans`.

## Walkthrough (on a sample)

`abaa`: 8 distinct substrings as listed (total `10` possible intervals minus `2` duplicates from repeated `a`-prefixes).

## Pitfalls

- Counting subsequences instead of substrings.
- Including the empty string.
- 32-bit overflow on `n*(n+1)/2`.
- Forgetting to subtract LCPs (or double-counting SAM root).

## Complexity

- Time: `O(n)` SAM or `O(n log n)` SA  
- Space: `O(n)`

## Key takeaway

**Distinct substrings = sum over SAM states `(len - len[link])`**, or `n*(n+1)/2 - sum(LCP)`.
