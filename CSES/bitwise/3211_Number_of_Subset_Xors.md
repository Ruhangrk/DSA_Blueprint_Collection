# [3211 — Number of Subset Xors](https://cses.fi/problemset/task/3211)

**Platform:** CSES  
**Source:** CSES Problem Set — Bitwise Operations  
**Tags:** XOR basis, linear algebra GF(2)  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an array of `n` integers, your task is to find the number of different subset xors.

### Input

The first line has an integer `n`: the size of the array.

The next line has `n` integers `x1, x2, ..., xn`: the contents of the array.

### Output

Print one integer: the number of different subset xors.

### Constraints

- `1 <= n <= 2e5`
- `0 <= xi <= 1e9`

### Examples

**Input**
```
3
3 6 5
```

**Output**
```
4
```

Explanation: Possible subset xors are `0`, `3`, `5`, `6` (four distinct values).

---

## What this problem really is

Size of the set `{ xor of S : S subset of the array }` (empty → `0`).

## What to use

| Tool | Why |
|------|-----|
| XOR linear basis | Span size is `2^rank` |
| Rank = # basis vectors | Insert all `xi`, count nonempty basis slots |
| Include `0` | Always achievable |

## Approach

**Root idea:** subset XORs are exactly the vector space spanned by the numbers over GF(2). A space of dimension `r` has `2^r` distinct vectors.

**Mental picture:** each array element is a bit-vector. Independent ones multiply the number of reachable XORs by 2; dependent ones add nothing new.

**How that becomes the method**

1. Build the XOR basis by Gaussian elimination (same insert as Maximum Xor Subset).
2. Let `r` = number of nonzero basis vectors.
3. Answer `1LL << r` (fits in 64-bit because `r <= 30` for values `<=1e9`, or `r <= 60` safely — actually `2^30` fine; if values were larger still `r <= bitwidth`).

**Data structures:** `basis[32]`; `long long` for the power of two.

## Walkthrough (on a sample)

`{3,6,5}` has rank `2` → `4` distinct subset xors.

## Pitfalls

- Answering `n+1` or `2^n` without rank.  
- Forgetting empty subset.  
- Overflow if you use `int` for `1<<r` when `r=31`.

## Complexity

- Time: `O(n B)`  
- Space: `O(B)`

## Key takeaway

**# distinct subset XORs = `2^(XOR-basis rank)`.**
