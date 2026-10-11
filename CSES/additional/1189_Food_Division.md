# [1189 — Food Division](https://cses.fi/problemset/task/1189)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems II  
**Tags:** Circular flow, prefix sums, median, greedy on a cycle  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

`n` children sit around a round table. Child `i` has `a[i]` food and wants `b[i]`. The totals match. Each step, a child can give one unit of food to a neighbor. Find the minimum number of steps.

### Input

- The first line has `n`.
- The second line has `a[1..n]`.
- The third line has `b[1..n]`.

### Output

Print the minimum number of steps.

### Constraints

- `1 <= n <= 2e5`
- `0 <= a[i], b[i] <= 1e6`

### Examples

**Input**
```
3
3 5 0
2 4 2
```

**Output**
```
2
```

**Explanation:** Child 1 gives one unit to child 3, and child 2 gives one unit to child 3.

---

## What this problem really is

Each unit of food passing from one neighbor to the next costs one step. So the total steps equal the sum of the absolute amounts of food flowing across each of the `n` edges of the circle. We need to choose the flows so that every child ends with the right amount, while minimizing the sum of absolute flows.

## What to use

| Tool | Why |
|------|-----|
| Surplus `d[i] = a[i] - b[i]` | Tells how much each child must pass on |
| Prefix sums `P[i]` | Once the flow across one edge is fixed, every other edge's flow is determined by its prefix sum |
| Median of prefix sums | Minimizes the sum of absolute differences, which is exactly the cost |

## Approach

**Root idea:** let `x[i]` be the net flow from child `i` to child `i+1` (with child `n+1` meaning child `1`). Child `i` ends with `a[i] - x[i] + x[i-1] = b[i]`, so `x[i] = x[i-1] + d[i]`. Going around the circle, every flow is the same starting value `c` plus a prefix sum: `x[i] = c + P[i]`, where `P[i] = d[1] + ... + d[i]`. The only unknown is `c`, the flow across the edge `n -> 1`.

The cost is `sum |c + P[i]|`. The value of `c` that minimizes this is the negative median of the `P[i]`.

**Mental picture:** picture the circle as `n` slots, with a flow on each boundary between slots. Each child either pushes food forward or pulls it from behind, so the flows form a wave that shifts up or down by `d[i]` at each child. Shifting the whole wave by `c` changes every boundary by the same amount. The best shift lines the wave up around its middle, which is the median.

**How that becomes the method**

1. Compute `d[i] = a[i] - b[i]`.
2. Compute prefix sums `P[1..n]` (`P[n]` is `0`, since totals match).
3. Find the median `m` of the values `P[1..n]`.
4. Answer = `sum |P[i] - m|`.

**Data structures:** an array of `n` prefix sums, and a sort (or `nth_element`) to find the median.

## Walkthrough (on a sample)

`a = 3 5 0`, `b = 2 4 2`.

Surplus: `d = 1, 1, -2`.

Prefix sums: `P = 1, 2, 0`.

Median of `{1, 2, 0}`: `1`.

Cost: `|1 - 1| + |2 - 1| + |0 - 1| = 0 + 1 + 1 = 2`. Matches.

Check the flows: with `c = -1`, the flows are `x = P - 1 = 0, 1, -1`. So the edge `1 -> 2` carries `0`, the edge `2 -> 3` carries `1` (child 2 gives to child 3), and the edge `3 -> 1` carries `-1` (child 1 gives one unit to child 3). This matches the explanation.

## Pitfalls

- Using the mean instead of the median. The cost is a sum of absolute values, so the median is the minimizer.
- Forgetting that the cost counts absolute flows. A negative flow means food moves the other way and still costs steps.
- Overflow. Prefix sums can reach about `2e5 * 1e6 = 2e11`, so use 64-bit integers.
- Mixing up the direction convention. Flow from `i` to `i+1` is the only thing to track, and the sign carries the direction.

## Complexity

- Time: `O(n log n)` (sort for the median).
- Space: `O(n)`.

## Key takeaway

On a cycle, all edge flows are a shared shift plus fixed prefix sums. The shift that minimizes total absolute flow is the median.
