# [1743 — String Reorder](https://cses.fi/problemset/task/1743)

**Platform:** CSES  
**Source:** CSES Problem Set — Introductory Problems  
**Tags:** greedy, strings, frequencies  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to reorder the characters of a string so that no two adjacent characters are the same. What is the lexicographically minimal such string?

### Input

The only line has a string of length `n` consisting of characters `A`–`Z`.

### Output

Print the lexicographically minimal reordered string where no two adjacent characters are the same. If it is not possible to create such a string, print `-1`.

### Constraints

- `1 <= n <= 1e6`

### Examples

**Input**
```
HATTIVATTI
```

**Output**
```
AHATITITVT
```

---

## What this problem really is

Lexicographically smallest rearrangement with no equal neighbors. Impossible when some letter is too frequent (`freq > (n+1)/2`).

## What to use

| Tool | Why |
|------|-----|
| Frequency array `[26]` | Only 26 letters; `n` up to `1e6` |
| Greedy by position | At each step pick the smallest letter that is safe and still allows a completion |
| Feasibility check | Max frequency `<= (n+1)/2` |

## Approach

**Root idea:** build left to right. At each position, try letters from `A` to `Z` and take the **smallest** one that (1) still has remaining count, (2) differs from the previous character, and (3) after taking it, the remaining multiset is still rearrangeable (no letter exceeds `(rem+1)/2`).

**Mental picture:** always append the earliest letter in the alphabet that doesn’t break adjacency or doom the leftover frequencies.

**Feasibility after placing a letter:** if remaining length is `r` and some count is `c`, need `c <= (r+1)/2` for all letters (same rule on the suffix).

**Steps**

1. Count frequencies; if `max_freq > (n+1)/2` → `-1`.
2. `prev = 0` (sentinel). For each position:
   - For `ch = A..Z`: if `freq[ch]>0` and `ch != prev` and (hypothetically decrementing) leftover is OK → choose `ch`, break.
3. Append choices; print the string.

(Optimized implementations avoid rescanning all 26 with care; at `n=1e6` and 26 letters, `O(26 n)` is acceptable.)

## Walkthrough (on a sample)

`HATTIVATTI` — frequencies allow a valid layout; greedy smallest letters yield `AHATITITVT` with no equal adjacent pair.

## Pitfalls

- Only checking global max freq once, then greedily picking smallest without leftover checks — can get stuck.
- Using a max-heap “most frequent first” — valid but **not** lex-smallest.
- `n=1` — any single char is fine.

## Complexity

- Time: `O(26 * n)`  
- Space: `O(n)` for the answer

## Key takeaway

**Lex-smallest no-adjacent-equal reorder = greedy smallest safe letter each step**, with the `(remaining+1)/2` frequency invariant (or `-1` if max freq too large).
