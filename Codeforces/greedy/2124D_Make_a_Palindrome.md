# [2124D — Make a Palindrome](https://codeforces.com/problemset/problem/2124/D)

**Platform:** Codeforces
**Source:** EPIC Institute of Technology Round Summer 2025 (Codeforces Round 1036, Div. 1 + Div. 2)
**Tags:** greedy, sortings, two pointers
**Difficulty / Rating:** 1700
**Time limit:** 2 seconds
**Memory limit:** 256 megabytes
**Editorial:** [Round 1036 editorial (Problem D section)](https://codeforces.com/blog/entry/144382)

---

## Problem Statement

You are given an array `a` of size `n` and an integer `k`. You may perform the following operation any number of times:

1. Select two integers `l` and `r` with `1 <= l <= r <= |a|` such that `r - l + 1 >= k`.
2. Select an index `i` with `l <= i <= r` such that `a[i]` is the `k`-th smallest number of the subarray `a[l..r]`. If several indices qualify, you may choose any of them. For example, with `a = [1, 2, 2, 1, 3]`, `l = 1`, `r = 5`, and `k = 3`, the candidate indices are 2 and 3.
3. Delete `a[i]` from `a`, concatenating the remaining parts of the array.

Determine whether it is possible to make the array a palindrome after any number of operations. An empty array counts as a palindrome.

An array `b[1..m]` is a palindrome if `b[i] = b[m + 1 - i]` for every `1 <= i <= m`.

### Input

The first line contains `t` (`1 <= t <= 1e4`), the number of test cases. Each test case has:
- a line with `n` and `k` (`1 <= k <= n <= 2e5`);
- a line with `a[1..n]` (`1 <= a[i] <= n`).

The sum of `n` over all test cases does not exceed `2e5`.

### Output

For each test case, print `YES` if a palindrome can be obtained, otherwise `NO`. Any letter case is accepted.

### Constraints

- `1 <= t <= 1e4`
- `1 <= k <= n <= 2e5`
- `1 <= a[i] <= n`
- Sum of `n` over all test cases `<= 2e5`

### Examples

Input:

```
8
5 3
5 4 3 4 5
4 1
1 1 2 1
6 6
2 3 4 5 3 2
5 4
5 2 4 3 1
8 5
4 7 1 2 3 1 3 4
5 4
1 2 1 2 2
3 3
1 2 2
4 4
2 1 2 2
```

Output:

```
YES
YES
YES
NO
NO
YES
NO
YES
```

### Note

- Test 1: `a` is already a palindrome.
- Test 2: `[1, 1, 2, 1]` becomes `[1, 2, 1]`, then `[1, 1]`.
- Test 3: `[2, 3, 4, 5, 3, 2]` becomes `[2, 3, 4, 3, 2]`.
- Tests 4 and 5: no sequence of operations produces a palindrome.

---

## What this problem really is

We may remove elements one at a time, but a removal is only legal if the removed value is the `k`-th smallest of some window of length at least `k`. The question is whether a palindrome can be the end result.

## What to use

| Tool | Why |
|------|-----|
| Sorting | Gives the `(k-1)`-th smallest value, which I call the **cutoff**. It separates the values that must stay from the values that can go. |
| Filtering | Every element strictly greater than the cutoff can be removed for free, and removing it never hurts, so it is dropped first. |
| Two pointers | After filtering, we only need to match the two ends of the array. Each mismatch forces exactly one deletion. |
| A budget counter `spare` | The number of cutoff-valued elements we may remove without dropping below `k-1` remaining elements. |

## Approach

**Root idea:** The `k-1` smallest values can never be removed. Everything above the cutoff can be removed freely. Only elements equal to the cutoff form a limited budget.

**Mental picture:** Imagine a row of blocks sorted by height. The `k-1` shortest blocks are fixed to the floor. Blocks taller than the cutoff can be lifted off at any time. Blocks exactly at the cutoff height can also be lifted off, but only `spare` of them, because the row must keep at least `k-1` blocks. The palindrome check is a matching game on this row: compare the two ends, and if they differ, the only block you are allowed to lift is a cutoff block at one of the ends.

**How that becomes the method:**
1. If `k = 1`, every element can be removed (a single element is the 1st smallest of its own window), so the answer is YES. Stop here.
2. Otherwise, find the cutoff: the `(k-1)`-th smallest value in `a`.
3. Build `c`, the array of elements whose value is at most the cutoff, keeping their original order. Let `m = |c|`.
4. Set `spare = m - (k - 1)`. This is how many elements of `c` we may delete while keeping at least `k-1` of them.
5. Two pointers with `L = 0` and `R = m - 1`, while `L < R`:
   - If `c[L] = c[R]`, keep both and move both pointers inward.
   - Otherwise, if `spare = 0`, the answer is NO.
   - Otherwise, if `c[L]` is the cutoff, delete it: move `L` inward and decrease `spare`.
   - Otherwise, if `c[R]` is the cutoff, delete it: move `R` inward and decrease `spare`.
   - Otherwise, the answer is NO, since neither end can be removed.
6. If the loop finishes without failing, the answer is YES.

**Data structures:**
- A sorted copy of `a` to read off the cutoff.
- The filtered array `c` with its original order.
- Two integer indices `L` and `R`.
- One integer `spare`.

## Walkthrough (on a sample)

Test 6: `n = 5`, `k = 4`, `a = [1, 2, 1, 2, 2]`.

- Sorted: `1, 1, 2, 2, 2`. The `(k-1) = 3`rd smallest is `2`, so the cutoff is `2`.
- Nothing is above the cutoff, so `c = [1, 2, 1, 2, 2]` and `m = 5`.
- `spare = 5 - 3 = 2`.
- Step 1: `L = 0` (value `1`), `R = 4` (value `2`, the cutoff). They differ, and `c[L]` is not the cutoff, but `c[R]` is. Delete it. `R = 3`, `spare = 1`. The array now reads `[1, 2, 1, 2]`.
- Step 2: `L = 0` (value `1`), `R = 3` (value `2`, the cutoff). They differ again. Delete the right end. `R = 2`, `spare = 0`. The array now reads `[1, 2, 1]`.
- Step 3: `L = 0` and `R = 2` both hold `1`. Keep both. `L = 1`, `R = 1`, the loop ends.
- The answer is YES. The final array `[1, 2, 1]` is a palindrome, and `3 = k-1` elements remain, as required.

## Pitfalls

- **`k = 1`:** There is no `(k-1)`-th smallest value. Every element is removable, so the answer is always YES.
- **Miscounting `spare`:** It is the number of elements `<=` cutoff minus `(k-1)`, not the number of cutoff-valued elements.
- **Skipping the filter:** Elements above the cutoff inflate the array and can block matches. Remove them first.
- **Spending the budget early:** Only spend `spare` on a mismatch. Deleting cutoff elements that already match wastes budget.
- **Thinking the final array must have length `>= k`:** It does not. Once the length drops below `k`, no more operations are possible, which is fine as long as the array is a palindrome.
- **Both ends at the cutoff:** They are equal, so they match and are kept.

## Complexity

- Time: `O(n log n)` for the sort, then `O(n)` for the filter and two pointers. Over all tests, `O(sum of n * log n)`.
- Space: `O(n)`.

## Key takeaway

Protect the `k-1` smallest values, discard everything bigger than the cutoff, and treat ties at the cutoff as a budget that is spent only on mismatches.
