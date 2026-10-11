# [3191 — Maximum Xor Subset](https://cses.fi/problemset/task/3191)

**Platform:** CSES  
**Source:** CSES Problem Set — Bitwise Operations  
**Tags:** XOR basis, Gaussian elimination, linear basis  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an array of `n` integers, your task is to find the maximum xor sum of a subset.

### Input

The first line has an integer `n`: the size of the array.

The next line has `n` integers `x1, x2, ..., xn`: the contents of the array.

### Output

Print one integer: the maximum xor sum of a subset.

### Constraints

- `1 <= n <= 2e5`
- `0 <= xi <= 1e9`

### Examples

**Input**
```
4
1 6 12 6
```

**Output**
```
13
```

---

## What this problem really is

Max `xor of some subset` (order irrelevant; empty subset xor `0`). Equivalent to max value obtainable from the XOR-linear span of the numbers.

## What to use

| Tool | Why |
|------|-----|
| XOR basis / Gaussian elim over GF(2) | Independent bit vectors under XOR |
| Insert each `xi` into basis | `O(B^2)` per insert with `B~30` |
| Greedy from MSB | To maximize, try to turn on high bits using basis vectors |

## Approach

**Root idea:** subset XORs form a vector space over GF(2). Build a basis (at most one vector per bit). Then greedily XOR basis elements from high bit to low to maximize the result.

**Mental picture:** each number is a bundle of bit switches. Gaussian elimination finds a clean set of switches; then you flip the highest useful switches on.

**How that becomes the method**

1. `basis[0..B]` initially empty (`-1` / 0 meaning none).
2. For each `x`: for `b = B..0`, if bit `b` of `x` is set: if `basis[b]` empty, store `x` and break; else `x ^= basis[b]`.
3. `ans = 0`; for `b = B..0`: `ans = max(ans, ans ^ basis[b])` when that basis slot is filled (always take XOR if it increases — for max XOR, `if ((ans ^ basis[b]) > ans) ans ^= basis[b]`).
4. Print `ans`.

**Data structures:** array `basis[32]` of ints.

## Walkthrough (on a sample)

Numbers `1,6,12,6` span a space whose maximum element is `13`.

## Pitfalls

- Confusing with max subarray XOR (needs trie + prefixes).  
- Inserting without reducing by existing basis.  
- Starting greedy from LSB.

## Complexity

- Time: `O(n B^2)` or `O(n B)` with careful insert  
- Space: `O(B)`

## Key takeaway

**Max subset XOR = build XOR basis, then greedy MSB combination.**
