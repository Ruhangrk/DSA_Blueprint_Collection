# [1747 — Pyramid Array](https://cses.fi/problemset/task/1747)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems I  
**Tags:** Inversions, adjacent swaps, Fenwick tree (BIT), greedy decomposition  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array of `n` distinct integers. One move swaps two adjacent values. Transform the array into a pyramid array: increasing up to a peak, then decreasing. The array may also be only increasing or only decreasing.

Find the minimum number of moves.

### Input

- The first line has `n`.
- The next line has `x[1], ..., x[n]`, all distinct.

### Output

Print one integer: the minimum number of moves.

### Constraints

- `1 <= n <= 2e5`
- `1 <= x[i] <= 1e9`

### Examples

**Input**
```
4
2 1 5 3
```

**Output**
```
1
```

**Explanation:** Swap the first two values to get `1 2 5 3`, which is a pyramid.

---

## What this problem really is

Adjacent swaps sort a sequence by removing inversions, one pair at a time. The minimum number of swaps to reach a target arrangement equals the number of pairs whose relative order differs from the target. So the task is to pick the target pyramid that changes the fewest pairs.

## What to use

| Tool | Why |
|------|-----|
| Largest value is the peak | In any pyramid, the maximum sits at the top |
| Each smaller value goes to the left or right side | This is what defines a pyramid shape |
| Pair rule | For any two values `x < y`, the final order depends only on which side `x` chose |
| Fenwick tree (BIT) | Counts positions of larger values in `O(log n)` |

## Approach

**Root idea:** in a pyramid, the maximum is the peak. Every other value `x` is placed on the left side (it will be left of the peak) or on the right side (it will be right of the peak). For two values `x < y`, the final relative order is decided by `x` alone:
- if `x` is on the left, `x` ends up left of `y`;
- if `x` is on the right, `x` ends up right of `y`.

So each value can choose its side independently, and the cost splits into independent pieces.

**Mental picture:** imagine every value sitting on a pair of stairs. Small values stand at the outside edges, and the peak stands in the middle. The small value picks a side, and that choice fixes its order against every bigger value. The number of swaps needed is the number of bigger values whose original order it would violate.

**How that becomes the method**

1. For each value `x` other than the maximum, count:
   - `L[x]` = number of larger values `y` located to the left of `x` in the original array,
   - `R[x]` = number of larger values `y` located to the right of `x`.
2. If `x` goes left, the pairs `(x, y)` with `y` to the left of `x` become inversions, which costs `L[x]`.
3. If `x` goes right, the pairs with `y` to the right of `x` cost `R[x]`.
4. Each value takes the cheaper side: add `min(L[x], R[x])`.
5. The answer is the sum over all non-maximum values.

To get `L` and `R`, process values from largest to smallest and insert each position into a BIT. When handling `x`, the BIT holds positions of all larger values. `R[x]` is the count of inserted positions greater than `pos[x]`, and `L[x]` is the count less than `pos[x]`.

**Data structures:**
- `pos[v]`: original index of value `v`. Use coordinate compression on values if needed.
- Fenwick tree over positions `1..n`.

## Walkthrough (on a sample)

`x = 2 1 5 3`, so `n = 4`. Positions: `value 2 at 1`, `value 1 at 2`, `value 5 at 3`, `value 3 at 4`. The maximum is `5`.

Process values from largest to smallest (skip the maximum `5` as a choice, but insert it):

- Insert `5` at position `3`. BIT: `{3}`.
- Value `3` (position `4`): inserted positions `{3}`. `L = count < 4 = 1`, `R = count > 4 = 0`. `min = 0`. Insert `4`. BIT: `{3,4}`.
- Value `2` (position `1`): inserted `{3,4}`. `L = count < 1 = 0`, `R = count > 1 = 2`. `min = 0`. Insert `1`. BIT: `{1,3,4}`.
- Value `1` (position `2`): inserted `{1,3,4}`. `L = count < 2 = 1` (position 1), `R = count > 2 = 2` (positions 3, 4). `min = 1`. Insert `2`.

Total = `0 + 0 + 1 = 1`. Matches.

Check: value `1` takes the left side (cost `1`): the target is `1 2 5 3`. The swap of the first two values is exactly that one move.

## Pitfalls

- Counting the maximum as a choice. It is the peak and has no side.
- Using only "all left" or "all right". The best answer is a mix, so take the min per value.
- Mixing up `L` and `R`. `L` counts larger values to the left of `x` in the original array, and `R` counts those to the right.
- Forgetting that values are distinct, so positions are unique and the BIT logic is correct.
- Using `O(n^2)` inversion counting. With `n = 2e5` it is too slow.

## Complexity

- Time: `O(n log n)`.
- Space: `O(n)`.

## Key takeaway

The minimum adjacent swaps to reach a target order is the number of pairs whose order changes. When each element's final side fixes all its pair orders, every element can be decided independently with a simple min.
