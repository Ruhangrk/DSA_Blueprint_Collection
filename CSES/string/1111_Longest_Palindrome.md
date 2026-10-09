# [1111 — Longest Palindrome](https://cses.fi/problemset/task/1111)

**Platform:** CSES  
**Source:** CSES Problem Set — String Algorithms  
**Tags:** Manacher, palindromes  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given a string, your task is to determine the longest palindromic substring of the string. For example, the longest palindrome in `aybabtu` is `bab`.

### Input

The only input line contains a string of length `n`. Each character is one of `a`–`z`.

### Output

Print the longest palindrome in the string. If there are several solutions, you may print any of them.

### Constraints

- `1 <= n <= 1e6`

### Examples

**Input**
```
aybabtu
```

**Output**
```
bab
```

---

## What this problem really is

Find any longest contiguous substring that reads the same forwards and backwards. Expand-around-center is `O(n^2)` worst case — too slow for `n = 1e6`.

## What to use

| Tool | Why |
|------|-----|
| Manacher’s algorithm | All odd/even palindrome radii in `O(n)` |
| Mirror inside a known palindrome | Reuse radius without re-expanding from scratch |
| Track max radius + center | Recover the substring at the end |

## Approach

**Root idea:** every palindrome has a center (a char, or a gap between chars). Manacher expands each center but, when inside a previously found larger palindrome, copies the mirror radius and only expands further when needed — total expansions stay linear.

**Mental picture:** a growing “palindrome window” `[L,R]`. For a new center inside it, look at the mirror center across the midpoint; borrow its radius, then try to push outward.

**Steps (odd + even radii)**

1. Compute `d1[i]` = radius of longest odd palindrome centered at `i` (length `2*d1[i]-1`).
2. Compute `d2[i]` = radius of longest even palindrome centered between `i-1` and `i` (length `2*d2[i]`).
3. Scan for the maximum length; remember the center index.
4. Slice `s` accordingly and print.

Unify odd/even by transforming to `s' = #s[0]#s[1]#...#` and running only the odd case — same asymptotics, shorter code.

## Walkthrough (on a sample)

`aybabtu`: odd center at the middle `b` of `bab` gives length 3; no longer palindrome exists → output `bab`.

## Pitfalls

- Expand-around-center without Manacher — TLE on `aaaa...`.
- Off-by-one when converting radius → substring indices.
- Returning a subsequence instead of a **substring** (must be contiguous).
- Forgetting even-length palindromes (`aa`).

## Complexity

- Time: `O(n)`  
- Space: `O(n)`

## Key takeaway

**Longest palindromic substring in linear time = Manacher** — mirror radii inside the current window, expand only when forced.
