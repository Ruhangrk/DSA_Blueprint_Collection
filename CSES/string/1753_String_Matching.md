# [1753 — String Matching](https://cses.fi/problemset/task/1753)

**Platform:** CSES  
**Source:** CSES Problem Set — String Algorithms  
**Tags:** KMP, Z-algorithm, pattern matching  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given a string and a pattern, your task is to count the number of positions where the pattern occurs in the string.

### Input

The first input line has a string of length `n`, and the second input line has a pattern of length `m`. Both of them consist of characters `a`–`z`.

### Output

Print one integer: the number of occurrences.

### Constraints

- `1 <= n, m <= 1e6`

### Examples

**Input**
```
saippuakauppias
pp
```

**Output**
```
2
```

---

## What this problem really is

Count (possibly overlapping) occurrences of pattern `P` as a substring of text `T`. Lengths up to `1e6` → need linear (or near-linear) matching, not `O(n*m)` naive compare.

## What to use

| Tool | Why |
|------|-----|
| Z-algorithm or KMP | `O(n+m)` substring search |
| Separator trick | `S = P + "#" + T` so Z/KMP sees matches against `P` |
| Count `Z[i] == m` (or KMP matches) | Each full hit is one occurrence |

## Approach

**Root idea (Z-algorithm):** `Z[i]` = length of the longest substring starting at `i` that matches a prefix of `S`. If you put the pattern first, every place in the text where `Z[i] == |P|` is an occurrence.

**Mental picture:** slide a “ruler” that already knows the pattern; the Z-box reuses previous matches so you never re-compare the same characters blindly.

**Z-method steps**

1. Build `S = P + "$" + T` (any char not in the alphabet).
2. Compute the Z-array of `S` in `O(|S|)`.
3. Count indices `i > |P|` with `Z[i] == |P|`.

**KMP alternative:** build failure/`pi` on `P`, then scan `T`; every time the match length reaches `m`, increment and fall back using `pi`.

Hashing (Rabin–Karp) also works with careful double hashing.

## Walkthrough (on a sample)

`T = saippuakauppias`, `P = pp`.  
Occurrences start at the two `pp` blocks inside `saippua...` and `...kauppias` → answer `2`.

## Pitfalls

- Forbidding overlaps — overlapping matches still count (e.g. `aaa` vs `aa` → 2).
- Separator char that appears in the alphabet — use `$` or similar outside `a-z`.
- Off-by-one when counting only the text portion of `S`.
- Naive `O(n*m)` — TLE at `1e6`.

## Complexity

- Time: `O(n + m)`  
- Space: `O(n + m)`

## Key takeaway

**Count pattern hits in linear time with Z or KMP** — `P + sep + T`, then count full-length matches.
