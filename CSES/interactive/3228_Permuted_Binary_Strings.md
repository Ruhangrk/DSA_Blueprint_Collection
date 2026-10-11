# [3228 — Permuted Binary Strings](https://cses.fi/problemset/task/3228)

**Platform:** CSES  
**Source:** CSES Problem Set — Interactive Problems  
**Tags:** interactive, bit probing, permutation  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There is a hidden permutation `a1, a2, ..., an` of integers `1, 2, ..., n`. Your task is to find this permutation.

To do this, you can ask questions: you can choose a binary string `b1 b2 ... bn` and you will receive the binary string `b[a1] b[a2] ... b[an]`.

### Interaction

This is an interactive problem. Read a single integer `n` first.

On your turn, print one of:

- `? b1b2...bn` with each `bi` in `{0,1}`: grader returns `b[a1]...b[an]`.
- `! a1 a2 ... an`: report the permutation and exit.

Flush every line.

### Constraints

- `1 <= n <= 1000`
- you can ask at most `10` questions of type `?`

### Examples

```
3
? 100
100
? 010
001
? 001
010
! 1 3 2
```

Explanation: Hidden permutation `[1,3,2]`.

---

## What this problem really is

Oracle applies an unknown permutation to bitstrings: input `b`, output `b ∘ a` (index `i` of output is `b[a[i]]`). Recover `a` in ≤10 queries.

## What to use

| Tool | Why |
|------|-----|
| Bitwise identity encoding | `ceil(log2 n)` bit probes label each position |
| Column of answers | Bit `j` of queries at output index `i` rebuilds `a[i]` |
| ≤10 queries | `2^10 = 1024 >= n` |

## Approach

**Root idea:** give every index a unique binary ID. Query, for each bit plane, the string that is `1` exactly on indices whose ID has that bit. The response’s bit at position `i` tells you that bit of `a[i]`.

**Mental picture:** stamp each seat with a unique barcode of 10 bits. Each question reads one barcode stripe across all seats, but the answers come back in permuted seat order — so you learn each output seat’s barcode, i.e. which original index it maps from.

**How that becomes the method**

1. Let `B = ceil(log2 n)` (at most 10).
2. For `bit = 0..B-1`: build string `s[j] = 1` iff `((j) >> bit) & 1` (0-based or 1-based consistently — use 0-based indices `0..n-1` mapped to values `1..n` carefully). Ask `? s`. Store response bit for each position `i`.
3. For each output position `i`, reconstruct integer `id` from the `B` response bits; that `id` is `a[i]` (adjust if you labeled `0..n-1` then add 1).
4. Print `!` permutation.

**Data structures:** `B` response strings; array `a[n]`.

## Walkthrough (on a sample)

Three queries as bit planes of size 3 recover mapping `1,3,2`.

## Pitfalls

- 0-based vs 1-based labels.  
- More than 10 bits.  
- Not flushing.

## Complexity

- Queries: `ceil(log2 n) <= 10`  
- Space: `O(n log n)` bits

## Key takeaway

**Permuted bitstrings = learn each `a[i]` bit-by-bit** with `log n` characteristic queries.
