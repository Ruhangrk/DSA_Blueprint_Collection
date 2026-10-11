# [3223 — Sliding Window Inversions](https://cses.fi/problemset/task/3223)

**Platform:** CSES  
**Source:** CSES Problem Set — Sliding Window Problems  
**Tags:** sliding window, Fenwick, inversions  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array of `n` integers. Your task is to calculate the number of inversions in each window of `k` elements, from left to right.

An inversion is a pair of elements where the left element is larger than the right element.

### Input

The first line contains two integers `n` and `k`: the number of elements and the size of the window.

Then there are `n` integers `x1, x2, ..., xn`: the contents of the array.

### Output

Print `n-k+1` values: the numbers of inversions.

### Constraints

- `1 <= k <= n <= 2e5`
- `1 <= xi <= 1e9`

### Examples

**Input**
```
8 3
1 2 3 2 5 2 4 4
```

**Output**
```
0 1 1 1 2 0
```

---

## What this problem really is

Inversion count inside every length-`k` subarray — pairs `(i,j)` with `i<j` in the window and `a[i]>a[j]`.

## What to use

| Tool | Why |
|------|-----|
| Fenwick / segment tree of frequencies | Order-statistic counts |
| Coordinate compression | Values up to `1e9` |
| Slide update | Add new right element; remove old left; adjust inversion total |

## Approach

**Root idea:** maintain a BIT of values present in the window. When inserting `v` at the right, it forms an inversion with every already-present value `> v` — add `count_greater(v)`. When deleting `u` from the left, remove inversions where `u` was the left element: that is all still-present values `< u` to its right — which is exactly `count_smaller(u)` among remaining elements (everything else in the window is to the right of the leaving index).

**Mental picture:** a bag of window values in a tree that can answer “how many bigger/smaller than `v`?”. Newcomer asks how many bigger already sit in the bag; leaver asks how many smaller remain (those were its inversion partners).

**How that becomes the method**

1. Compress all `a[i]` to ranks `1..n`.
2. BIT supports `add(rank, +1/-1)` and prefix sums.
3. Build first window: for `i=0..k-1` in order, `inv += query(rank_max) - query(rank[i])` (count greater), then `add(rank[i],1)`.
4. Print `inv`. For each new `r`, remove `a[r-k]`: `inv -= query(rank[leaving]-1)` (smaller count), `add(rank[leaving],-1)`; then insert `a[r]` as above; print.

**Data structures:** Fenwick tree size `n`; `long long inv`.

## Walkthrough (on a sample)

Sorted-ish windows start at `0` inversions; as larger-before-smaller pairs appear, counts become `1,1,1,2,0`.

## Pitfalls

- Wrong direction when removing (greater vs smaller).  
- Not compressing ties consistently.  
- 32-bit inversion counter.

## Complexity

- Time: `O(n log n)`  
- Space: `O(n)`

## Key takeaway

**Sliding inversions = Fenwick of window frequencies** — add count-greater on insert, subtract count-smaller on delete.
