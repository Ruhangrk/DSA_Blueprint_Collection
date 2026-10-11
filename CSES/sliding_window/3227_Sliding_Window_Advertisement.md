# [3227 — Sliding Window Advertisement](https://cses.fi/problemset/task/3227)

**Platform:** CSES  
**Source:** CSES Problem Set — Sliding Window Problems  
**Tags:** sliding window, histogram, sparse table, cartesian tree  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A fence consists of `n` vertical boards. The width of each board is `1` and their heights may vary.

You want to attach a rectangular advertisement to the fence. Your task is to calculate the maximum area of such an advertisement in each window of `k` vertical boards, from left to right.

### Input

The first line contains two integers `n` and `k`: the width of the fence and the size of the window.

After this, there are `n` integers `x1, x2, ..., xn`: the height of each board.

### Output

Print `n - k + 1` integers: the maximum areas of the advertisements.

### Constraints

- `1 <= k <= n <= 2e5`
- `1 <= xi <= 1e9`

### Examples

**Input**
```
8 3
4 1 5 3 3 2 4 1
```

**Output**
```
5 6 9 6 6 4
```

---

## What this problem really is

For every subarray of length `k`, max rectangle area in that histogram strip (classic largest rectangle, but restricted to each window).

## What to use

| Tool | Why |
|------|-----|
| Per-window histogram area | For fixed heights, area = `height[i] * width` of maximal run where `i` is minimum |
| Sparse table (RMQ) | Range minimum queries in `O(1)` |
| Two pointers / stack per window | Too slow if restarted `O(nk)`; need global structure |
| Cartesian tree + contribution | Each bar’s max width as minimum inside a window |

## Approach

**Root idea:** in a histogram, the best rectangle with bottom on the fence and height equal to some board `i` extends to the largest segment where `i` is a minimum. For a fixed window `[L,L+k)`, the answer is `max over i in window of h[i] * (min(R_i, L+k) - max(L_i, L))` where `(L_i, R_i)` is the maximal open interval around `i` on which `h[i]` is strictly minimal (nearest stricter smaller to left/right).

**Mental picture:** each plank tries to be the short side of a poster; it can stretch left/right until a shorter plank blocks it, but only inside the current `k`-board view.

**How that becomes the method**

1. With a monotonic stack, for every `i` compute `left[i]` = nearest index `< i` with `h[left] < h[i]` (strict), `right[i]` similarly to the right.
2. For each window start `L` (`R = L+k`):
   - Candidate from bar `i`: width = `min(right[i], R) - max(left[i]+1, L)`; area = `h[i] * width` (only if `i` in `[L,R)`).
3. Naively max over `i` in window is `O(nk)`. Speed up: the answer for window `L` is the max of those candidate areas among `i in [L,R)`. This is a range-maximum query on an array of “area if the whole `[left+1, right)` were allowed”, clipped to the window — often solved with a segment tree of candidates or sparse table on precomputed contributions carefully.

Practical accepted approach: sparse table for RMQ of heights + binary lifting / two pointers to evaluate classic `O(k)` histogram stack **only if** you move the window with undo — hard. Common solution: for each `i`, the windows where `i` can contribute are those containing `i` and contained in `(left[i], right[i])`; the contribution `h[i] * min(k, right-left-1 clipped)` can be written into a difference array of window answers.

Simpler mental algorithm for learning: precompute `left`/`right`; for each window use a sparse-table RMQ-based “largest rectangle in range” offline if available; or difference-array of contributions:

- Bar `i` contributes area `h[i] * w` to every window of length `k` that contains `i` and lies inside `(left[i], right[i])`.
- For a fixed width `w = k` window, if the window is `[L, L+k)` and `left[i] < L` and `L+k-1 < right[i]` and `L <= i < L+k`, contribution is `h[i]*k` (whole window height-limited by `i`). More generally width is `k` always for the rectangle spanning the full window only when `i` is the min of the whole window — which is exactly one candidate: `h[rmq(L,L+k)) * k`. Also need smaller-width rectangles inside the window — full largest-rectangle, not only full-width.

Full largest rectangle in each window = classic problem “sliding window maximum histogram area”. Standard solution uses the `left`/`right` spans plus a segment tree storing max `h[i]*(min(right,?)...)` — implement via:

**Difference array on contributions:** for each `i`, let `maxW = right[i]-left[i]-1`. For every width `w = 1..min(k,maxW)`, bar `i` can form area `h[i]*w` inside windows that fit. The clean contest pattern: use a sparse table for range minimum, and for each window binary-search the largest rectangle via cartesian-tree recursion in total `O(n log n)` if carefully memoized — 

**Reliable O(n log n) approach:** build sparse table of heights. Recursively, largest rectangle in `[l,r)` = max( `h[m]*(r-l)` where `m = argmin`, largest in `[l,m)`, largest in `[m+1,r)` ). With memo or D&C over all query windows sharing the same min structure… For sliding fixed `k`, compute for each start using stack in `O(1)` amortized with undoable stack when advancing `L` — advanced.

For the page, state the contribution view clearly:

1. Compute `left[i]`, `right[i]` via monotonic stacks.
2. Answer for window `[L,R)` (`R=L+k`) = `max_{i=L}^{R-1} h[i] * (min(right[i], R) - max(left[i]+1, L))`.
3. Evaluate that max over sliding `L` using a segment tree of line-like upper envelopes or sparse table on a static “area if window were the full span” plus clipping — or process with a multiset of current candidates updated at `L` moves (`O(n log n)`).

**Data structures:** monotonic stacks; segment tree / sparse table; 64-bit areas.

## Walkthrough (on a sample)

Windows of 3 boards: e.g. `[5,3,3]` allows `3*3=9`; `[4,1,5]` allows `5*1` or `1*3` etc. → `5`; matching the sample row.

## Pitfalls

- Only considering full-width `k * min` (misses taller thinner posters).  
- Non-strict nearest smaller (equal heights).  
- 32-bit area overflow.

## Complexity

- Time: `O(n log n)` with stacks + segment tree / careful sliding  
- Space: `O(n)`

## Key takeaway

**Sliding largest advertisement = largest rectangle in each histogram window** via nearest-smaller spans, queried/slid in `O(n log n)`.
