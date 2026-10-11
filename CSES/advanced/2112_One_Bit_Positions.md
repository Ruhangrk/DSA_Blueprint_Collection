# [2112 — One Bit Positions](https://cses.fi/problemset/task/2112)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Techniques  
**Tags:** FFT, autocorrelation, bitstring  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a binary string of length `n`. Your task is to calculate, for every `k` between `1 ... n-1`, the number of ways we can choose two positions `i` and `j` such that `i-j=k` and there is a one-bit at both positions.

### Input

The only input line has a string that consists only of characters `0` and `1`.

### Output

For every distance `k` between `1 ... n-1` print the number of ways we can choose two such positions.

### Constraints

- `2 <= n <= 2e5`

### Examples

**Input**
```
1001011010
```

**Output**
```
1 2 3 0 2 1 0 1 0
```

---

## What this problem really is

For each distance `k`, count pairs of `1`-indices at difference `k`. Autocorrelation of the 0-1 array.

## What to use

| Tool | Why |
|------|-----|
| Indicator array `a[0..n-1]` | `a[i]=1` iff `s[i]=='1'` |
| Convolution / FFT | `ans[k] = sum_i a[i]*a[i+k]` |
| Polynomial square trick | Multiply `P` by its reverse (or `P(t)*P(t^{-1})`) |

## Approach

**Root idea:** `sum_i a[i] a[i+k]` is the coefficient of a convolution of the sequence with itself (reversed). FFT multiplies in `O(n log n)`.

**Mental picture:** write `1`s as spikes on a line. Slide a copy by `k` steps; count how many spikes land on spikes.

**How that becomes the method**

1. Let `a[i] = 1` if `s[i]=='1'` else `0`.
2. Build polynomial `P(t) = sum_i a[i] t^i`.
3. Compute `P(t) * P(t)` is not quite right for differences — instead compute convolution of `a` with reversed `a`: let `R[i]=a[n-1-i]`, then `(a * R)[n-1+k] = sum_i a[i] a[i+k]` (for valid `k`), or use FFT correlation directly.
4. Print values for `k=1..n-1`.

**Data structures:** double/NTT arrays of length `>= 2n`; output integers (counts `<= n`).

## Walkthrough (on a sample)

Distances between pairs of `1`s in `1001011010` accumulate into the printed row (e.g. several pairs at distance `3`, none at distance `4`).

## Pitfalls

- Counting unordered pairs twice or using `i+j` instead of `i-j`.  
- Including `k=0`.  
- `O(n^2)` pair loops.  
- FFT rounding when converting to ints.

## Complexity

- Time: `O(n log n)`  
- Space: `O(n)`

## Key takeaway

**Counts of `1`-pairs by distance = autocorrelation via FFT.**
