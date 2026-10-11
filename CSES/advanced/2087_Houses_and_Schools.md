# [2087 — Houses and Schools](https://cses.fi/problemset/task/2087)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Techniques  
**Tags:** DP, Knuth optimization, facility location  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` houses on a street, numbered `1,2,...,n`. The distance of houses `a` and `b` is `|a-b|`. You know the number of children in each house.

Your task is to establish `k` schools in such a way that each school is in some house. Then, each child goes to the nearest school. What is the minimum total walking distance of the children if you act optimally?

### Input

The first input line has two integers `n` and `k`: the number of houses and the number of schools. The houses are numbered `1,2,...,n`.

After this, there are `n` integers `c1, c2, ..., cn`: the number of children in each house.

### Output

Print the minimum total distance.

### Constraints

- `1 <= k <= n <= 3000`
- `1 <= ci <= 1e9`

### Examples

**Input**
```
6 2
2 7 1 4 6 4
```

**Output**
```
11
```

Explanation: Houses `2` and `5` will have schools.

---

## What this problem really is

Place `k` schools on a line of weighted points; every house walks to the nearest school. Min total child-distance.

## What to use

| Tool | Why |
|------|-----|
| Optimal 1-school on a segment | Median of the weighted houses (on a line) |
| Prefixed cost `cost(l,r)` | Min walk if only one school serves houses `l..r` |
| Layered DP + Knuth/D&C | `dp[t][r] = min_l dp[t-1][l-1] + cost(l,r)` |

## Approach

**Root idea:** with schools on a line and nearest assignment, the `k` schools partition the street into `k` contiguous segments, each served by one school. Optimize the partition; inside a segment the best school is at a weighted median.

**Mental picture:** draw `k-1` borders between houses. Each block gets one school at its “balance point”; kids only walk inside their block to that school.

**How that becomes the method**

1. Precompute `cost(l,r)` = min over school position `p in [l,r]` of `sum_{i=l..r} c[i]*|i-p|`. On a line this is the weighted median; with prefix sums of `c` and of `c*i` you can evaluate a candidate `p` in `O(1)` and find the median in `O(1)` after prefixes (or build all `cost` in `O(n^2)`).
2. `dp[1][i] = cost(1,i)`.
3. `dp[t][i] = min_{j<i} dp[t-1][j] + cost(j+1,i)` for `t=2..k`.
4. Answer `dp[k][n]`. Use Knuth or divide-and-conquer optimization (`n<=3000`).

**Data structures:** prefix `sumC`, `sumCi`; `cost[][]` or on-the-fly; rolling `dp`.

## Walkthrough (on a sample)

Schools at `2` and `5`: left block houses `1..3` (or similar optimal split) and right `4..6` yield total walk `11`.

## Pitfalls

- Placing school off the segment (never optimal here).  
- Mean instead of median for 1D absolute distance.  
- 32-bit overflow (`ci` up to `1e9`, distances up to `n`).  
- Naive `O(k n^3)` building costs inside DP.

## Complexity

- Time: `O(n^2)` precompute + `O(k n)` Knuth / `O(k n log n)` D&C  
- Space: `O(n^2)` for cost or `O(n)` with careful evaluation

## Key takeaway

**k schools on a line = partition DP** with `cost(l,r)` = weighted-median 1-facility cost, sped up by Knuth/D&C.
