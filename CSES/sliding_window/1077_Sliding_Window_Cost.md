# [1077 — Sliding Window Cost](https://cses.fi/problemset/task/1077)

**Platform:** CSES  
**Source:** CSES Problem Set — Sliding Window Problems  
**Tags:** sliding window, median, cost to equalize  
**Difficulty / Rating:** Medium–Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array of `n` integers. Your task is to calculate for each window of `k` elements, from left to right, the minimum total cost of making all elements equal.

You can increase or decrease each element with cost `x` where `x` is the difference between the new and the original value. The total cost is the sum of such costs.

### Input

The first line contains two integers `n` and `k`: the number of elements and the size of the window.

Then there are `n` integers `x1, x2, ..., xn`: the contents of the array.

### Output

Output `n-k+1` values: the costs.

### Constraints

- `1 <= k <= n <= 2e5`
- `1 <= xi <= 1e9`

### Examples

**Input**
```
8 3
2 4 3 5 8 1 2 1
```

**Output**
```
2 2 5 7 7 1
```

---

## What this problem really is

For each window, min `sum |a[i] - t|` over choice of target `t`. On a line, optimal `t` is a median; cost is sum of absolute deviations from that median.

## What to use

| Tool | Why |
|------|-----|
| Two multisets + sum of each half | Same split as sliding median |
| Cost formula | `sum_hi - sum_lo` adjusted for odd/even sizes (all go to median) |
| 64-bit | Costs up to `k * 1e9` |

## Approach

**Root idea:** maintaining the sliding median structure already partitions into values `<= median` and `>= median`. The equalize-to-median cost is `(sum of high half) - (sum of low half)` with a correction so both sides meet at the median (subtract/add `median * size` appropriately).

**Mental picture:** pull every number in the window to the median height; total rope length is the cost. As the window slides, update the two piles and their weight sums.

**How that becomes the method**

1. Maintain `lo` (max-side multiset) and `hi` (min-side) with running `sumLo`, `sumHi`, sizes as for median.
2. After balance, let `m` be the median (`*lo.rbegin()`).
3. Cost = `sumHi - sumLo + m * (|lo| - |hi|)` (equivalent forms: `sum |x-m| = sumHi - sumLo - m*(|hi|-|lo|)` depending on which half contains `m` — derive once from “all in lo are `<=m`, all in hi are `>=m`”).
4. Slide with insert/erase updating sums; print cost each time.

**Data structures:** dual multisets + two `long long` sums; or PBDS with fenwick on compressed values for sum/order.

## Walkthrough (on a sample)

Window `[2,4,3]`: median `3`, cost `|2-3|+|4-3|+0=2`. Later windows match the sample row.

## Pitfalls

- Optimizing to the mean (wrong for L1).  
- 32-bit cost.  
- Sum not updated on rebalance moves between `lo` and `hi`.

## Complexity

- Time: `O(n log k)`  
- Space: `O(k)`

## Key takeaway

**Min equalize cost in a window = L1-to-median**; maintain sliding median halves **and their sums**.
