# [2124E — Make it Zero](https://codeforces.com/problemset/problem/2124/E)

**Platform:** Codeforces
**Source:** EPIC Institute of Technology Round Summer 2025 (Codeforces Round 1036, Div. 1 + Div. 2)
**Tags:** constructive algorithms, greedy, math
**Difficulty / Rating:** 2100
**Time limit:** 2 seconds
**Memory limit:** 256 megabytes
**Editorial:** [EPIC Institute of Technology Round Summer 2025 Editorial (Problem E section)](https://codeforces.com/blog/entry/144382)

---

## Problem Statement

You are given an array `a` of `n` positive integers. In one operation:

- Select an array `b` of size `n` such that:
  - `0 <= b[i] <= a[i]` for each `1 <= i <= n`,
  - there exists an index `1 <= i < n` such that `b[1] + ... + b[i] = b[i+1] + ... + b[n]`, that is, the prefix of length `i` has the same sum as the suffix of length `n - i`.
- Then subtract `b[i]` from `a[i]` for each `i`.

Change all elements to `0` using the minimum number of operations.

If it is impossible, output `-1`. Under the constraints, the minimum number of operations is at most 17 when it is possible.

### Input

Each test contains multiple test cases. The first line contains the number of test cases `t` (`1 <= t <= 1e4`).

The first line of each test case contains an integer `n` (`2 <= n <= 5e4`).

The second line contains `n` integers `a[1..n]` (`1 <= a[i] <= 1e12`).

The sum of `n` over all test cases does not exceed `5e4`.

### Output

For each test case, output `-1` if it is impossible.

Otherwise, output an integer `s` (`1 <= s <= 17`), the minimum number of operations. Then output `s` lines, each with `n` integers `b[1..n]` for one operation (`0 <= b[i] <= a[i]` at that moment). After all operations, every element must be `0`.

### Constraints

- `1 <= t <= 1e4`
- `2 <= n <= 5e4`
- `1 <= a[i] <= 1e12`
- Sum of `n` `<= 5e4`

### Examples

Input:

```
3
3
1 2 3
2
2 5
4
5 3 1 5
```

Output:

```
1
1 2 3
-1
2
3 1 1 1
2 2 0 4
```

### Note

In the first test case, we can choose `b = a`, which is valid because `b[1] + b[2] = b[3]`.

In the second test case, it can be proven that it is impossible to change all the elements of `a` to `0`.

---

## What this problem really is

Each operation removes a chunk from every element, but the chunk must split the array into a left part and a right part whose removed amounts are equal. Let `s` be the total sum. Every operation removes an even amount in total (the two halves are equal), so `s` must be even. We need to know exactly when zeroing is possible, when one operation suffices, and how to build a two-operation plan otherwise.

## What to use

| Tool | Why |
|------|-----|
| Total sum parity | Each operation removes `2 * (half-sum)`, so the total must be even. |
| Half-sum check on the largest element | An element greater than `s / 2` can never be fully removed, because each operation removes at most half the total from one side. |
| Prefix sums | Finding whether a prefix equals exactly `s / 2` decides whether one operation is enough. |
| A pivot position | If no prefix hits `s / 2`, the element that crosses the half line is the pivot. Splitting around it gives a two-step plan. |

## Approach

**Root idea:** Write `s` for the total sum. Work with halves of `s`.

- `-1` if `s` is odd, or if some element is greater than `s / 2`.
- Answer `1` if some proper prefix sums to exactly `s / 2`. Then the whole array is one valid `b = a`.
- Otherwise the answer is `2`. Find the pivot `pos`: the first index where the prefix sum reaches at least `s / 2`. Let `x` be the sum before the pivot, `y = a[pos]`, and `z` the sum after it. Then `x < s / 2 <= x + y` and `x + y + z = s`.

**Mental picture:** Lay the array on a balance beam. The pivot is the element that straddles the midpoint. The left block has sum `x`, the pivot has sum `y`, and the right block has sum `z`. One operation can cut the beam at a point where the left and right pulled amounts are equal. A two-step plan works in two passes:

- First operation: take the whole left block, take `x + y - s/2` from the pivot, and take `s/2 - y` from the right block. The left side removes `x`, and the right side (pivot plus right block) removes `(x + y - s/2) + (s/2 - y) = x`. The cut is between the left block and the rest, so the two sides match.
- After that, the left block is zero, the pivot has `s/2 - x` left, and the right block has `z + y - s/2 = s/2 - x` left. These two remaining amounts are equal, so the second operation cuts between the pivot and the right block and removes everything.

**Why the first operation's amounts are valid:** The pivot amount `x + y - s/2` must be between `0` and `y`, which holds because `x < s/2 <= x + y` and `x <= s/2`. The right block amount `s/2 - y` must be between `0` and `z`, which holds because `y <= s/2` (the largest element check) and `y + z = s - x > s/2`.

**Why the answer cannot be `0` operations:** The task needs at least one operation whenever some element is positive, so `1` is the floor when it is possible at all.

**Data structures:**
- A running prefix sum to find the pivot position.
- The array itself, updated in place across the two operations, so the second operation is just the remaining values.

**Short steps:**
1. Compute `s`. If odd, output `-1`.
2. If the maximum element is greater than `s / 2`, output `-1`.
3. Scan prefix sums. If some proper prefix equals `s / 2`, output `1` and the array.
4. Otherwise find the pivot, build operation 1 as above (fill the right block greedily from the far end until its total is `s/2 - y`), subtract it, and output operation 2 as the remaining array.

## Walkthrough (on a sample)

Test 3: `a = [5, 3, 1, 5]`, `s = 14`, `s / 2 = 7`.

- Odd check: `14` is even. Max is `5`, which is at most `7`. Feasible.
- Prefix sums: `5` (less than 7), `8` (at least 7). No prefix equals 7, so the answer is 2.
- Pivot is index `2` (value `y = 3`), with `x = 5` before it and `z = 6` after it.
- Operation 1: the left block (`a[1] = 5`) is taken whole, so `b[1] = 5`. The pivot takes `x + y - s/2 = 5 + 3 - 7 = 1`. The right block takes `s/2 - y = 4`, taken greedily from the far end: `b[4] = 3`, `b[3] = 1`. So `b = [5, 1, 1, 3]`. Check the cut after index 1: left `5`, right `1 + 1 + 3 = 5`. Valid.
- After operation 1, `a = [0, 2, 0, 2]`.
- Operation 2: `b = [0, 2, 0, 2]`. Cut after index 2: left `0 + 2 = 2`, right `0 + 2 = 2`. Valid. `a` becomes all zeros.

Two operations, matching the sample's count.

For test 2 (`a = [2, 5]`): `s = 7` is odd, so the answer is `-1`.

## Pitfalls

- **Forgetting parity:** An odd total can never become zero, even if no element is too big.
- **Missing the dominant element:** An element above `s / 2` makes the task impossible even with even `s`.
- **Using `>=` for the one-operation check:** The one-operation case needs a prefix that equals `s / 2` exactly.
- **Wrong pivot:** It is the first index where the prefix sum reaches at least `s / 2`, not the largest element.
- **Assuming the right block can always be taken whole:** Only the amount `s/2 - y` is taken, distributed from the far end, and each piece must not exceed the element's current value.
- **Overflow:** Sums reach `5e4 * 1e12`; use 64-bit integers.

## Complexity

- Time: `O(n)` per test for the sum, the prefix scan, and the construction.
- Space: `O(n)` for the output arrays.

## Key takeaway

For "split into two equal halves" operations, the feasibility is decided by parity and by whether one element can ever be balanced; a pivot that straddles the midpoint lets two cuts finish the job.
