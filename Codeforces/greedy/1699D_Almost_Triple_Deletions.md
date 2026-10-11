# [1699D — Almost Triple Deletions](https://codeforces.com/problemset/problem/1699/D)

**Platform:** Codeforces
**Source:** Codeforces Round 804 (Div. 2)
**Tags:** data structures, dp, greedy
**Difficulty / Rating:** 2300
**Time limit:** 2 seconds
**Memory limit:** 256 megabytes
**Editorial:** [Codeforces Round #804 (Div. 2) Editorial](https://codeforces.com/blog/entry/108560)

---

## Problem Statement

You are given an array `a[1..n]`. In one operation, choose an index `i` (`1 <= i < n`) such that `a[i] != a[i+1]`, and delete both `a[i]` and `a[i+1]`. The remaining parts are concatenated.

Find the maximum possible length of an array of equal elements obtainable by applying any number of operations (possibly none).

### Input

The first line contains `t` (`1 <= t <= 1000`). Each test case has `n` (`1 <= n <= 5000`), followed by `a[1..n]` with `1 <= a[i] <= n`. The sum of `n` over all test cases is at most `1e4`.

### Output

For each test case, print one integer: the maximum length.

### Constraints

- `1 <= t <= 1000`
- `1 <= n <= 5000`
- `1 <= a[i] <= n`
- Sum of `n` `<= 1e4`

### Examples

Input:

```
5
7
1 2 3 2 1 3 3
1
1
6
1 1 1 2 2 2
8
1 1 2 2 3 3 1 1
12
1 5 2 3 3 3 4 4 4 4 3 3
```

Output:

```
3
1
0
4
2
```

### Note

In the first test, the sequence is `[1,2,3,2,1,3,3] -> [3,2,1,3,3] -> [3,3,3]`, so the answer is `3`. In the third test, the whole array can be deleted, so the answer is `0`.

---

## What this problem really is

Keep some subsequence of equal values, and delete everything between them in valid chunks. The question becomes: which segments of the original array can be completely removed, and how do the kept elements chain together?

## What to use

| Tool | Why |
|------|-----|
| Deletable-segment lemma | A segment can be fully removed iff its length is even and no value occurs more than half its length. |
| Sentinels at both ends | Lets the prefix before the first kept element and the suffix after the last one be handled by the same rule. |
| DP over positions `dp[i]` | `dp[i]` is the longest chain of equal kept values ending with `a[i]`. |
| Incremental frequency scan | For a fixed start, extend the segment one element at a time and track its max frequency in `O(1)`. |

## Approach

**Root idea:** A segment `[l..r]` can be fully deleted if and only if its length is even and its most frequent value appears at most half the length. The optimal strategy is to always delete a most frequent value together with a neighbor.

Why that lemma holds: If the most frequent value occurs more than half the time, at least `2k - n > 0` elements must remain. Otherwise, deleting one most frequent value with an adjacent different value never pushes any value above half, so the array can be emptied.

Given deletability, the kept elements form a chain: if `a[i] = a[j]` with `j < i` and the segment strictly between them is deletable, then `a[i]` can extend a chain ending at `a[j]`. The DP stores the best chain length ending at each position.

**Mental picture:** Think of a row of blocks. You are picking matching blocks to keep, and the gaps between consecutive kept blocks must be erasable, which only happens when each gap is even in size and no single color dominates it.

**How that becomes the method:**
1. Add sentinels: position `0` with no value (a start point), and position `n + 1` with no value (an end point that matches nothing).
2. `dp[0]` is the start. For each `j > 0`, `dp[j]` starts at `0`. For position `j` with `dp[j] > 0`, set it to `1` if the prefix before `j` is deletable (the sentinel rule handles this).
3. For each `i` from `0` to `n`, scan `j` from `i + 1` to `n + 1`, maintaining the max frequency of the segment `a[i+1..j-1]`. When `j - i - 1` is even, the segment is deletable, and `a[i] == a[j]` (or `i = 0`), set `dp[j] = max(dp[j], dp[i] + 1)`.
4. The answer is `dp[n + 1] - 1`, with the sentinel excluded from the count.

Note the editorial's code scans forward and only pushes from states with `dp[i] > 0`, which is the same recurrence written as a push.

**Data structures:**
- A `dp` array of length `n + 2`.
- A frequency array reset per start `i`.

## Walkthrough (on a sample)

Test 1: `a = [1, 2, 3, 2, 1, 3, 3]`, `n = 7`.

Keep the `3` at position 3. The prefix `[1, 2]` has length 2 (even) and max frequency 1 ≤ 1, so it is deletable. `dp[3] = 1`.

Next keep the `3` at position 6. The gap `[2, 1]` at positions 4 and 5 has length 2 and max frequency 1, so it is deletable. `dp[6] = dp[3] + 1 = 2`.

Next keep the `3` at position 7. The gap is empty (length 0, deletable). `dp[7] = dp[6] + 1 = 3`.

Sentinel at position 8: the suffix after position 7 is empty, so it is deletable. `dp[8] = dp[7] + 1 = 4`. Answer `dp[8] - 1 = 3`. Matches.

This corresponds to the sample's operations: delete positions 1 and 2 (values 1, 2), then delete positions 4 and 5 (values 2, 1), leaving `[3, 3, 3]`.

Test 3: `a = [1, 1, 1, 2, 2, 2]`. Length 6 is even and max frequency 3 ≤ 3, so the whole array is deletable. No value is kept, so the answer is `0`. Matches.

## Pitfalls

- **Odd-length gaps:** A gap of odd length can never be fully removed, so only even-length gaps are allowed.
- **Using `<` instead of `<=` for frequency:** The condition is max frequency at most half the length. A value occupying exactly half is still allowed.
- **Empty gaps:** Length 0 is deletable, so adjacent equal elements chain directly.
- **Comparing to the sentinel:** The end sentinel must not match any real value, and the start sentinel matches anything.
- **Answer off by one:** The sentinel at `n + 1` is counted in `dp`, so subtract 1.

## Complexity

- Time: `O(n^2)` per test case, which fits since `n <= 5000` and the total `n` is at most `1e4`.
- Space: `O(n)`.

## Key takeaway

Deletability of a segment reduces to a parity and max-frequency check, and then the kept elements form a simple chain DP over positions.
