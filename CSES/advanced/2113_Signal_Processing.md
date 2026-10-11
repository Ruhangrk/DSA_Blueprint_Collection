# [2113 — Signal Processing](https://cses.fi/problemset/task/2113)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Techniques  
**Tags:** FFT, convolution, cross-correlation  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given two integer sequences: a signal and a mask. Your task is to process the signal by moving the mask through the signal from left to right. At each mask position calculate the sum of products of aligned signal and mask values in the part where the signal and the mask overlap.

### Input

The first input line consists of two integers `n` and `m`: the length of the signal and the length of the mask.

The next line consists of `n` integers `a1, a2, ..., an` defining the signal.

The last line consists of `m` integers `b1, b2, ..., bm` defining the mask.

### Output

Print `n+m-1` integers: the sum of products of aligned values at each mask position from left to right.

### Constraints

- `1 <= n, m <= 2e5`
- `1 <= ai, bi <= 100`

### Examples

**Input**
```
5 3
1 3 2 1 4
1 2 3
```

**Output**
```
3 11 13 10 16 9 4
```

Explanation: For example, at the second mask position the sum of aligned products is `2*1 + 3*3 = 11`.

---

## What this problem really is

Discrete cross-correlation / convolution of sequences `a` and `b` as the mask slides, producing `n+m-1` overlap sums of products.

## What to use

| Tool | Why |
|------|-----|
| Convolution | Sliding dot-product is polynomial multiply |
| Reverse the mask | Correlation ↔ convolution with reversed `b` |
| FFT | Lengths `2e5` → `O((n+m) log (n+m))` |

## Approach

**Root idea:** when the mask is shifted so that `b[0]` aligns against signal index `t` (with partial overlaps at the ends), the value is `sum_j a[t+j] * b[j]` over valid `j`. That family of sums is exactly the convolution of `a` with reversed `b`.

**Mental picture:** slide a stencil across the signal; at each offset, multiply overlapping cells and add. FFT does all offsets at once.

**How that becomes the method**

1. Reverse `b` (or reverse `a` — be consistent with desired left-to-right order).
2. Multiply polynomials `sum a[i] t^i` and `sum b_rev[j] t^j` with FFT/NTT.
3. Output the `n+m-1` meaningful coefficients in the order matching the problem’s left-to-right sweep (match the sample’s first values: first overlap is only `a[0]*b[m-1]` style depending on convention — calibrate on the sample: first output `3 = 1*3`, last `4 = 4*1`).

**Data structures:** FFT buffers; 64-bit integer results after rounding.

## Walkthrough (on a sample)

Mask `1 2 3` over signal `1 3 2 1 4` produces seven positions; second printed value `11` matches the stated partial alignment.

## Pitfalls

- Forgetting to reverse one sequence (getting the wrong direction).  
- Wrong output length (`n+m-1`).  
- FFT precision (`ai,bi <= 100` keeps products manageable).  
- `O(nm)` sliding window — TLE.

## Complexity

- Time: `O((n+m) log (n+m))`  
- Space: `O(n+m)`

## Key takeaway

**Sliding mask dot-products = convolution (FFT)** of the signal with the reversed mask.
