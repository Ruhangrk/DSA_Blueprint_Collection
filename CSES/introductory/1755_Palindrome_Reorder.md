# [1755 — Palindrome Reorder](https://cses.fi/problemset/task/1755)

**Platform:** CSES  
**Source:** CSES Problem Set — Introductory Problems  
**Tags:** strings, counting, construction  
**Difficulty / Rating:** Easy  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given a string, your task is to reorder its letters in such a way that it becomes a palindrome (i.e., it reads the same forwards and backwards).

### Input

The only input line has a string of length `n` consisting of characters `A`–`Z`.

### Output

Print a palindrome consisting of the characters of the original string. You may print any valid solution. If there are no solutions, print `"NO SOLUTION"`.

### Constraints

- `1 <= n <= 1e6`

### Examples

**Input**
```
AAAACACBA
```

**Output**
```
AACABACAA
```

---

## What this problem really is

Rearrange the multiset of letters into a palindrome, or decide it’s impossible.

## What to use

| Tool | Why |
|------|-----|
| Frequency count `[26]` | At most one letter may have odd count |
| Build half + middle | Mirror the first half to form the palindrome |

## Approach

**Root idea:** in a palindrome, every character except possibly one center letter appears an even number of times. If more than one odd frequency → impossible. Otherwise put half of each letter on the left, the odd letter (if any) in the middle, and mirror the left side.

**Mental picture:** fold the string in half — leftover odd letter sits on the fold.

**Steps**

1. Count frequencies.
2. If more than one letter has odd count → `NO SOLUTION`.
3. Build `left` by appending `freq[c]/2` copies of each `c`; note the odd letter `mid` if any.
4. Print `left + mid + reverse(left)`.

## Walkthrough (on a sample)

`AAAACACBA` → A:6, C:2, B:1. One odd (`B`) → OK. Left half from pairs of A/C, center B, mirror → e.g. `AACABACAA`.

## Pitfalls

- Allowing multiple odd counts.
- Outputting characters you don’t have (wrong half counts).
- `O(n^2)` string concat in a loop without reserve — careful at `1e6` in some languages.

## Complexity

- Time: `O(n)`  
- Space: `O(n)` for the answer

## Key takeaway

**Palindrome anagram exists iff ≤1 odd frequency** — build half, optional center, mirror.
