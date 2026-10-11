# [3108 — K Subset Sums I](https://cses.fi/problemset/task/3108)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems II  
**Tags:** Subset sums, sorting, min-heap enumeration, shifting negatives  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given `n` integers. Consider the sums of all `2^n` subsets (the empty subset has sum `0`). Print the `k` smallest subset sums in increasing order.

### Input

- The first line has `n` and `k`.
- The next line has `x[1..n]`.

### Output

Print `k` integers: the `k` smallest subset sums in increasing order.

### Constraints

- `1 <= n <= 2e5`
- `1 <= k <= min(2^n, 2e5)`
- `-1e9 <= x[i] <= 1e9`

### Examples

**Input**
```
4 9
1 6 3 -3
```

**Output**
```
-3 -2 0 0 1 1 3 3 4
```

---

## What this problem really is

Subset sums can be negative, which makes "smallest" tricky. But we can shift the problem so every item is non-negative, and then the smallest sums come out of a simple heap walk.

## What to use

| Tool | Why |
|------|-----|
| Shift negatives | Turns every item into a non-negative weight plus one fixed offset |
| Sorting the weights | Lets the heap walk add the next-largest item in order |
| Min-heap of states | Pops the next smallest subset sum each time, `O(log k)` per pop |
| Two child moves per state | "Add the next item" and "swap the last item for the next one" reach every subset exactly once |

## Approach

**Step 1, remove negatives.** Let `B` be the sum of all negative `x[i]`. For each item, define a weight: `w = x` for non-negative items, and `w = -x` for negative items. Then every subset sum equals `B` plus the sum of some subset of the weights. (Reason: including a negative item `x` contributes `x = -|x|`, which is the same as starting with all negatives included in `B`, then removing `|x|` from the total when that item is left out. So leaving a negative item out adds `|x|` back to the base. Each subset of weights corresponds to one subset of items, and the mapping is one-to-one.)

**Step 2, enumerate subset sums of non-negative weights in increasing order.** Sort the weights `w[0] <= w[1] <= ... <= w[n-1]`. Each state is `(sum, i)`, where `i` is the largest index chosen so far. Start with the empty subset (sum `0`) as the first output. Then push the state `(w[0], 0)`.

From state `(s, i)`, when `i + 1 < n`, there are two children:
- **Add:** `(s + w[i+1], i+1)`, keeping the old items and adding `w[i+1]`.
- **Swap:** `(s - w[i] + w[i+1], i+1)`, removing `w[i]` and adding `w[i+1]`.

Pop from the min-heap `k - 1` more times (the empty subset is already output).

**Why each subset appears exactly once:** every non-empty subset with largest index `j` has exactly one parent. If index `j-1` is in the subset, the parent is the subset without `j`, reached by "add". Otherwise the parent is the subset with `j` replaced by `j-1`, reached by "swap". Following parents always leads back to a singleton, and then to the empty subset, so each subset is produced once.

**Step 3, shift back.** Add `B` to each popped sum.

**Data structures:**
- Sorted array `w`.
- Min-heap of `(sum, last index)`, at most about `2k` entries.

## Walkthrough (on a sample)

`x = 1 6 3 -3`, `k = 9`.

Negatives: only `-3`, so `B = -3`. Weights: `1, 6, 3, 3` (the `-3` becomes `3`). Sorted `w = [1, 3, 3, 6]`.

Output the empty subset: `0`. Push `(1, 0)`.

| Pop | Sum | Last index | Children pushed |
|---|---|---|---|
| 1 | 1 | 0 | add `(4, 1)`, swap `(3, 1)` |
| 2 | 3 | 1 | add `(6, 2)`, swap `(3, 2)` |
| 3 | 3 | 2 | add `(9, 3)`, swap `(6, 3)` |
| 4 | 4 | 1 | add `(7, 2)`, swap `(4, 2)` |

Pops so far: `0, 1, 3, 3, 4`. The next pops are `4, 6, 6, 7`, giving the nine smallest subset sums `0, 1, 3, 3, 4, 4, 6, 6, 7`.

Check against all subsets of `{1, 3, 3, 6}`: the sums in order are `0, 1, 3, 3, 4, 4, 6, 6, 7, 7, 9, 9, 10, 10, 12, 13`. The first nine match.

Add `B = -3` to each: `-3, -2, 0, 0, 1, 1, 3, 3, 4`. Matches the sample.

## Pitfalls

- Forgetting the empty subset. It has sum `0` (plus `B` after shifting) and must be printed first.
- Dropping the shift. Negative weights break the heap order if you do not shift them.
- Getting the shift direction wrong. Leaving a negative item out adds its absolute value back, so the weight is `|x|`.
- Generating duplicates. Only the add and swap moves from the last index are allowed; do not add other moves.
- Overflow. Sums can reach about `2e14` in magnitude, so use 64-bit integers.

## Complexity

- Time: `O(n log n + k log k)`.
- Space: `O(n + k)`.

## Key takeaway

Shift the problem so every item is non-negative and sorted. Then the smallest subset sums come from a heap that only ever adds the next item or swaps the last item for the next one.
