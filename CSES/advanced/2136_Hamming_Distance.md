# [2136 — Hamming Distance](https://cses.fi/problemset/task/2136)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Techniques  
**Tags:** bitsets, popcount, brute force  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

The Hamming distance between two strings `a` and `b` of equal length is the number of positions where the strings differ.

You are given `n` bit strings, each of length `k` and your task is to calculate the minimum Hamming distance between two strings.

### Input

The first input line has two integers `n` and `k`: the number of bit strings and their length.

Then there are `n` lines each consisting of one bit string of length `k`.

### Output

Print the minimum Hamming distance between two strings.

### Constraints

- `2 <= n <= 2e4`
- `1 <= k <= 30`

### Examples

**Input**
```
5 6
110111
001000
100001
101000
101110
```

**Output**
```
1
```

Explanation: The strings `101000` and `001000` differ only at the first position.

---

## What this problem really is

Minimum pairwise Hamming distance among `n` bitvectors of length `k <= 30`.

## What to use

| Tool | Why |
|------|-----|
| Pack string → `int` bitmask | `k <= 30` fits in 32 bits |
| `popcount(a ^ b)` | Hamming distance of two masks |
| Tight `O(n^2)` loop | `n=2e4` → ~2e8 xors; passes with fast I/O / pragmas in practice |

## Approach

**Root idea:** distance is just the number of differing bits. After packing, every pair is one xor + popcount; take the global minimum.

**Mental picture:** each string is a binary number. XOR lights up the disagreeing bits; count the lights; keep the smallest count over pairs.

**How that becomes the method**

1. Read each string; set bit `j` if `s[j]=='1'` (either endianness is fine if consistent).
2. Store `n` integers.
3. For all `i < j`, `ans = min(ans, popcount(v[i] ^ v[j]))`.
4. Print `ans` (init with `k`).

Fancier FPT / Walsh tricks exist for harder variants; here raw pair enumeration is the intended practical solution.

**Data structures:** `vector<int> v(n)`.

## Walkthrough (on a sample)

`101000` xor `001000` = one bit → distance `1`, which is optimal.

## Pitfalls

- Slow input without fast I/O.  
- Using `string` compares in the inner loop.  
- Assuming you must beat `O(n^2)` — not required at these limits with a tight loop.

## Complexity

- Time: `O(n^2)` bit operations  
- Space: `O(n)`

## Key takeaway

**Min Hamming on short bitstrings = pack to ints + `popcount(xor)` over pairs.**
