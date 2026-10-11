# [3233 — All Subarray Xors](https://cses.fi/problemset/task/3233)

**Platform:** CSES  
**Source:** CSES Problem Set — Bitwise Operations  
**Tags:** sliding basis, subarray XOR  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an array of `n` integers, your task is to find all integers that are the xor sum in some subarray.

### Input

The first line has an integer `n`: the size of the array.

The next line has `n` integers `x1, x2, ..., xn`: the contents of the array.

### Output

First print an integer `k`: the number of distinct integers that are the xor sum in some subarray.

After this print `k` integers: the xor sums in increasing order.

### Constraints

- `1 <= n <= 2e5`
- `0 <= xi <= 1e6`

### Examples

**Input**
```
4
5 1 5 9
```

**Output**
```
7
1 4 5 8 9 12 13
```

---

## What this problem really is

List every distinct value of `a[l] xor a[l+1] xor ... xor a[r]` over nonempty subarrays, sorted.

## What to use

| Tool | Why |
|------|-----|
| Ending-at-`i` XOR set | New right endpoint updates old ending xors by `^ a[i]` |
| Gaussian reduce to `O(B)` | Keep only a linearly independent bag of ending xors |
| Global `set` | Union of all seen ending values |

## Approach

**Root idea:** the family of subarray xors that end at index `i` is `{ a[i] } ∪ { v ^ a[i] : v ends at i-1 }`. That family has huge size in theory, but its linear basis has size `O(B)` (`B ~ 20` for values up to `1e6`). Maintain that basis while sliding `i`, and dump every produced xor into a global set.

**Mental picture:** at the right tip of the array you hold a small pouch of “representative” xors of segments that end here. Move one step right: xor the whole pouch by the new element and toss in the singleton, then compress duplicates via linear dependence.

**How that becomes the method**

1. `vector<int> cur` — basis of xors of subarrays ending at the previous index.  
2. `set<int> all`.  
3. For each `a` in the array:  
   - Build a list `nxt = {a}`; for each `v` in `cur`, push `v ^ a`.  
   - Insert every element of `nxt` into `all`.  
   - Replace `cur` by a Gaussian-eliminated basis of `nxt` (size `<= B`).  
4. Print `all.size()` and the sorted contents.

**Data structures:** small vectors for bases; `set<int>` (or `bool` mark if you compress to `0..2e6` range of possible xors — not guaranteed dense).

## Walkthrough (on a sample)

Seven distinct subarray xors appear; printed in increasing order as in the sample.

## Pitfalls

- Computing only subset xors (different problem).  
- Keeping the full `O(n)` ending set without reduction → TLE/MLE.  
- Forgetting singletons `[i..i]`.

## Complexity

- Time: `O(n B^2 + |ans| log |ans|)`  
- Space: `O(|ans| + B)`

## Key takeaway

**All subarray XORs = slide an `O(B)` ending-xor basis** and union values into a global set.
