# [2101B — Quartet Swapping](https://codeforces.com/problemset/problem/2101/B)

**Platform:** Codeforces
**Source:** Codeforces Round 1024 (Div. 1)
**Tags:** brute force, data structures, divide and conquer, greedy, sortings
**Difficulty / Rating:** 1800
**Time limit:** 2 seconds
**Memory limit:** 256 megabytes
**Editorial:** [Round 1024 editorial (Problem B section)](https://codeforces.com/blog/entry/142788)

---

## Problem Statement

You are given a permutation `a` of length `n`. You are allowed to do the following operation any number of times (possibly zero):

- Choose an index `1 <= i <= n - 3`. Then, swap `a[i]` with `a[i + 2]`, and `a[i + 1]` with `a[i + 3]` simultaneously. In other words, the permutation is transformed from `[..., a[i], a[i + 1], a[i + 2], a[i + 3], ...]` to `[..., a[i + 2], a[i + 3], a[i], a[i + 1], ...]`.

Determine the lexicographically smallest permutation that can be obtained by applying the above operation any number of times.

A permutation of length `n` is an array of `n` distinct integers from `1` to `n` in arbitrary order.

An array `x` is lexicographically smaller than an array `y` of the same size if and only if, in the first position where they differ, `x` has a smaller element than `y`.

### Input

Each test contains multiple test cases. The first line contains the number of test cases `t` (`1 <= t <= 1000`). The description of the test cases follows.

The first line of each test case contains a single integer `n` (`4 <= n <= 2e5`) — the length of permutation `a`.

The second line contains `n` integers `a[1], a[2], ..., a[n]` (`1 <= a[i] <= n`) — the elements of permutation `a`.

It is guaranteed that the sum of `n` over all test cases does not exceed `2e5`.

### Output

For each test case, output the lexicographically smallest permutation that can be obtained by applying the above operation any number of times.

### Constraints

- `1 <= t <= 1000`
- `4 <= n <= 2e5`
- `a` is a permutation of `1..n`
- Sum of `n` over all test cases `<= 2e5`

### Examples

Input:

```
3
4
3 4 1 2
5
5 4 3 1 2
10
10 9 8 7 6 5 4 3 2 1
```

Output:

```
1 2 3 4
2 1 3 4 5
2 1 4 3 6 5 8 7 10 9
```

### Note

In the first test case, an operation can be done on index `i = 1`, and the permutation will become `[1, 2, 3, 4]`, which is the lexicographically smallest permutation achievable.

In the second test case, we can do the following sequence of operations:

- Do an operation on index `i = 2`. The permutation becomes `[5, 1, 2, 4, 3]`.
- Do an operation on index `i = 1`. The permutation becomes `[2, 4, 5, 1, 3]`.
- Do an operation on index `i = 2`. The permutation becomes `[2, 1, 3, 4, 5]`.

---

## What this problem really is

Using the block swap of two pairs two positions apart, rearrange a permutation into the lexicographically smallest reachable one.

## What to use

| Tool | Why |
|------|-----|
| Odd / even chains | Each operation moves values two steps, so index parity never changes. |
| Sorting each chain | Within a parity class, values can be rearranged almost freely, so sort them ascending for the lex-smallest layout. |
| Inversion parity | One global invariant remains: the parity of the number of inversions in the odd chain versus the even chain. It fixes the last four positions. |

## Approach

**Root idea (from the editorial):** An operation never changes the parity of an element's index. So the odd-indexed positions stay an odd-chain, and the even-indexed positions stay an even-chain. Inside those chains you can rearrange freely until only a short suffix is left; the remaining constraint is that the parity of inversions is preserved, which uniquely decides whether the last four elements need one extra swap.

**Mental picture:** Think of the array as two interleaved lanes: odd seats and even seats. The operation slides two passengers from one block into the other block's seats of the same color. You can sort each lane, then weave them back as `odd1, even1, odd2, even2, ...`. If that weaving breaks the inversion-parity invariant, swap the last odd seat with the odd seat two places before it (`a[n]` with `a[n - 2]` when `n` is odd is the same idea in 1-based indexing: swap positions `n` and `n - 2`).

**How that becomes the method:**
1. Split `a` into two vectors: values at odd indices and values at even indices (1-based).
2. Compute the parity of the number of inversions in each vector.
3. Sort both vectors ascending.
4. Write them back into the odd / even positions in that sorted order.
5. If the two original inversion parities differed, swap positions `n` and `n - 2` in the rebuilt array.
6. Print the array.

**Data structures:**
- Two vectors for the chains.
- A Fenwick tree (or any `O(n log n)` inversion counter) to get inversion parity. Only the parity is needed.

## Walkthrough (on a sample)

Second test: `a = [5, 4, 3, 1, 2]`.

- Odd positions (1, 3, 5): `[5, 3, 2]` — inversions `(5,3), (5,2), (3,2)` → 3, odd parity.
- Even positions (2, 4): `[4, 1]` — one inversion → odd parity.
- Parities match, so no suffix correction.
- Sort odds → `[2, 3, 5]`, sort evens → `[1, 4]`.
- Weave: `2, 1, 3, 4, 5`, matching the sample.

First test: odds `[3, 1]`, evens `[4, 2]`. Both have one inversion (odd). Sorted weave: `1, 2, 3, 4`.

## Pitfalls

- **Mixing 0-based and 1-based parity:** The problem statement uses 1-based indices; keep the odd/even split consistent with that.
- **Sorting without the parity fix:** Fully sorted weave is not always reachable; the final swap restores the invariant.
- **Counting full inversions only for the answer:** You only need parity, but any correct inversion count works.
- **Trying to move a value across parities:** Impossible under these operations.

## Complexity

- Time: `O(n log n)` per test for sorting and inversion parity. Over all tests, `O(sum of n * log n)`.
- Space: `O(n)`.

## Key takeaway

Values stay on their index-parity lane. Sort each lane for lex-smallest order, then apply one parity-of-inversions correction on the suffix if needed.
