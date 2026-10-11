# [2115 — Bit Substrings](https://cses.fi/problemset/task/2115)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems II  
**Tags:** Prefix sums, counting pairs by difference, convolution (FFT/NTT), grouping by value  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given a bit string of length `n`, for each `k` from `0` to `n`, count the non-empty substrings that contain exactly `k` ones. Print `n + 1` values.

### Input

- One line with the binary string of length `n`.

### Output

Print `n + 1` values separated by spaces.

### Constraints

- `1 <= n <= 2e5`

### Examples

**Input**
```
101
```

**Output**
```
1 4 1 0
```

---

## What this problem really is

A substring is a pair of cut positions `l < r` on the string. Its count of ones is the difference of the prefix counts at the two cuts. So we need, for each difference `k`, how many pairs of cuts have that difference. The cuts with the same prefix count are grouped together, and that grouping is what makes the problem fast.

## What to use

| Tool | Why |
|------|-----|
| Prefix ones count `P[0..n]` | Substring `(l, r]` has `P[r] - P[l]` ones |
| `cnt[v]` = number of cuts with prefix count `v` | Groups cuts with the same count |
| Pair counting by group | Since `P` never decreases, cuts with value `x` come before cuts with value `x + k` |
| Correlation via FFT/NTT | Computes `sum over x of cnt[x] * cnt[x + k]` for all `k` quickly |

## Approach

**Root idea:** `P` is non-decreasing, starting at `0` and ending at the total number of ones, `m`. Take two cuts `l < r`. If `P[r] - P[l] = k >= 1`, then `P[l] = x` and `P[r] = x + k`. All cuts with value `x` come before all cuts with value `x + k`, so every pair from the two groups is a valid ordered pair. The number of such pairs is `cnt[x] * cnt[x + k]`.

For `k = 0`, pairs of cuts with the same value form substrings with no ones. Inside one group of size `c` there are `c * (c - 1) / 2` pairs.

**Mental picture:** lay out the cut positions in a row, each labeled with how many ones came before it. Equal labels form a bucket. A substring with `k` ones spans from one bucket with label `x` to a later bucket with label `x + k`. Count the bucket sizes and multiply the bucket pairs whose labels differ by `k`. That is a sliding sum over the bucket sizes.

**How that becomes the method**

1. Compute `P[0..n]`, and build `cnt[v]` for `v = 0..m`.
2. `ans[0] = sum over v of cnt[v] * (cnt[v] - 1) / 2`.
3. For `k >= 1`, `ans[k] = sum over x of cnt[x] * cnt[x + k]`. This is a correlation of the `cnt` array with itself. Compute it for all `k` with FFT or NTT. Use exact integer arithmetic (for example two NTT primes with CRT), since the answers can reach about `4e10`.
4. Output `ans[0..n]`. Values with `k > m` are `0`.

**Data structures:** an array `cnt` of size `m + 1`, and an NTT routine for the correlation.

## Walkthrough (on a sample)

String `101`, `n = 3`.

Prefix counts `P[0..3]`: `P[0] = 0`, `P[1] = 1`, `P[2] = 1`, `P[3] = 2`.

So `cnt[0] = 1`, `cnt[1] = 2`, `cnt[2] = 1`.

**`k = 0`:** `cnt[1]` has 2 cuts, giving `2 * 1 / 2 = 1` pair. The substring is the single `0` between cuts `1` and `2`. Answer `1`. ✓

**`k = 1`:** `cnt[0] * cnt[1] + cnt[1] * cnt[2] = 1 * 2 + 2 * 1 = 4`. These are the substrings `10`, `1` (cuts 1 to 2), `01`, `1`. Four. ✓

**`k = 2`:** `cnt[0] * cnt[2] = 1 * 1 = 1`. The substring `101`. ✓

**`k = 3`:** no `x` with `x + 3` present, answer `0`. ✓

Output: `1 4 1 0`. Matches.

## Pitfalls

- Counting all pairs `(l, r)` directly. That is `O(n^2)` and too slow for `n = 2e5`.
- Forgetting the `k = 0` case, which needs `c * (c - 1) / 2` inside each group, not `c * c`.
- Using `c * c` for `k = 0` also counts empty substrings (a cut paired with itself).
- Overflow in the convolution. Use exact arithmetic, since double-precision FFT can lose the low digits for these sizes.
- Printing `n + 1` values, including the trailing zeros for large `k`.

## Complexity

- Time: `O(n log n)` for the NTT-based correlation.
- Space: `O(n)`.

## Key takeaway

Substrings with exactly `k` ones are pairs of cuts whose prefix counts differ by `k`. Group the cuts by prefix count, then the answer for each `k` is a correlation of the group sizes, computed with FFT or NTT.
