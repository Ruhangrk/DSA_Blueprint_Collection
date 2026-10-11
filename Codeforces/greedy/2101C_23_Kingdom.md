# [2101C — 23 Kingdom](https://codeforces.com/problemset/problem/2101/C)

**Platform:** Codeforces
**Source:** Codeforces Round 1024 (Div. 1, Div. 2)
**Tags:** binary search, brute force, data structures, greedy, ternary search, two pointers
**Difficulty / Rating:** 2200
**Editorial:** [Codeforces Round 1024 (Div. 1, Div. 2) Editorial (Problem C section)](https://codeforces.com/blog/entry/142788)

---

## Problem Statement

The distance of a value `x` in an array `c`, written `d_x(c)`, is the largest gap between any two occurrences of `x` in `c`. Formally, `d_x(c) = max(j - i)` over all pairs `i < j` with `c[i] = c[j] = x`. If `x` appears once or not at all, `d_x(c) = 0`.

The beauty of an array `c` is the sum of `d_x(c)` over all values `1 <= x <= n`.

Given an array `a` of length `n`, an array `b` of length `n` is nice if `1 <= b[i] <= a[i]` for all `i`. Find the maximum possible beauty of any nice array.

### Input

Each test contains multiple test cases. The first line contains the number of test cases `t` (`1 <= t <= 1e4`).

The first line of each test case contains `n` (`1 <= n <= 2e5`).

The second line contains `n` integers `a[1..n]` (`1 <= a[i] <= n`).

The sum of `n` over all test cases does not exceed `2e5`.

### Output

For each test case, output the maximum possible beauty of a nice array.

### Constraints

- `1 <= t <= 1e4`
- `1 <= n <= 2e5`
- `1 <= a[i] <= n`
- Sum of `n` `<= 2e5`

### Examples

Input:

```
4
4
1 2 1 2
2
2 2
10
1 2 1 5 1 2 2 1 1 2
8
1 5 2 8 4 1 4 2
```

Output:

```
4
1
16
16
```

### Note

In the first test case, `b = [1, 2, 1, 2]` gives `d_1 = 3 - 1 = 2` and `d_2 = 4 - 2 = 2`, for a beauty of `4`. No nice array does better.

In the second test case, `b = [1, 1]` and `b = [2, 2]` both have beauty `1`.

In the third test case, `b = [1, 2, 1, 4, 1, 2, 1, 1, 1, 2]` has `d_1 = 9 - 1 = 8`, `d_2 = 10 - 2 = 8`, and `d_4 = 0`, for a beauty of `16`.

---

## What this problem really is

Each value contributes the length of the span between its first and last occurrence. We choose values for the array, subject to each position's upper limit, and want the total span length as large as possible. Only the first and last occurrence of each value matter, because the middle occurrences do not change the span. So the real question is: which values should start and end where?

## What to use

| Tool | Why |
|------|-----|
| Counting the span by gaps | A span from position `i` to `j` covers the gaps `i`, `i+1`, ..., `j-1`. Counting per gap avoids handling each value's span separately. |
| Prefix and suffix matching | For gap `g`, the number of values whose span crosses it is limited by how many distinct values can be "started" in the prefix `1..g` and "ended" in the suffix `g+1..n`. |
| Greedy matching to the largest free value | For a prefix, each position can start one distinct value no larger than its limit. Matching to the largest free value is optimal. |
| A set of free values | Supports "largest free value at most `a[i]`" and removal in `O(log n)`. |

## Approach

**Root idea:** The beauty equals the sum over gaps `g` (between positions `g` and `g+1`) of the number of values whose first occurrence is at or before `g` and last occurrence is after `g`. For a gap `g`, the number of such values is at most:

- `P[g]`: the maximum number of distinct values that can be the first occurrence in the prefix `1..g`, and
- `S[g+1]`: the maximum number of distinct values that can be the last occurrence in the suffix `g+1..n`.

Each side is a matching problem: each position takes one distinct value `<= a[i]`. The editorial proves that a single array attains these bounds for all gaps at the same time, so the answer is the sum of `min(P[g], S[g+1])` over all gaps.

**Mental picture:** Imagine each position as a box that can hold one value label, up to its limit. In the prefix, boxes try to claim distinct labels to "open" them. In the suffix, boxes try to claim distinct labels to "close" them. A gap is crossed by exactly those labels that were opened on the left and closed on the right. So each gap's crossing count is bounded by the smaller of its two sides.

**Why greedy matching works:** When scanning a prefix left to right, a position with limit `a[i]` should take the largest unused label not exceeding `a[i]`. Taking a smaller label could block a later position that needs it, while the largest label is the least useful to others with the same limit or smaller. This is the standard greedy for matching points to intervals `[1, a[i]]`.

**How that becomes the method:**
1. Build `P[g]` for `g = 1..n`: scan positions from left to right with a set of free labels `1..n`. For each `a[i]`, take the largest free label `<= a[i]` if one exists. `P[g]` is the number of labels taken after processing position `g`.
2. Build `S[g]` for `g = n..1` the same way, scanning right to left.
3. The answer is the sum over `g = 1..n-1` of `min(P[g], S[g+1])`.

**Data structures:**
- A `set` of free labels, initialised to `1..n`.
- Two arrays `P` and `S` of length `n`, storing the matched counts.

## Walkthrough (on a sample)

Test 1: `a = [1, 2, 1, 2]`, `n = 4`.

Prefix matching (`P`, left to right, free labels `{1,2,3,4}`):

- Position 1, limit 1: take label `1`. Taken count `1`. `P[1] = 1`.
- Position 2, limit 2: largest free `<= 2` is `2`. Taken count `2`. `P[2] = 2`.
- Position 3, limit 1: free labels `<= 1` are none (1 is taken). `P[3] = 2`.
- Position 4, limit 2: free labels `<= 2` are none. `P[4] = 2`.

Suffix matching (`S`, right to left, free labels `{1,2,3,4}`):

- Position 4, limit 2: take `2`. `S[4] = 1`.
- Position 3, limit 1: take `1`. `S[3] = 2`.
- Position 2, limit 2: free labels `<= 2` are none. `S[2] = 2`.
- Position 1, limit 1: none. `S[1] = 2`.

Answer, sum over gaps `g = 1..3` of `min(P[g], S[g+1])`:

- `g = 1`: `min(P[1] = 1, S[2] = 2) = 1`.
- `g = 2`: `min(P[2] = 2, S[3] = 2) = 2`.
- `g = 3`: `min(P[3] = 2, S[4] = 1) = 1`.

Total `4`, matching the sample. Gap `2` is crossed by both values, giving `d_1 = d_2 = 2`, consistent with the note.

Test 2: `a = [2, 2]`. `P[1] = 1` (take label 2), `S[2] = 1` (take label 2). Gap 1: `min(1, 1) = 1`. Answer `1`.

## Pitfalls

- **Taking the smallest free label:** For prefix matching, the largest free label `<= a[i]` is the right choice. The smallest can block later positions.
- **Summing matched counts instead of gap bounds:** The answer is the sum of per-gap minima, not `P[n]` or the number of distinct values.
- **Forgetting that the distance counts gaps:** A span from `i` to `j` has length `j - i`, which equals the number of gaps it crosses.
- **Off-by-one on suffix indices:** Gap `g` uses `P[g]` (prefix ending at `g`) and `S[g+1]` (suffix starting at `g+1`).
- **Using `int` for sums:** The answer can exceed `2^31` for large `n`; use 64-bit integers.
- **`n = 1`:** There are no gaps, so the answer is `0`.

## Complexity

- Time: `O(n log n)` per test, from the set operations on each of the two passes.
- Space: `O(n)`.

## Key takeaway

When a total equals a sum over gaps and each gap's crossing count is limited by two independent matchings, compute the matchings from each side greedily and sum the minima.
