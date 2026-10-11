# [3426 — Sliding Window Xor](https://cses.fi/problemset/task/3426)

**Platform:** CSES  
**Source:** CSES Problem Set — Sliding Window Problems  
**Tags:** sliding window, xor, LCG  
**Difficulty / Rating:** Easy  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array of `n` integers. Your task is to calculate the bitwise xor of each window of `k` elements, from left to right.

In this problem the input data is large and it is created using a generator.

### Input

The first line contains two integers `n` and `k`: the number of elements and the size of the window.

The next line contains four integers `x`, `a`, `b` and `c`: the input generator parameters. The input is generated as follows:

- `x1 = x`
- `xi = (a * x_{i-1} + b) mod c` for `i = 2,3,...,n`

### Output

Print the xor of all window xors.

### Constraints

- `1 <= k <= n <= 1e7`
- `0 <= x, a, b <= 1e9`
- `1 <= c <= 1e9`

### Examples

**Input**
```
8 5
3 7 1 11
```

**Output**
```
0
```

Explanation: The input array is `[3,0,1,8,2,4,7,6]`. The windows are `[3,0,1,8,2]`, `[0,1,8,2,4]`, `[1,8,2,4,7]` and `[8,2,4,7,6]`, and their xors are `8`, `15`, `8` and `15`. Thus, the answer is `8 xor 15 xor 8 xor 15 = 0`.

---

## What this problem really is

Same streaming setup as window sum, but the window aggregate is XOR. Then XOR all window aggregates.

## What to use

| Tool | Why |
|------|-----|
| XOR cancel property | `w ^= leave ^= enter` updates the window xor |
| Ring buffer size `k` | Recall the leaving value |
| Running `ans` | `ans ^= window_xor` each step |

## Approach

**Root idea:** XOR is its own inverse, so removing an element is XORing it again. Maintain `wx` for the current window exactly like a sum with `+/-` replaced by `xor`.

**Mental picture:** the window’s XOR is a lightboard of bits; toggling a leaving or entering number flips the bits it owns.

**How that becomes the method**

1. Generate stream; keep last `k` values in a buffer.
2. First window: `wx = xor of first k`; `ans = wx`.
3. Each step: `wx ^= buf[i%k] ^ newv`; update buffer; `ans ^= wx`.
4. Print `ans`.

**Data structures:** circular buffer; integers `wx`, `ans`.

## Walkthrough (on a sample)

Window xors `8,15,8,15` → overall `0`.

## Pitfalls

- Subtracting instead of xoring the leaving value.  
- Confusing “xor of window xors” with a single xor of the whole array.

## Complexity

- Time: `O(n)`  
- Space: `O(k)`

## Key takeaway

**Sliding window XOR updates with `wx ^= leave ^ enter`** — same skeleton as window sum.
