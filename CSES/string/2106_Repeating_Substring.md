# [2106 — Repeating Substring](https://cses.fi/problemset/task/2106)

**Platform:** CSES  
**Source:** CSES Problem Set — String Algorithms  
**Tags:** suffix array, LCP, suffix automaton  
**Difficulty / Rating:** Medium–Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A repeating substring is a substring that occurs in two (or more) locations in the string. Your task is to find the longest repeating substring in a given string.

### Input

The only input line has a string of length `n` that consists of characters `a`–`z`.

### Output

Print the longest repeating substring. If there are several possibilities, you can print any of them. If there is no repeating substring, print `-1`.

### Constraints

- `1 <= n <= 1e5`

### Examples

**Input**
```
cabababc
```

**Output**
```
abab
```

---

## What this problem really is

Find a longest string that appears as a substring at **least twice** (occurrences may overlap). If every character is unique and no longer repeat exists, output `-1`.

## What to use

| Tool | Why |
|------|-----|
| Suffix array + LCP | Max LCP between adjacent sorted suffixes = length of longest common prefix of two different suffixes = longest repeat |
| Suffix automaton | States with `endpos` size `>= 2` (or link structure) hold repeats; take max `len` |
| Recover the string | From SA index + LCP length, or from a SAM state’s substring |

## Approach

**Root idea:** two occurrences of a string `t` mean two suffixes that share `t` as a prefix. Among all pairs of suffixes, the maximum shared prefix length is the answer length; that maximum always appears between some **adjacent** pair in the sorted suffix array (standard LCP fact).

**Mental picture:** sort all “tails” of the string. Neighbors that look alike for a long stretch reveal a long chunk that shows up in two places.

**Steps (SA + LCP)**

1. Build suffix array `sa[0..n-1]` and LCP array `lcp[i] = LCP(suffix sa[i], suffix sa[i-1])`.
2. Let `best = max lcp[i]`; if `best == 0` print `-1`.
3. Otherwise take any `i` achieving `best` and print `s.substr(sa[i], best)` (or `sa[i-1]`).

**SAM sketch:** after building SAM and propagating whether a state corresponds to `>= 2` end positions, answer is the maximum `len[v]` among such states; recover one substring from that state’s endpos.

## Walkthrough (on a sample)

`cabababc`: `abab` appears starting at index 1 and 3 (0-based) → length 4 is maximal among repeats.

## Pitfalls

- Requiring non-overlapping occurrences — overlaps are allowed unless stated otherwise (here locations just must differ).
- Printing the length instead of the string.
- `O(n^2)` compare-all-pairs.
- Forgetting `-1` when `max LCP == 0`.

## Complexity

- Time: `O(n log n)` SA (or `O(n)` SAM)  
- Space: `O(n)`

## Key takeaway

**Longest repeating substring = maximum LCP of adjacent suffixes in the suffix array** (or longest SAM state with multiple end positions).
