# [1993D — Med-imize](https://codeforces.com/problemset/problem/1993/D)

**Platform:** Codeforces
**Source:** Codeforces Round 963 (Div. 2)
**Tags:** binary search, dp, greedy
**Difficulty / Rating:** 2200
**Time limit:** 2 seconds
**Memory limit:** 256 megabytes
**Editorial:** [Editorial of Codeforces Round 963 (Div. 2) (Problem D section)](https://codeforces.com/blog/entry/132185)

---

## Problem Statement

Given two positive integers `n` and `k`, and an array `a` of `n` integers.

In one operation, select any subarray of size `k` and remove it, keeping the order of the other elements. For example, with `a = [1, 2, 3, 4, 5]`, removing `(3, 5)` gives `[1, 2]`; removing `(2, 4)` gives `[1, 5]`; removing `(1, 3)` gives `[4, 5]`.

Repeat the operation while the length of `a` is greater than `k`. Find the largest possible median of the remaining array.

The median of an array of length `m` is the element at index `floor((m + 1) / 2)` (1-based) after sorting in non-decreasing order.

### Input

The first line contains `t` (`1 <= t <= 1e4`).

Each test case has a line with `n` and `k` (`1 <= n, k <= 5e5`), then a line with `a[1..n]` (`1 <= a[i] <= 1e9`).

The sum of `n` over all test cases does not exceed `5e5`.

### Output

For each test case, print the largest possible median.

### Constraints

- `1 <= t <= 1e4`
- `1 <= n, k <= 5e5`
- `1 <= a[i] <= 1e9`
- Sum of `n` `<= 5e5`

### Examples

Input:

```
5
4 3
3 9 9 2
5 3
3 2 5 6 4
7 1
5 9 2 6 5 4 6
8 2
7 1 2 6 8 3 4 5
4 5
3 4 5 6
```

Output:

```
3
4
9
6
4
```

### Note

In the first test case, the subarray `(1, 3)` or `(2, 4)` can be removed, leaving `[3]` or `[2]`. The better median is `3`.

In the second test case, the three reachable arrays are `[6, 4]`, `[3, 4]`, and `[3, 2]`, with medians `4`, `3`, and `2`. The answer is `4`.

In the third test case, only one element remains, so the answer is the largest element, `9`.

---

## What this problem really is

We delete blocks of exactly `k` consecutive elements until at most `k` remain, and want the median of what is left to be as large as possible. The median is hard to optimise directly, but "is the median at least `x`?" is easy to test for a fixed `x`. So the problem becomes: for a given `x`, can we delete blocks so the survivors have more elements `>= x` than `< x`? Then binary search on `x`.

## What to use

| Tool | Why |
|------|-----|
| Binary search on the answer `x` | The predicate "median `>= x`" is monotone in `x`. |
| `+1` / `-1` encoding | Mark each element `+1` if `a[i] >= x`, else `-1`. Then "median `>= x`" is equivalent to the sum of the survivors being positive. |
| Residues mod `k` | Deleting a block of length `k` shifts later indices by `k`, so the index modulo `k` of each survivor is fixed. Survivors are one per residue class. |
| Linear DP over the marked array | Chooses which elements survive with the largest possible sum, in `O(n)` per check. |

## Approach

**Root idea:** Fix a candidate `x`. Mark each element `+1` if it is at least `x` and `-1` otherwise. The median of the final array is at least `x` exactly when the final marked sum is positive. So we need the maximum possible final marked sum over all valid deletion sequences. If that maximum is positive, `x` is achievable.

**Why the survivors have a fixed structure:** Deleting a block of length `k` moves everything after it `k` positions to the left. If we track indices modulo `k`, a shift by `k` changes nothing. So the element that ends up at final position `j` always comes from an original index congruent to `j` modulo `k`. The final length is `((n - 1) mod k) + 1` when `n > k`, and the survivors are one per residue class from `0` up to that length minus one. Whether a survivor is kept is decided by a prefix-style choice across the array.

**Mental picture:** Lay the marked array out in rows of width `k`, one row per block. A deletion removes a full column-aligned block, so the surviving entries stay in fixed columns. Going left to right, at each column you either keep the running total going, or restart the total from the current entry, which is the same as deleting everything before it in whole blocks. The DP tracks the best running total ending at each position.

**How that becomes the method:**
1. Set `b[i] = +1` if `a[i] >= x`, else `-1`.
2. Define a DP over `b`: `dp[0] = b[0]`. For `i` a multiple of `k` (and `i > 0`), `dp[i] = max(dp[i - k], b[i])`. Otherwise `dp[i] = dp[i - 1] + b[i]`, and when `i > k` also take `max(dp[i], dp[i - k])`.
3. `x` is achievable if `dp[n - 1] > 0`.
4. Binary search `x` over `[1, 1e9]` for the largest achievable value.

If `n <= k`, nothing is deleted, `dp` is just the prefix sums, and the check reduces to "the whole array's marked sum is positive", which is the median test on the original array.

**Data structures:**
- An array `b` of `+1` / `-1` values, rebuilt per check.
- A DP array of length `n`.
- Binary search bounds `lo`, `hi`.

## Walkthrough (on a sample)

Test 2: `n = 5`, `k = 3`, `a = [3, 2, 5, 6, 4]`. Expected answer `4`.

Check `x = 4`: `b = [-1, -1, +1, +1, +1]`.

- `dp[0] = -1`.
- `i = 1` (not a multiple of 3): `dp[1] = dp[0] + b[1] = -2`. `i > k`? No.
- `i = 2`: `dp[2] = dp[1] + b[2] = -1`.
- `i = 3` (multiple of 3): `dp[3] = max(dp[0], b[3]) = max(-1, +1) = +1`.
- `i = 4` (not a multiple): `dp[4] = dp[3] + b[4] = +2`. Since `i > k`, also `max(+2, dp[1] = -2) = +2`.
- `dp[4] = 2 > 0`, so `x = 4` is achievable.

Check `x = 5`: `b = [-1, -1, +1, +1, -1]`.

- `dp[0] = -1`, `dp[1] = -2`, `dp[2] = -1`, `dp[3] = max(-1, +1) = +1`.
- `dp[4] = dp[3] + b[4] = 0`, then `max(0, dp[1] = -2) = 0`.
- `dp[4] = 0`, not positive. `x = 5` fails.

Largest achievable `x` is `4`, matching the sample.

## Pitfalls

- **Using `>` instead of `>=` for the marking:** An element equal to `x` counts toward `>= x`, so it must be `+1`.
- **Getting the median index wrong:** The median index is `floor((m + 1) / 2)`. For length `m`, the median is `>= x` exactly when more than half of the survivors are `>= x`, which is the same as the `+1` / `-1` sum being positive, for both odd and even `m`.
- **Forgetting the `n <= k` case:** No deletion happens, so the check is just the total marked sum.
- **Binary search range:** The answer is always one of the input values, so `[1, 1e9]` covers it.
- **Overflow:** Sums are at most `5e5`, so `int` is fine, but the DP array must be sized to `n`.
- **Mixing up the two DP transitions:** The residue-class step (`i` a multiple of `k`) and the general step are different; applying the general step at multiples of `k` gives wrong answers.

## Complexity

- Time: `O(n log(max a))` per test: one `O(n)` check for each of about 30 binary search steps.
- Space: `O(n)`.

## Key takeaway

To maximise a median, binary search the answer and turn the question into a `+1` / `-1` sum; when deletions are blocks of fixed length, tracking indices modulo that length turns the deletion problem into a small linear DP.
