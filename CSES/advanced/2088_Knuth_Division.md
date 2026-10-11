# [2088 — Knuth Division](https://cses.fi/problemset/task/2088)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Techniques  
**Tags:** DP, Knuth optimization, interval DP  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an array of `n` numbers, your task is to divide it into `n` subarrays, each of which has a single element.

On each move, you may choose any subarray and split it into two subarrays. The cost of such a move is the sum of values in the chosen subarray.

What is the minimum total cost if you act optimally?

### Input

The first input line has an integer `n`: the array size. The array elements are numbered `1,2,...,n`.

The second line has `n` integers `x1, x2, ..., xn`: the contents of the array.

### Output

Print one integer: the minimum total cost.

### Constraints

- `1 <= n <= 5000`
- `1 <= xi <= 1e9`

### Examples

**Input**
```
5
2 7 3 2 5
```

**Output**
```
43
```

---

## What this problem really is

Start from the whole array as one piece; repeatedly split until every piece is a singleton. Each split of a segment pays that segment’s sum. Min total payment — classic interval DP with Knuth optimization.

## What to use

| Tool | Why |
|------|-----|
| Interval DP | `dp[l][r] = sum(l..r) + min_k (dp[l][k]+dp[k+1][r])` |
| Prefix sums | Segment sum in `O(1)` |
| Knuth `opt` | `n=5000` needs `O(n^2)`, not `O(n^3)` |

## Approach

**Root idea:** the last split that separated `[l..r]` cut after some `k`. You already paid optimally to fully split left and right; then you also paid `sum(l..r)` when you split this segment (whenever that happened — it is charged exactly once for this interval in the recurrence above). Empty/singleton: `dp[i][i]=0` (no split needed).

**Mental picture:** a binary breaking tree over the array. Every contiguous segment that appears as a node costs its sum when it breaks into two children. Choose the tree of minimum total node costs (except leaves).

**How that becomes the method**

1. `pref[0]=0`; `pref[i]=pref[i-1]+x[i]`; `sum(l,r)=pref[r]-pref[l-1]`.
2. `dp[i][i]=0`; for length `len=2..n`, for all `l`, `r=l+len-1`:
   - `dp[l][r] = sum(l,r) + min_{k=l..r-1} (dp[l][k] + dp[k+1][r])`
3. Knuth: maintain `opt[l][r]` with `opt[l][r-1] <= opt[l][r] <= opt[l+1][r]`, only search that range.
4. Answer `dp[1][n]` (or `0` if `n=1`).

**Data structures:** `dp[n+1][n+1]`, `opt[n+1][n+1]`, prefix sums; 64-bit.

## Walkthrough (on a sample)

Optimal break order yields total `43` (every mid-size segment sum that appears in the break tree is charged once).

## Pitfalls

- Charging the sum on singletons.  
- Off-by-one in split index `k`.  
- 32-bit overflow.  
- Cubic DP without Knuth → TLE at `n=5000`.

## Complexity

- Time: `O(n^2)` with Knuth  
- Space: `O(n^2)`

## Key takeaway

**Min cost recursive splitting = interval DP** `sum + min left/right`, accelerated by **Knuth optimization**.
