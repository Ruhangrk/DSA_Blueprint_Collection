# [3404 — Permutation Subsequence](https://cses.fi/problemset/task/3404)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems I  
**Tags:** Longest common subsequence, permutations, longest increasing subsequence, reconstruction  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given two arrays that are permutations, find their longest common subsequence. Print its length, then one such subsequence.

### Input

- The first line has `n` and `m`, the sizes of the arrays.
- The second line has `a[1..n]`, a permutation of `1..n`.
- The third line has `b[1..m]`, a permutation of `1..m`.

### Output

First the length. Then one longest common subsequence.

### Constraints

- `1 <= n, m <= 2e5`
- `1 <= a[i] <= n`
- `1 <= b[i] <= m`

### Examples

**Input**
```
8 6
3 1 2 8 5 7 6 4
6 5 1 2 3 4
```

**Output**
```
3
1 2 4
```

---

## What this problem really is

A common subsequence appears in both arrays in the same relative order. In `b`, every value appears exactly once, so each value has one fixed position there. Reading `a` from left to right, a common subsequence is a set of values whose positions in `b` keep increasing. That is a longest increasing subsequence (LIS) problem.

## What to use

| Tool | Why |
|------|-----|
| Position map of `b` | Gives each value its single position in `b` |
| Sequence of positions, read from `a` | Turns the LCS question into an increasing-sequence question |
| LIS with patience sorting | Finds the longest increasing run in `O(n log n)` |
| Parent links | Recovers the actual subsequence, not just its length |

## Approach

**Root idea:** because `b` is a permutation, every value `v` has exactly one position `pos[v]` in `b`. Read `a` from left to right and write down `pos[a[i]]` for each value that also appears in `b`. A common subsequence is exactly a subsequence of this list whose entries increase. So the answer is the LIS of this list.

**Mental picture:** imagine `b` as a row of numbered seats. Walk through `a` and, for each value, write down its seat number. A common subsequence is a group of values that you pick in the order they appear in `a`, and whose seat numbers go up. The biggest such group is the longest increasing run of seat numbers.

**How that becomes the method**

1. Build `pos[v]` = index of `v` in `b`.
2. Walk `a` from left to right. Keep `a[i]` only if `a[i] <= m`, since larger values do not appear in `b`. Record the sequence of pairs `(pos[a[i]], a[i])`.
3. Find the LIS of the positions using patience sorting:
   - `tail[k]` = the smallest possible last position of an increasing run of length `k+1`.
   - For each new position, binary search the first `tail` that is `>=` it, replace that tail, and store a parent link to the element currently at the previous tail.
4. The LIS length is the final number of tails. Follow parent links back from the last element of the longest run, then reverse to get the values in order.

**Data structures:**
- `pos[]` for the positions in `b`.
- `tail[]`: indices into the filtered sequence, for patience sorting.
- `parent[]`: for each element of the filtered sequence, the previous element of its run.

## Walkthrough (on a sample)

`a = 3 1 2 8 5 7 6 4`, `b = 6 5 1 2 3 4`.

Positions in `b`: `6 → 1`, `5 → 2`, `1 → 3`, `2 → 4`, `3 → 5`, `4 → 6`.

Walk `a` and keep only values `<= 6`:

| `a` value | Kept? | Position in `b` |
|---|---|---|
| 3 | yes | 5 |
| 1 | yes | 3 |
| 2 | yes | 4 |
| 8 | no | — |
| 5 | yes | 2 |
| 7 | no | — |
| 6 | yes | 1 |
| 4 | yes | 6 |

Filtered sequence of positions: `5, 3, 4, 2, 1, 6` (values `3, 1, 2, 5, 6, 4`).

Patience sorting, showing the tails (positions) after each element:

1. `5` → tails `[5]`.
2. `3` → replaces `5` → tails `[3]`.
3. `4` → appended → tails `[3, 4]`. Parent of `4` is `3`.
4. `2` → replaces `3` → tails `[2, 4]`.
5. `1` → replaces `2` → tails `[1, 4]`.
6. `6` → appended → tails `[1, 4, 6]`. Parent of `6` is `4`.

The LIS length is `3`. Follow parents from the last element, position `6` (value `4`):
- parent is position `4` (value `2`),
- parent of that is position `3` (value `1`).

Reversed, the values are `1, 2, 4`. Output: `3`, then `1 2 4`. Matches the expected sample.

## Pitfalls

- Using LCS dynamic programming. With `n, m` up to `2e5`, `O(n * m)` is far too slow. The permutation structure reduces the problem to LIS.
- Forgetting to skip values of `a` that are larger than `m`. They do not appear in `b`.
- Printing positions instead of values. The output is the values from `a`.
- Reconstructing from the final tails array. Use the parent link stored when each element was placed.

## Complexity

- Time: `O((n + m) log n)`.
- Space: `O(n + m)`.

## Key takeaway

When both sequences are permutations, a common subsequence is an increasing subsequence after mapping each element to its position in the second array. That turns LCS into LIS.
