# [3402 — Minimum Cost Pairs](https://cses.fi/problemset/task/3402)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems II  
**Tags:** Sorting, greedy, heap, linked list, convex cost  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an array of `n` integers, choose `k` disjoint pairs. Each number belongs to at most one pair. The cost of a pair `(a, b)` is `|a - b|`. For each `k = 1..floor(n/2)`, print the minimum total cost.

### Input

- The first line has `n`.
- The second line has `x[1..n]`.

### Output

Print `floor(n/2)` integers: the minimum cost for `k = 1, ..., floor(n/2)`.

### Constraints

- `2 <= n <= 2e5`
- `1 <= x[i] <= 1e9`

### Examples

**Input**
```
8
3 1 2 7 9 3 4 7
```

**Output**
```
0 0 1 6
```

---

## What this problem really is

After sorting, the question becomes: which `k` disjoint *neighbouring* pairs have the smallest total gap? Once the array is sorted, pairing values that are neighbours is always at least as good as any other pairing, so the costs become simple numbers on a line that we pick from.

## What to use

| Tool | Why |
|------|-----|
| Sort the array | Makes "closest values" mean "neighbouring positions" |
| Gap array `c[i] = x[i+1] - x[i]` | The cost of pairing `x[i]` with `x[i+1]` |
| Picking non-adjacent gaps | Two picked gaps may not share an element, so they must not be neighbours in the gap array |
| Heap plus linked list | Greedy choice with the ability to undo a bad early pick |

## Approach

**Root idea:** after sorting, a valid set of `k` pairs is a set of `k` gaps `c[i]` (pairing `x[i]` and `x[i+1]`) where no two chosen gaps are neighbours. Choosing `k` non-adjacent items from a line with the smallest total value is a known problem with a greedy solution.

**Mental picture:** lay the gaps `c[1..m]` out on a line, `m = n-1`. You may select a gap only if you do not select its left or right neighbour. Each time you take the cheapest gap, you lose its two neighbours. Instead of permanently losing them, the greedy replaces the taken gap with a single "merged" value `left + right - taken`. Later, if that merged value is the cheapest, it means "undo the earlier choice and take both neighbours instead".

**Method**

1. Sort `x`. Build gaps `c[1..m]`, `m = n-1`.
2. Store the gaps in a doubly linked list, with a min-heap of `(value, index)`.
3. Repeat `k` times, for `k = 1..floor(n/2)`:
   - Pop the smallest alive entry `(v, i)`. Skip it if it was removed or its value changed.
   - Add `v` to the running total. This is the answer for the current `k`.
   - Let `L` and `R` be the alive neighbours of `i`.
   - If `L` or `R` is missing, set `c[i] = INF`. Otherwise set `c[i] = c[L] + c[R] - v`.
   - Remove `L` and `R` from the list, and push the new `c[i]` into the heap.
4. Print the running totals.

## Walkthrough (on the sample)

Sorted: `1 2 3 3 4 7 7 9`. Gaps `c[1..7]`:

| i | pair | c[i] |
|---|------|------|
| 1 | (1,2) | 1 |
| 2 | (2,3) | 1 |
| 3 | (3,3) | 0 |
| 4 | (3,4) | 1 |
| 5 | (4,7) | 3 |
| 6 | (7,7) | 0 |
| 7 | (7,9) | 2 |

Greedy steps (alive list shown after each step):

| Step | Pick (value) | Running total = answer for k | Update | Alive after step |
|------|--------------|------------------------------|--------|------------------|
| 1 | gap 3 (0) | 0 | c[3] = c[2]+c[4]-0 = 2; remove 2, 4 | 1(1), 3(2), 5(3), 6(0), 7(2) |
| 2 | gap 6 (0) | 0 | c[6] = c[5]+c[7]-0 = 5; remove 5, 7 | 1(1), 3(2), 6(5) |
| 3 | gap 1 (1) | 1 | no left neighbour, so c[1] = INF; remove 3 | 1(INF), 6(5) |
| 4 | gap 6 (5) | 6 | left neighbour 1 is removed, no right neighbour, so c[6] = INF | 6(INF) |

Answers: `0 0 1 6`. Matches the expected output.

**Reading step 2:** gap 6 is the pair (7,7). Its merged value 5 means "undo this pair and use (4,7) and (7,9) instead, costing 3 + 2 = 5". That merged value is only taken later if it is the cheapest remaining choice, which is exactly what happens at step 4.

## Pitfalls

- Forgetting to sort. The gap method only works on sorted values.
- Using the non-adjacent gap rule incorrectly. Picked gaps must not be neighbours in the gap array.
- Overflow or wrong values when a neighbour is missing. Use `INF` for that case and do not add to it.
- Stale heap entries. A popped entry may have been removed or updated; check it is still alive and its value still matches.
- Using 64-bit integers. Costs can reach about `2e5 * 1e9`.

## Complexity

- Time: `O(n log n)` for sorting plus heap operations.
- Space: `O(n)`.

## Key takeaway

Sorting turns the pairing problem into choosing non-adjacent gaps. Picking the cheapest gap greedily, and replacing it with a "merged" value that can later be undone, gives the optimal answer for every `k` in one pass.
