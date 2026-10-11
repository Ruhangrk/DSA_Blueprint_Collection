# [1076 — Sliding Window Median](https://cses.fi/problemset/task/1076)

**Platform:** CSES  
**Source:** CSES Problem Set — Sliding Window Problems  
**Tags:** sliding window, two heaps, policy PBDS  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array of `n` integers. Your task is to calculate the median of each window of `k` elements, from left to right.

The median is the middle element when the elements are sorted. If the number of elements is even, there are two possible medians and we assume that the median is the smaller of them.

### Input

The first line contains two integers `n` and `k`: the number of elements and the size of the window.

Then there are `n` integers `x1, x2, ..., xn`: the contents of the array.

### Output

Print `n-k+1` values: the medians.

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
3 4 5 5 2 1
```

---

## What this problem really is

Median of every length-`k` window: order statistic at index `(k-1)/2` in the sorted window (0-based) — i.e. lower median when `k` even.

## What to use

| Tool | Why |
|------|-----|
| Two multisets / heaps (`lo`, `hi`) | `lo` holds smaller half (max at rbegin); balance sizes |
| Indexed BST (PBDS) | Direct `find_by_order((k-1)/2)` |
| Lazy deletion | Soft-delete when sliding out |

## Approach

**Root idea:** keep the window ordered in two halves so the median sits at the top of the low half. Rebalance after each insert/erase so `|lo| = (k+1)/2`, `|hi| = k/2`.

**Mental picture:** a sorted line of `k` beads; the median is the middle-left bead. Two bowls hold the left and right halves; you always read the largest bead in the left bowl.

**How that becomes the method**

1. Insert into `lo` or `hi` comparing with current median; rebalance sizes.
2. To remove `x[i-k]`: erase from the multiset that contains it (or lazy-count).
3. Print `*lo.rbegin()` each window.

PBDS alternative: one ordered multiset of pairs `(value, index)`; median = `*find_by_order((k-1)/2)`.

**Data structures:** `multiset` pair / dual heaps with delayed deletion; or `__gnu_pbds::indexed_set`.

## Walkthrough (on a sample)

Windows of 3: medians `3,4,5,5,2,1` match the sorted middle of each triple.

## Pitfalls

- Upper median when `k` even (statement wants the smaller).  
- Multiset erase-by-value removing all duplicates — erase one iterator.  
- Size imbalance after removals.

## Complexity

- Time: `O(n log k)`  
- Space: `O(k)`

## Key takeaway

**Sliding median = two-heap / ordered-statistic structure** on the window; lower median when even.
