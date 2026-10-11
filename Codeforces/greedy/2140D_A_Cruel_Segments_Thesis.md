# [2140D — A Cruel Segment's Thesis](https://codeforces.com/problemset/problem/2140/D)

**Platform:** Codeforces
**Source:** Codeforces Round 1049 (Div. 2)
**Tags:** data structures, greedy, sortings
**Difficulty / Rating:** 2000
**Time limit:** 2 seconds
**Memory limit:** 256 megabytes
**Editorial:** [Codeforces Round 1049 (Div. 2) Editorial (Problem D section)](https://codeforces.com/blog/entry/146218)

---

## Problem Statement

You are given `n` segments on a number line. The `i`-th segment is represented as `[l[i], r[i]]`. Initially, all the segments are unmarked.

You perform the following operation repeatedly until there are no unmarked segments left:

- In the `k`-th operation, if there are at least two unmarked segments, choose any two unmarked segments `[l[i], r[i]]` and `[l[j], r[j]]`, mark both of them, and add a new marked segment `[x[k], y[k]]` satisfying:
  - `l[i] <= x[k] <= r[i]`,
  - `l[j] <= y[k] <= r[j]`,
  - `x[k] <= y[k]`.
- If there is exactly one unmarked segment remaining, mark it.

Your task is to determine the maximum possible sum of lengths of all the marked segments at the end of the process. The length of a segment `[l, r]` is `r - l`.

### Input

Each test contains multiple test cases. The first line contains the number of test cases `t` (`1 <= t <= 1e4`).

The first line of each test case contains a single integer `n` (`1 <= n <= 2e5`), the number of segments.

Each of the next `n` lines contains two integers `l[i]` and `r[i]` (`1 <= l[i] <= r[i] <= 1e9`).

It is guaranteed that the sum of `n` across all test cases does not exceed `2e5`.

### Output

For each test case, print a single integer: the maximum possible total length of all marked segments at the end of the process.

### Constraints

- `1 <= t <= 1e4`
- `1 <= n <= 2e5`
- `1 <= l[i] <= r[i] <= 1e9`
- Sum of `n` `<= 2e5`

### Examples

Input:

```
4
2
1 1000000000
1 1000000000
3
1 10
2 15
3 9
5
1 11
2 7
15 20
1 3
11 15
1
1000000000 1000000000
```

Output:

```
2999999997
42
59
0
```

### Note

In the first test case, we choose the given two segments and make the new segment `[1, 1e9]`.

In the second test case, we choose the segments `[1, 10]` and `[2, 15]` and make the new segment `[1, 15]`. Now, `[3, 9]` is the only segment left unmarked, and it will be marked in the next step.

---

## What this problem really is

Every original segment is counted in the answer anyway, because all of them end up marked. The only real choice is the new segments: each one pairs two original segments and contributes `y - x`, where `x` lies inside the first segment and `y` lies inside the second. We want to maximise the sum of these new lengths, and the odd segment left out (if `n` is odd) is just counted by its own length.

## What to use

| Tool | Why |
|------|-----|
| Greedy choice of endpoints | Each new segment wants `x` as small as possible and `y` as large as possible. So we take `x = l` and `y = r` on the chosen sides. |
| Sorting by `l + r` | The total depends on which segments give their left end and which give their right end. Sorting by `l + r` tells us the cheapest way to pick the left-end set. |
| 64-bit integers | The answer can be around `2e5 * 1e9`. |

## Approach

**Root idea:** Let `n` be even first. Every segment is paired with another. For a pair `(A, B)`, the best new segment is `[l[A], r[B]]` or `[l[B], r[A]]`, whichever is longer. Across all pairs, each segment contributes exactly one endpoint: either its right end (if it is the `y` side) or its left end (if it is the `x` side). Half the segments give `l` and half give `r`. So the new total is:

`sum over segments in the right-end group of r  -  sum over segments in the left-end group of l`

Rewrite this as `sum of all r  -  sum over left-end group of (l + r)`. To maximise, we want the left-end group to have the smallest possible `l + r` values. So choose the `n/2` segments with the smallest `l + r` as the left-end group.

**Mental picture:** Each segment is a pair of hooks, one at each end. Every segment must hang one hook on the new segment, and each new segment needs one left hook and one right hook. Choosing which hook each segment gives is the decision: giving the left hook costs `l + r` relative to the right hook baseline. So we give the cheap-to-give segments (small `l + r`) the left hook, and everyone else keeps the right hook.

**Why this selection is always achievable:** The chosen left-end group might not pair up cleanly, but the editorial argues that any imbalance can be fixed by swapping a left-end segment with a right-end segment across pairs, without decreasing the total. So the formula is the true maximum for even `n`.

**Odd `n`:** One segment is left out and marked alone at the end. Its length is still counted. So we choose one segment to leave out, and for the remaining `n - 1` segments (even) we apply the even rule. The total is the sum of all lengths plus the best even-case extra. The editorial gives two ways: try every leftover, or find the best leftover directly from prefix and suffix information over the sorted-by-`l + r` order.

**Data structures:**
- Array of `(l + r, l, r)` tuples, sorted by `l + r`.
- Running sums of `r` (for the right-end baseline) and of `l + r` over the chosen group.
- For odd `n`, prefix and suffix information over the sorted order to handle the leftover quickly.

**Short steps (even n):**
1. Sum all `r - l` (the original lengths) into the answer.
2. Sort segments by `l + r`.
3. Add the sum of all `r` as the baseline for the new segments.
4. Subtract the smallest `n/2` values of `l + r`.
5. Output the answer.

## Walkthrough (on a sample)

Test 2: segments `[1, 10]`, `[2, 15]`, `[3, 9]` (so `n = 3`, odd).

- Original lengths: `9 + 13 + 6 = 28`. This is the base that is always counted.
- Try leaving out `[3, 9]`. The remaining two are `[1, 10]` and `[2, 15]`. Their `l + r` values are `11` and `17`. Their `r` values sum to `10 + 15 = 25`.
- For an even pair of two segments, one gives its left end and one gives its right end. Choose the one with smaller `l + r` (`[1, 10]`, sum `11`) to give its left end `1`. The other gives its right end `15`.
- New segment length `15 - 1 = 14`.
- Total: `28 + 14 = 42`. Matches the sample.

Test 4: a single segment `[1e9, 1e9]`. There are no pairs, so no new segments. The base length is `0`. Answer `0`.

## Pitfalls

- **Counting length as `r - l + 1`:** The problem defines length as `r - l`. Using `+1` gives the wrong sum.
- **Forgetting the odd leftover:** Its original length still counts, and the left-out choice matters.
- **Using `l` alone for the sort:** The key is `l + r`. Sorting by `l` or `r` alone does not minimise the total.
- **Overflow:** Sums of `r` can be around `2e5 * 1e9`. Use 64-bit integers.
- **`n = 1`:** No new segment is created; the answer is the single segment's length, which is `r - l`. In the sample this is `0`.
- **Assuming the chosen pairs always satisfy `x <= y`:** The editorial's swap argument is what guarantees a valid pairing at the optimum.

## Complexity

- Time: `O(n log n)` per test, dominated by sorting.
- Space: `O(n)`.

## Key takeaway

When each item must contribute one of two endpoints and the total is a sum of chosen endpoints, sort by the combined value and pick the cheapest half to give the endpoint that is being subtracted.
