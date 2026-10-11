# [3195 — Xor Pyramid Row](https://cses.fi/problemset/task/3195)

**Platform:** CSES  
**Source:** CSES Problem Set — Bitwise Operations  
**Tags:** XOR, binomial mod 2, Lucas  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Consider a xor pyramid where each number is the xor of lower-left and lower-right numbers. Given the bottom row of the pyramid, your task is to find the numbers on the `k`-th row from the top.

### Input

The first line has two integers `n` and `k`: the size of the pyramid and the given row.

The next line has `n` integers `a1, a2, ..., an`: the bottom row of the pyramid.

### Output

Print `k` integers: the numbers on the `k`-th row from the top.

### Constraints

- `1 <= k <= n <= 2e5`
- `1 <= ai <= 1e9`

### Examples

**Input**
```
8 5
2 10 5 12 9 5 1 5
```

**Output**
```
1 10 5 1 8
```

---

## What this problem really is

Same xor pyramid; output the entire row that has `k` entries when counting from the top (top has 1 entry = row `1`, bottom has `n` = row `n`).

## What to use

| Tool | Why |
|------|-----|
| Height above bottom | Row `k` from top sits `n-k` levels above the bottom |
| Binomial mod 2 | Each row cell is XOR of a window of the bottom with Lucas coeffs |
| Submask test | Fast odd-binomial check |

## Approach

**Root idea:** going up `h = n-k` levels, a cell on that row equals the peak of a contiguous bottom segment of length `h+1` (same as Peak on that window). Equivalently, the `j`-th entry (0-based) on that row is `xor_t a[j+t+1]` over those `t` with `C(h, t)` odd.

**Mental picture:** each stone on the requested floor is the tip of a small xor-pyramid planted on a sliding window of the base of width `h+1`.

**How that becomes the method**

1. `h = n - k` // levels above bottom  
2. For each position `j = 0..k-1` on that row:  
   `x = 0`; for `t = 0..h`: if `(t & h) == t`, `x ^= a[j+t+1]`; print `x`.  
3. Naive is `O(k * 2^{popcount(h)})` which can be heavy; optimize by noting all row cells are the same linear filter slid across `a` — implement as XOR convolution with the characteristic vector of submasks of `h`, done with FWHT/SOS or by xoring `a[i]` into answer positions with stride.

Efficient view: answer[j] = xor of `a[j+1 + t]` for submasks `t` of `h`. For each submask `t`, XOR the whole array `a` into `ans` shifted by `t` (`ans[j] ^= a[j+t]` for valid j). Enumerate `2^{popcount(h)}` submasks — OK when popcount is small; when large, use the bit-DP / FWHT approach for the fixed mask pattern.

**Data structures:** answer array of length `k`.

## Walkthrough (on a sample)

`n=8`, `k=5` → `h=3`. Five windows of length 4 on the bottom produce `1 10 5 1 8`.

## Pitfalls

- Off-by-one on “k-th from the top”.  
- Building `h` full pyramid layers in `O(n^2)`.  
- Using mod-`1e9+7` binomials.

## Complexity

- Time: `O(n * 2^{popcount(n-k)})` or `O(n log A)` with FWHT-style methods  
- Space: `O(n)`

## Key takeaway

**Xor pyramid row = sliding Peak windows** of length `(n-k)+1` with binomial-mod-2 weights (submasks of `n-k`).
