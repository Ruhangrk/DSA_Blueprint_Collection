# [2013E — Prefix GCD](https://codeforces.com/problemset/problem/2013/E)

**Platform:** Codeforces
**Source:** Codeforces Round 973 (Div. 2)
**Tags:** brute force, dp, greedy, math, number theory
**Difficulty / Rating:** 2200
**Time limit:** 2 seconds
**Memory limit:** 256 megabytes
**Editorial:** [Codeforces Round 973 (Div. 2) Editorial (Problem E section)](https://codeforces.com/blog/entry/134170)

---

## Problem Statement

You are given an array of positive integers `a[1], ..., a[n]`. The elements can be rearranged in any order. Find the smallest possible value of

`gcd(a[1]) + gcd(a[1], a[2]) + ... + gcd(a[1], a[2], ..., a[n])`.

### Input

The first line contains `t` (`1 <= t <= 1e4`).

Each test case has a line with `n` (`1 <= n <= 1e5`), then a line with `a[1..n]` (`1 <= a[i] <= 1e5`).

The sum of `n` over all test cases does not exceed `1e5`. The sum of `max(a)` over all test cases does not exceed `1e5`.

### Output

For each test case, output the answer on its own line.

### Constraints

- `1 <= t <= 1e4`
- `1 <= n <= 1e5`
- `1 <= a[i] <= 1e5`
- Sum of `n` `<= 1e5`, sum of `max(a)` `<= 1e5`

### Examples

Input:

```
5
3
4 2 2
2
6 3
3
10 15 6
5
6 42 12 52 20
4
42 154 231 66
```

Output:

```
6
6
9
14
51
```

### Note

In the first test case, rearrange to `[2, 4, 2]`: `gcd(2) + gcd(2, 4) + gcd(2, 4, 2) = 2 + 2 + 2 = 6`.

In the third test case, rearrange to `[6, 10, 15]`: `gcd(6) + gcd(6, 10) + gcd(6, 10, 15) = 6 + 2 + 1 = 9`.

---

## What this problem really is

The prefix gcd sequence starts at the first element and can only shrink (or stay the same) as more elements are added. Each prefix gcd is added to the total. So we want the prefix gcd to drop as fast as possible, and the ordering decides when each drop happens. The question is: in what order should the elements be placed to make the running gcd fall quickly?

## What to use

| Tool | Why |
|------|-----|
| Divide everything by the overall gcd `g` | Scales the problem so the final prefix gcd is `1`. The answer is multiplied back by `g` at the end. |
| Greedy: always append the element that minimises the new gcd | Lowers the running gcd as much as possible at every step. |
| Stop once the running gcd is `1` | After that, every further prefix gcd is `1`, so the rest of the sum is the count of remaining elements. |
| Small number of rounds | The running gcd reaches `1` after very few steps, so the greedy costs only about `10 * n` gcd operations. |

## Approach

**Root idea:** Let `g` be the gcd of the whole array. Divide every element by `g`. The answer for the original array is `g` times the answer for the divided array, because every prefix gcd gets multiplied by `g`. For the divided array, the final prefix gcd is `1`. Choose the order greedily: at each step, append the element that makes the current prefix gcd as small as possible. Once the prefix gcd is `1`, each remaining element adds `1` to the sum, so the order of the rest does not matter.

**Why the greedy is right:** Suppose `A` is the smallest achievable gcd for the current prefix, and an optimal solution puts some other element `B` in this position, with `A < B`. Put `A` there instead, then keep the rest of the optimal order. The new prefix gcd is `A`, which is no larger than the old value at this step. Each later prefix gcd is at most the old value too, since adding `A` to the prefix can only lower or keep the gcd. The editorial's inequality `A + gcd(A, B) <= B` is the key step: the running sum cannot get worse.

**Why only about 10 rounds:** The running gcd never increases, and the editorial shows that it reaches `1` within about 10 greedy picks for values up to `1e5`. After that the loop stops.

**Mental picture:** Picture a ladder of divisors. Each greedy pick takes the rung that drops you down the most. Two rungs down the ladder is always better than one rung, and after a handful of drops you hit the bottom rung `1`, which stays there for the rest of the sequence.

**How that becomes the method:**
1. Compute `g = gcd(all a[i])`, then divide each `a[i]` by `g`.
2. Keep `cur` (the running gcd, starting at `0`, since `gcd(0, x) = x`). Each round, compute `nc = min over all i of gcd(cur, a[i])`. Set `cur = nc` and add it to the answer.
3. If `cur == 1`, add `1` for each remaining position and stop.
4. Multiply the total by `g`.

**Data structures:**
- The array `a`, rescaled in place.
- A running gcd `cur` and the answer `ans` (64-bit).

## Walkthrough (on a sample)

Test 3: `a = [10, 15, 6]`, `n = 3`. Overall gcd `g = 1`, so nothing is divided.

- Round 0: `cur = 0`. `gcd(0, a[i]) = a[i]`, so the minimum is `6`. Add `6`. Total `6`.
- Round 1: `gcd(6, 10) = 2`, `gcd(6, 15) = 3`, `gcd(6, 6) = 6`. Minimum `2`. Add `2`. Total `8`.
- Round 2: `gcd(2, 10) = 2`, `gcd(2, 15) = 1`, `gcd(2, 6) = 2`. Minimum `1`. Add `1`. Total `9`.
- `cur = 1`, with `n - t - 1 = 3 - 2 - 1 = 0` remaining elements to add. Stop.

Answer `9`, matching the sample. The order produced is `6, 10, 15` (or an equivalent one).

Test 1: `a = [4, 2, 2]`, `g = 2`, divided array `[2, 1, 1]`. Round 0 minimum is `1`, add `1`, and `cur = 1`, so the remaining `2` positions add `2` more. Divided total `3`, times `g = 2`, gives `6`. Matches.

## Pitfalls

- **Forgetting the rescaling:** Without dividing by `g`, the greedy still works but the bookkeeping is less clean. Dividing first makes the final gcd exactly `1`.
- **Starting `cur` at `1` or at the first element:** Start at `0`, so the first round picks the smallest element and `gcd(0, x) = x` handles it.
- **Looping over all rounds without stopping at `1`:** Once `cur` is `1`, the rest adds `1` each. Continuing the loop wastes time but is still correct.
- **Counting the remaining elements wrong:** After round `t` (0-indexed), there are `n - t - 1` elements left to add with value `1`.
- **Overflow:** The answer can be up to about `1e5 * 1e5`. Use 64-bit integers.

## Complexity

- Time: `O(n * 10)` gcd operations per test, from about 10 greedy rounds over `n` elements (plus a logarithmic factor per gcd).
- Space: `O(n)`.

## Key takeaway

For "order the elements to minimise a sum of running gcds", greedily append the element that drops the running gcd the most; the gcd only has a few drops before it hits `1`, so the greedy is fast.
