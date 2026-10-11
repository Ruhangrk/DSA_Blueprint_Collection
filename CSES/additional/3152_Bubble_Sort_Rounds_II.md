# [3152 — Bubble Sort Rounds II](https://cses.fi/problemset/task/3152)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems I  
**Tags:** bubble sort, order-statistic tree, reconstruction  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Bubble sort is a sorting algorithm that consists of a number of rounds. On each round the algorithm scans the array from left to right and swaps any adjacent elements that are in the wrong order.

Given an array of `n` integers, find out the contents of the array after `k` bubble sort rounds.

### Input

The first line has two integers `n` and `k`: the array size and the number of rounds.

The next line has `n` integers `x1,x2,...,xn`: the array contents.

### Output

Print `n` integers: the contents of the array after `k` rounds.

### Constraints

- `1 <= n <= 2e5`
- `0 <= k <= 1e9`
- `1 <= xi <= 1e9`

### Examples

**Input**
```
5 2
3 2 4 1 4
```

**Output**
```
2 1 3 4 4
```

---

## What this problem really is

Simulate `k` bubble-sort passes without `O(nk)` work — `k` can be `1e9`, so jump to the configuration after `min(k, rounds_needed)` logically.

## What to use

| Tool | Why |
|------|-----|
| Same left-move rule as Rounds I | After `t` rounds, each element has moved left by at most `t` |
| Cap `k` by total rounds | If `k` ≥ rounds from problem I, output the fully sorted array |
| Order-statistic / greedy place | Reconstruct positions consistent with “moved left ≤ k” and stability |

## Approach

**Root idea:** after exactly `t` rounds, every value has advanced at most `t` seats toward its stable final index from the left, and the relative order is the one bubble sort would produce. If `t` is huge, the array is fully sorted.

**Mental picture:** each round, small keys take one step left through the line. After `k` steps, nobody has walked more than `k` seats left of where they started; larger keys have been pushed right accordingly.

**How that becomes the method**

1. Compute stable final positions of all elements (sort pairs `(value, index)`).
2. Let `R` be the answer to Bubble Sort Rounds I (max left distance). If `k >= R`, print the stably sorted array.
3. Otherwise, reconstruct the array after `k` rounds. One standard way:
   - Process positions `0..n-1` left to right;
   - Maintain the multiset / Fenwick of elements not yet placed, keyed by `(value, originalIndex)`;
   - The element that ends up at position `p` after `k` rounds is determined by: among elements whose original index `i` satisfies `i <= p+k` (they could have reached `p` by moving left at most `k`), pick the one that belongs earliest in the stable sorted order among those still available.
4. More explicit formula used in tutorials: for each original element at `i` with final index `f`, after `k` rounds it sits at `max(f, i-k)`, then resolve collisions by the stable bubble order (implement via the order-statistic scan above).

**Data structures:** sorted pairs; Fenwick/policy-based GNU `indexed_set` or PBDS for “k-th remaining”; output vector.

## Walkthrough (on a sample)

`k=2 < 3` rounds total. After 2 rounds the statement shows `[2,1,3,4,4]` — `1` has moved from index 3 to 1 (`2` steps), `2` reached the front, large `4`s settled right.

## Pitfalls

- Simulating `k` passes when `k=1e9`.
- Unstable sorting for ties.
- Wrong reachability `i <= p+k` off-by-one.

## Complexity

- Time: `O(n log n)`
- Space: `O(n)`

## Key takeaway

After `k` bubble rounds, rebuild with “each element moved left by at most `k`” (or fully sorted if `k` is large), using an order-statistic structure for `O(n log n)` reconstruction.
