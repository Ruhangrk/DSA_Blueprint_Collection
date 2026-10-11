# [2111 — Apples and Bananas](https://cses.fi/problemset/task/2111)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Techniques  
**Tags:** FFT, convolution, generating functions  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` apples and `m` bananas, and each of them has an integer weight between `1 ... k`. Your task is to calculate, for each weight `w` between `2 ... 2k`, the number of ways we can choose an apple and a banana whose combined weight is `w`.

### Input

The first input line contains three integers `k`, `n` and `m`: the number `k`, the number of apples and the number of bananas.

The next line contains `n` integers `a1, a2, ..., an`: weight of each apple.

The last line contains `m` integers `b1, b2, ..., bm`: weight of each banana.

### Output

For each integer `w` between `2 ... 2k` print the number of ways to choose an apple and a banana whose combined weight is `w`.

### Constraints

- `1 <= k, n, m <= 2e5`
- `1 <= ai <= k`
- `1 <= bi <= k`

### Examples

**Input**
```
5 3 4
5 2 5
4 3 2 3
```

**Output**
```
0 0 1 2 1 2 4 2 0
```

Explanation: For example for `w = 8` there are `4` different ways: we can pick an apple of weight `5` in two different ways and a banana of weight `3` in two different ways.

---

## What this problem really is

For every sum `w`, count ordered pairs `(apple, banana)` with `a_i + b_j = w`. Frequency convolution of two arrays of size `k`.

## What to use

| Tool | Why |
|------|-----|
| Freq arrays `A[1..k]`, `B[1..k]` | Count how many fruit of each weight |
| Convolution `C[w] = sum_i A[i]*B[w-i]` | Exactly the answer for sum `w` |
| FFT / NTT | `k` up to `2e5` → need `O(k log k)`, not `O(k^2)` |

## Approach

**Root idea:** if `A[x]` apples have weight `x` and `B[y]` bananas have weight `y`, ways for sum `w` are `sum_x A[x]*B[w-x]`. That is the coefficient-wise product of generating functions after FFT multiply.

**Mental picture:** two histograms of weights. Sliding one against the other, at each offset multiply-and-add the overlapping bars — that offset is the sum.

**How that becomes the method**

1. Build `A[0..k]`, `B[0..k]` (index by weight; `A[0]=B[0]=0`).
2. Multiply polynomials `P(t)=sum A[i] t^i` and `Q(t)=sum B[j] t^j` via FFT (double) or NTT.
3. For `w=2..2k`, print coefficient of `t^w` (64-bit integers; FFT needs careful rounding, NTT is exact if you pick mods / CRT).

**Data structures:** freq vectors; FFT buffers sized to next power of two `>= 2k+1`.

## Walkthrough (on a sample)

Two apples of weight `5`, one of `2`; bananas weights `4,3,2,3`. For `w=8`: `5+3` with `2*2=4` ways. Output lists counts for `w=2..10`.

## Pitfalls

- `O(k^2)` double loop — TLE.  
- FFT precision for large products (`n*m` up to `4e10` — use `long double` carefully or NTT/CRT).  
- Printing for `w=1` or skipping `w=2..2k` bounds.

## Complexity

- Time: `O(k log k)`  
- Space: `O(k)`

## Key takeaway

**Pair-sum counts by weight = frequency convolution via FFT/NTT.**
