# [1655 — Maximum Xor Subarray](https://cses.fi/problemset/task/1655)

**Platform:** CSES  
**Source:** CSES Problem Set — Bitwise Operations  
**Tags:** XOR trie, prefix XOR  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an array of `n` integers, your task is to find the maximum xor sum of a subarray.

### Input

The first line has an integer `n`: the size of the array.

The next line has `n` integers `x1, x2, ..., xn`: the contents of the array.

### Output

Print one integer: the maximum xor sum in a subarray.

### Constraints

- `1 <= n <= 2e5`
- `0 <= xi <= 1e9`

### Examples

**Input**
```
4
5 1 5 9
```

**Output**
```
13
```

---

## What this problem really is

Max `a[l] xor a[l+1] xor ... xor a[r]` over all subarrays.

## What to use

| Tool | Why |
|------|-----|
| Prefix XOR `p[r]` | Subarray xor = `p[r] ^ p[l-1]` |
| Binary trie of prefixes | For each `p[r]`, find prior `p[l-1]` maximizing xor |
| 30–31 bit depth | Values `<=1e9` |

## Approach

**Root idea:** maximize `p[r] ^ p[j]` over `j < r` (including `j=0` with `p[0]=0`). Insert prefixes into a bitwise trie; query the best opposite bits.

**Mental picture:** each prefix XOR is a path of bits in a tree. For a new prefix, walk preferring the opposite bit at every level to make the XOR large.

**How that becomes the method**

1. `pref = 0`; insert `0` into trie; `ans = 0`.
2. For each `x` in the array: `pref ^= x`; `ans = max(ans, query_max_xor(pref))`; `insert(pref)`.
3. Print `ans`.

`query_max_xor(v)`: from MSB, at each bit try to go where `(bit of v)` is flipped if that child exists.

**Data structures:** binary trie nodes with `next[2]`.

## Walkthrough (on a sample)

Some subarray XORs to `13` (e.g. involving `5^1^5^9` or a shorter piece — the trie finds the max).

## Pitfalls

- Forgetting to insert `0` (subarray from index 1).  
- Maximizing sum instead of XOR.  
- `O(n^2)` pair loops.

## Complexity

- Time: `O(n * B)` with `B~30`  
- Space: `O(n * B)`

## Key takeaway

**Max subarray XOR = prefix XORs + binary trie** for best prior prefix.
