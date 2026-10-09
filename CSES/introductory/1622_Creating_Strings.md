# [1622 — Creating Strings](https://cses.fi/problemset/task/1622)

**Platform:** CSES  
**Source:** CSES Problem Set — Introductory Problems  
**Tags:** backtracking, permutations  
**Difficulty / Rating:** Easy  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given a string, your task is to generate all different strings that can be created using its characters.

### Input

The only input line has a string of length `n`. Each character is between `a`–`z`.

### Output

First print an integer `k`: the number of strings. Then print `k` lines: the strings in alphabetical order.

### Constraints

- `1 <= n <= 8`

### Examples

**Input**
```
aabac
```

**Output**
```
20
aaabc
aaacb
...
cbaaa
```

---

## What this problem really is

Print all **distinct permutations** of the multiset of characters, sorted lexicographically. `n <= 8` so full enumeration is fine.

## What to use

| Tool | Why |
|------|-----|
| Sort then `next_permutation` | Unique perms in order automatically |
| Or backtracking + frequency | Skip duplicate branches when a letter is reused at the same depth |
| Count first | `k = n! / (f1! f2! ...)` or just `results.size()` |

## Approach

**Root idea:** every distinct rearrangement is a unique ordering of the multiset. Sort the string, then iterate `next_permutation` until it wraps — each yields the next alphabetical unique permutation.

**Mental picture:** a sorted deck of letters (with duplicates); keep asking for the next distinct shuffle in dictionary order.

**Steps**

1. Sort the string ascending.
2. Collect permutations via `do { save; } while (next_permutation(...))`.
3. Print count, then each string.

**Backtracking variant:** at each position choose a letter with remaining count `> 0`, try letters in alphabetical order once per depth.

## Walkthrough (on a sample)

`aabac` → frequencies a:3, b:1, c:1 → `5!/(3!)=20` distinct strings, listed from `aaabc` to `cbaaa`.

## Pitfalls

- Using permutations of indices without deduplicating — prints duplicates.
- Forgetting to sort before `next_permutation`.
- Not printing `k` on the first line.

## Complexity

- Time: `O(k * n)` with `k <= 8!`  
- Space: `O(k * n)` if storing, or print on the fly

## Key takeaway

**Distinct anagrams in order = sort + `next_permutation`** (or frequency backtracking).
