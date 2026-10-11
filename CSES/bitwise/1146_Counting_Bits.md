# [1146 — Counting Bits](https://cses.fi/problemset/task/1146)

**Platform:** CSES  
**Source:** CSES Problem Set — Bitwise Operations  
**Tags:** bit DP, digit DP, popcount sum  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to count the number of one bits in the binary representations of integers between `1` and `n`.

### Input

The only input line has an integer `n`.

### Output

Print the number of one bits in the binary representations of integers between `1` and `n`.

### Constraints

- `1 <= n <= 1e15`

### Examples

**Input**
```
7
```

**Output**
```
12
```

Explanation: Binary of `1..7` has twelve `1`-bits total.

---

## What this problem really is

Compute `sum_{x=1}^{n} popcount(x)` for huge `n` — need `O(log n)`, not a loop.

## What to use

| Tool | Why |
|------|-----|
| Per-bit contribution | Count how many numbers in `1..n` have bit `b` set |
| Block formula | For bit `b`, period `2^(b+1)` with half ones |
| 64-bit answer | Up to ~`n * 60` |

## Approach

**Root idea:** total ones = sum over bit positions of (how many integers in `1..n` have that bit on).

**Mental picture:** write all numbers in a binary table; count the `1`s column by column.

**How that becomes the method**

For each bit `b = 0..60`:

- Let `cycle = 1LL<<(b+1)`, `half = 1LL<<b`.
- In every complete cycle, bit `b` is `1` exactly `half` times.
- `ans += (n+1) / cycle * half` plus the partial remainder: let `r = (n+1) % cycle`; add `max(0, r - half)` (adjust for counting `0..n` then subtract popcount(0)=0 — equivalent formulas for `1..n`).

Alternatively: recursive digit-DP / “count from `0..n` then done.”

**Data structures:** none; loop bits.

## Walkthrough (on a sample)

`n=7`: bits contribute to total `12`.

## Pitfalls

- Including `0` or excluding `n`.  
- 32-bit overflow.  
- Off-by-one in the remainder segment for each bit.

## Complexity

- Time: `O(log n)`  
- Space: `O(1)`

## Key takeaway

**Sum of popcounts `1..n` = sum over bits of set-count in range** — closed form per bit.
