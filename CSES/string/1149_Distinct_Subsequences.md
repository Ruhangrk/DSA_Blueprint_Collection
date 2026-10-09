# [1149 — Distinct Subsequences](https://cses.fi/problemset/task/1149)

**Platform:** CSES  
**Source:** CSES Problem Set — String Algorithms  
**Tags:** DP, subsequences  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a string. You can remove any number of characters from it, but you cannot change the order of the remaining characters.

How many different strings can you generate?

### Input

The first input line contains a string of size `n`. Each character is one of `a`–`z`.

### Output

Print one integer: the number of strings modulo `1e9+7`.

### Constraints

- `1 <= n <= 5e5`

### Examples

**Input**
```
aybabtu
```

**Output**
```
103
```

---

## What this problem really is

Count **distinct non-empty subsequences** of `s` (not substrings — deletions allowed, order preserved), modulo `1e9+7`.

## What to use

| Tool | Why |
|------|-----|
| Online DP over alphabet | Process left to right; track how many subsequences end with each letter |
| `last[c]` / `ends[c]` | Avoid double-counting when the same letter repeats |
| Modular arithmetic | Required |

## Approach

**Root idea:** when you read a new character `c`, you may append `c` to **any** distinct subsequence seen so far (including starting a new subsequence with just `c`). That would create `total + 1` new subsequences ending with this occurrence of `c`. But all subsequences that already ended with `c` are replaced/superseded by these new ones — subtract the old `ends[c]` so each distinct string is counted once.

**Mental picture:** a bag of unique strings so far. New letter `c` stamps `c` onto every bag item plus a fresh single `c`, but wipe the old “ended with `c`” stamps first so duplicates collapse.

**Formulas**

```
total = 0           // # distinct non-empty subsequences so far
ends[26] = {0}      // ends[c] = # distinct subsequences ending with c

for each char c in s:
  add = (total + 1 - ends[c] + MOD) % MOD
  total = (total + add) % MOD
  ends[c] = (ends[c] + add) % MOD   // equivalently ends[c] = (total_old + 1) % MOD after update order

print total
```

(`+1` = the subsequence consisting of this single character.)

## Walkthrough (on a sample)

`aybabtu` yields `103` distinct non-empty subsequences under this recurrence (verified by the sample).

Small check: `aba` → subsequences `a,b,ab,ba,aa,aba` → 6.

## Pitfalls

- Counting contiguous substrings.
- Including the empty subsequence (or forgetting mod).
- Using `2^n` (counts subsequences with multiplicity / positions, not distinct strings).
- Negative mod when subtracting `ends[c]`.

## Complexity

- Time: `O(n)`  
- Space: `O(1)` extra (26 counters)

## Key takeaway

**Distinct subsequences online:** on char `c`, add `(total + 1 - ends[c])` new ones, then refresh `ends[c]` and `total`.
