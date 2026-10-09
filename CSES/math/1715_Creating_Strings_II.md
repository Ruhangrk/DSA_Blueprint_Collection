# [1715 — Creating Strings II](https://cses.fi/problemset/task/1715)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** multinomial, factorials  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given a string, your task is to calculate the number of different strings that can be created using its characters.

### Input

The only input line has a string of length `n`. Each character is between `a`–`z`.

### Output

Print the number of different strings modulo `1e9+7`.

### Constraints

- `1 <= n <= 1e6`

### Examples

**Input**
```
aabac
```

**Output**
```
20
```

---

## What this problem really is

Count distinct permutations of a multiset of letters: `n! / (f_a! f_b! ... f_z!)`, modulo `1e9+7`. (Same count as Creating Strings I, but `n` is huge so enumerate permutations is impossible.)

## What to use

| Tool | Why |
|------|-----|
| Frequency of each letter | Denominator factorials |
| `fact` / `invfact` up to `n` | Modular multinomial |
| One pass | `n <= 1e6` |

## Approach

**Root idea:** total permutations of `n` positions is `n!`; divide by `f!` for each letter that appears `f` times (indistinguishable copies).

**Mental picture:** if all characters were unique you’d have `n!` strings; identical letters collapse groups of size `f!`.

**Steps**

1. Count `freq[26]`; `n = length`.
2. Precompute `fact[0..n]`, `invfact[0..n]`.
3. `ans = fact[n]`; for each `f`: `ans = ans * invfact[f] % MOD`.
4. Print `ans`.

## Walkthrough (on a sample)

`aabac`: a×3, b×1, c×1 → `5! / 3! = 20`.

## Pitfalls

- Integer division instead of modular inverse.
- Forgetting letters with frequency 0 (harmless if you skip).

## Complexity

- Time: `O(n + Sigma)`  
- Space: `O(n)`

## Key takeaway

**Distinct anagrams count = multinomial `n! / Π f_c!` mod `1e9+7`.**
