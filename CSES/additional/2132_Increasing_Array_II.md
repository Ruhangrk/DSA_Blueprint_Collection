# [2132 — Increasing Array II](https://cses.fi/problemset/task/2132)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems II  
**Tags:** Slope trick, greedy with heap, non-decreasing sequence, minimum L1 cost  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array of `n` integers. You want to make it non-decreasing. Each move increases or decreases one element by `1`. Find the minimum number of moves.

### Input

- The first line has `n`.
- The second line has `x[1..n]`.

### Output

Print the minimum number of moves.

### Constraints

- `1 <= n <= 2e5`
- `1 <= x[i] <= 1e9`

### Examples

**Input**
```
5
3 8 5 6 5
```

**Output**
```
4
```

---

## What this problem really is

We want new values `y[i]` that are non-decreasing and close to `x[i]`, minimizing the total distance `sum |x[i] - y[i]|`. This is a classic "make the sequence sorted with minimum total change" problem.

## What to use

| Tool | Why |
|------|-----|
| Max-heap of "breakpoints" | Remembers the values the current prefix is allowed to be capped at |
| Greedy fix on each new element | When a new element is too small for a previous high value, pay the difference and lower that high value |

## Approach

**Root idea:** scan from left to right. Keep the current prefix's answer as a set of "peaks" in a max-heap. The top of the heap is the largest value the prefix currently wants to be capped at. When a new element `x` is smaller than that top, the cheapest fix is to lower the top down to `x`. That costs `top - x`. The top is removed, and `x` takes its place, since lowering the top to `x` is what makes the prefix consistent.

**Mental picture:** imagine stacking blocks by height. Each block must sit no lower than the one before it. When a new block is too short, you either shave the tallest block down to its height (paying the shaved amount) or leave it. Shaving the tallest block is always the cheapest way to fit, and it leaves a shorter, reusable stack.

**How that becomes the method**

1. Keep a max-heap `h`, and a counter `ans = 0`.
2. For each `x` in order:
   - Push `x` into `h`.
   - If `h.top() > x`: add `h.top() - x` to `ans`, pop the top, and push `x` again.
3. Print `ans`.

Each step either pushes a value, or moves the top down. The sum of the moves equals the minimum total change.

## Walkthrough (on a sample)

`x = 3 8 5 6 5`. Heap (max on top) and `ans`.

| `x` | Push | Top before check | Top > x? | Action | Heap after | `ans` |
|---|---|---|---|---|---|---|
| 3 | 3 | 3 | no | none | `{3}` | 0 |
| 8 | 8 | 8 | no | none | `{3, 8}` | 0 |
| 5 | 5 | 8 | yes | `ans += 8 - 5 = 3`, pop 8, push 5 | `{3, 5, 5}` | 3 |
| 6 | 6 | 6 | no | none | `{3, 5, 5, 6}` | 3 |
| 5 | 5 | 6 | yes | `ans += 6 - 5 = 1`, pop 6, push 5 | `{3, 5, 5, 5, 5}` | 4 |

Final answer `4`. Matches.

Check by building the result: the best non-decreasing sequence is `3, 5, 5, 5, 5`. Its cost is `|3-3| + |8-5| + |5-5| + |6-5| + |5-5| = 0 + 3 + 0 + 1 + 0 = 4`. ✓

## Pitfalls

- Using a min-heap by mistake. The top must be the largest value so far.
- Forgetting to push `x` again after popping. The new value must stay in the heap as a breakpoint.
- Checking `>=` instead of `>`. With equal values there is nothing to fix.
- Overflow in `ans`. The total can be about `2e5 * 1e9`, so use 64-bit.
- Reading the problem as "strictly increasing". The statement says non-decreasing, so equal neighbors are fine.

## Complexity

- Time: `O(n log n)`.
- Space: `O(n)`.

## Key takeaway

To make a sequence non-decreasing with minimum total change, keep a max-heap of breakpoints. Each time a value is too small, lower the highest breakpoint to it and pay the difference.
