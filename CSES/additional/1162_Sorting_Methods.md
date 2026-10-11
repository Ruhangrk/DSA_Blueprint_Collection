# [1162 — Sorting Methods](https://cses.fi/problemset/task/1162)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems I  
**Tags:** Permutations, inversions, cycles, longest increasing subsequence, greedy suffix  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a permutation of `1..n`. Compute the minimum number of steps to sort it using each of four operations, separately:

1. Swap two adjacent elements.
2. Swap any two elements.
3. Move any element to any other position.
4. Move any element to the front of the array.

### Input

- The first line has `n`.
- The second line has the permutation.

### Output

Print four numbers: the minimum steps for methods 1, 2, 3, and 4, in that order.

### Constraints

- `1 <= n <= 2e5`

### Examples

**Input**
```
8
7 8 2 6 5 1 3 4
```

**Output**
```
20 6 5 6
```

---

## What this problem really is

Each method is a different way of sorting, and each has its own classic formula:

- Method 1 counts pairs that are out of order.
- Method 2 counts how many swaps are needed to fix all cycles.
- Method 3 asks how many elements must move, so the elements that already stay in place form a longest increasing subsequence.
- Method 4 asks how many elements must move to the front, so the elements that stay form a chain of the largest values in order.

## What to use

| Method | Quantity | Why |
|---|---|---|
| 1. Adjacent swaps | Number of inversions | Each adjacent swap fixes exactly one out-of-order pair |
| 2. Any swaps | `n - cycles` | Each cycle of length `L` needs `L - 1` swaps |
| 3. Move anywhere | `n - LIS` | Elements in an increasing subsequence can stay put; every other element moves once |
| 4. Move to front | `n - L` | The unmoved elements must be the largest values, in increasing order |

## Approach

**Method 1, root idea:** an adjacent swap changes the number of inversions (pairs `i < j` with `a[i] > a[j]`) by exactly one. Sorting means reaching zero inversions, so the answer is the inversion count. Count inversions with a Fenwick tree while scanning left to right: for each element, count the larger elements already seen.

**Method 2, root idea:** view the permutation as cycles (follow `i -> a[i]`). A cycle of length `L` can be sorted with `L - 1` swaps, and no fewer. So the total is `n - (number of cycles)`.

**Method 3, root idea:** elements that never move must end up in increasing order, so they form an increasing subsequence of the original array. Every element outside it must be moved at least once, and moving one element each step is enough. So the answer is `n - LIS`.

**Method 4, root idea:** when we move elements to the front, the elements we never touch end up at the back, in their original order. Sorted order at the back means they must be the largest values `n-L+1, ..., n` in increasing position order. Find the largest such run, starting from `n` and going down, and the answer is `n - L`.

**Mental picture for method 4:** imagine the final sorted row. We pick up cards and push them to the front of the table. The cards we never pick up slide to the back, untouched. Those cards must already be a sorted tail, `k, k+1, ..., n`, appearing left to right. The longest such tail is the set we keep.

**How that becomes the method**

1. Method 1: scan, count with a Fenwick tree how many earlier values are greater than the current one. Sum those counts.
2. Method 2: follow each unvisited index around its cycle, count cycles, answer `n - cycles`.
3. Method 3: compute the LIS length with patience sorting. Answer `n - LIS`.
4. Method 4: let `pos[v]` be the index of value `v`. Start with `L = 1` at value `n`. While `v = n - L` exists and `pos[v] < pos[v+1]`, increase `L`. Answer `n - L`.

## Walkthrough (on a sample)

Permutation `a = 7 8 2 6 5 1 3 4`, `n = 8`.

**Method 1.** Count out-of-order pairs. For each value, count smaller values after it:
- `7`: after it are `2, 6, 5, 1, 3, 4`, all smaller except none, so `6`.
- `8`: `6`.
- `2`: `1` after it, so `1`.
- `6`: `5, 1, 3, 4`, so `4`.
- `5`: `1, 3, 4`, so `3`.
- Others: `0`.

Total `6 + 6 + 1 + 4 + 3 = 20`. ✓

**Method 2.** Cycles of `i -> a[i]`:
- `1 -> 7 -> 3 -> 2 -> 8 -> 4 -> 6 -> 1` is one cycle of length `7`.
- `5 -> 5` is a fixed point, length `1`.

Two cycles. Answer `8 - 2 = 6`. ✓

**Method 3.** Longest increasing subsequence of `7 8 2 6 5 1 3 4`: the best is `1, 3, 4` or `2, 3, 4`, length `3`. Answer `8 - 3 = 5`. ✓

**Method 4.** Positions: `pos[8]=2`, `pos[7]=1`, `pos[6]=4`, `pos[5]=5`, `pos[4]=8`, `pos[3]=7`, `pos[2]=3`, `pos[1]=6`.

- Start at value `8`, `L = 1`.
- Value `7`: is `pos[7] < pos[8]`? `1 < 2`, yes. `L = 2`.
- Value `6`: is `pos[6] < pos[7]`? `4 < 1`, no. Stop.

`L = 2`, answer `8 - 2 = 6`. ✓

Output: `20 6 5 6`. Matches.

## Pitfalls

- Method 2 with fixed points. A fixed point is a cycle of length 1 and needs zero swaps, so it is still counted as a cycle in `n - cycles`.
- Method 3 is not the same as method 1. Moving one element can fix many inversions at once.
- Method 4 requires the kept values to be the largest ones, not any increasing run. Checking "increasing from the start" gives a wrong answer.
- Using `O(n^2)` inversion counting. With `n = 2e5` use a Fenwick tree.
- Forgetting to check the run from value `n` downward. The kept tail must end at `n`.

## Complexity

- Method 1: `O(n log n)`.
- Method 2: `O(n)`.
- Method 3: `O(n log n)`.
- Method 4: `O(n)`.
- Space: `O(n)`.

## Key takeaway

Four sorting operations, four different bookkeeping quantities. Find the thing each operation can keep fixed, and the answer is what is left over.
