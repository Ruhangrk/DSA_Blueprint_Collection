# [2086 — Subarray Squares](https://cses.fi/problemset/task/2086)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Techniques  
**Tags:** DP, Knuth optimization, divide and conquer DP  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an array of `n` elements, your task is to divide into `k` subarrays. The cost of each subarray is the square of the sum of the values in the subarray. What is the minimum total cost if you act optimally?

### Input

The first input line has two integers `n` and `k`: the array elements and the number of subarrays. The array elements are numbered `1,2,...,n`.

The second line has `n` integers `x1, x2, ..., xn`: the contents of the array.

### Output

Print one integer: the minimum total cost.

### Constraints

- `1 <= k <= n <= 3000`
- `1 <= xi <= 1e5`

### Examples

**Input**
```
8 3
2 3 1 2 2 3 4 1
```

**Output**
```
110
```

Explanation: An optimal solution is `[2,3,1]`, `[2,2,3]`, `[4,1]`, whose cost is `(2+3+1)^2+(2+2+3)^2+(4+1)^2=110`.

---

## What this problem really is

Partition `a[1..n]` into exactly `k` contiguous pieces minimizing sum of `(piece_sum)^2`.

## What to use

| Tool | Why |
|------|-----|
| Prefix sums | `cost(l,r) = (pref[r]-pref[l-1])^2` in `O(1)` |
| Layered DP | `dp[c][i] = min_{j<i} dp[c-1][j] + cost(j+1,i)` |
| Knuth / D&C opt | Drop `O(k n^2)` → `O(k n log n)` or `O(k n)` |

## Approach

**Root idea:** last piece ends at `n` and starts after some cut `j`; recurse on `k-1` pieces for the prefix. The cost `(sum)^2` on positive arrays satisfies the quadrangle inequality, so optimal cut points are monotone → Knuth or divide-and-conquer optimization.

**Mental picture:** place `k-1` dividers in the array. Each block pays the square of its mass; clumps that are too fat get expensive fast, so you balance the block sums.

**How that becomes the method**

1. `pref[0]=0`; `pref[i] = pref[i-1] + x[i]`.
2. `dp[1][i] = (pref[i])^2` (one subarray).
3. For `c = 2..k`, compute `dp[c][i]` for `i = c..n` via:
   - Knuth: maintain `opt[c][i]` with `opt[c][i-1] <= opt[c][i] <= opt[c][i+1]`, search only that range; or
   - D&C: for a segment of `i`, the best `j` is monotone, recurse on halves.
4. Answer `dp[k][n]`.

Naive `O(k n^2)` can pass at `n=3000` if tight (~`k n^2 / 2` ~ 1.35e10 too big — need optimization or very tight loops; Knuth/D&C is the intended path).

**Data structures:** prefix array; 2 rolling DP rows; optional `opt` table.

## Walkthrough (on a sample)

Three blocks with sums `6,7,5` → squares `36+49+25=110`. Other partitions cost more because squaring punishes large sums.

## Pitfalls

- Allowing empty subarrays (require `j` such that each piece nonempty → `i >= c`).  
- 32-bit overflow (`sum` up to `3e3*1e5`, square up to `~9e16`).  
- Using `O(k n^2)` without Knuth/D&C carefully.

## Complexity

- Time: `O(k n)` Knuth or `O(k n log n)` D&C  
- Space: `O(n)` with rolling layers

## Key takeaway

**Min sum of squared subarray sums = layered DP + Knuth/D&C** on `cost(l,r)=(pref[r]-pref[l-1])^2`.
