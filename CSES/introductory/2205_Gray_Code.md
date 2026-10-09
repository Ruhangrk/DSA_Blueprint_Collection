# [2205 — Gray Code](https://cses.fi/problemset/task/2205)

**Platform:** CSES  
**Source:** CSES Problem Set — Introductory Problems  
**Tags:** bit manipulation, construction, recursion  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A Gray code is a list of all `2^n` bit strings of length `n`, where any two successive strings differ in exactly one bit (i.e., their Hamming distance is one).

Your task is to create a Gray code for a given length `n`.

### Input

The only input line has an integer `n`.

### Output

Print `2^n` lines that describe the Gray code. You can print any valid solution.

### Constraints

- `1 <= n <= 16`

### Examples

**Input**
```
2
```

**Output**
```
00
01
11
10
```

---

## What this problem really is

List all `n`-bit strings in an order where each step flips exactly one bit (binary-reflected Gray code is the standard construction).

## What to use

| Tool | Why |
|------|-----|
| Formula `i ^ (i >> 1)` | Binary-reflected Gray code for index `i` |
| Or recursive reflect | `0`+G(n-1), then `1`+reversed G(n-1) |
| `n <= 16` | `2^16 = 65536` lines — fine to print all |

## Approach

**Root idea (formula):** for `i = 0 .. 2^n - 1`, the Gray code word is the binary representation of `i ^ (i >> 1)`. Consecutive integers mapped this way differ by one bit.

**Mental picture:** walk the corners of an `n`-dimensional hypercube along edges — each step flips one coordinate.

**Recursive picture:** take a Gray list for `n-1`, prefix `0`; then reverse that list and prefix `1`. The join flips only the leading bit.

**Steps**

1. For `i = 0 .. (1<<n)-1`: `g = i ^ (i >> 1)`; print `g` as an `n`-bit string (leading zeros).
2. Done.

## Walkthrough (on a sample)

`n=2`: `i=0,1,2,3` → Gray `0,1,3,2` → `00 01 11 10`.

## Pitfalls

- Printing integers without padding to width `n`.
- Listing numbers in plain binary order — adjacent values can differ in many bits.

## Complexity

- Time: `O(n * 2^n)` to print  
- Space: `O(1)` extra with the formula

## Key takeaway

**Gray code = `i ^ (i >> 1)` for `i = 0..2^n-1`** (or the reflected recursive construction).
