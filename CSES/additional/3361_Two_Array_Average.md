# [3361 — Two Array Average](https://cses.fi/problemset/task/3361)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems I  
**Tags:** Binary search on the answer, prefix sums, fractional objective, precision  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given two arrays `a` and `b`, each with `n` integers. Choose a non-empty prefix of `a` and a non-empty prefix of `b`, so that the average of all selected numbers is as large as possible. Print the two prefix sizes.

Any answer whose average is within `1e-6` (absolute or relative) of the maximum is accepted.

### Input

- The first line has `n`.
- The next line has `a[1..n]`.
- The next line has `b[1..n]`.

### Output

Print two integers: the prefix size of `a` and the prefix size of `b`.

### Constraints

- `1 <= n <= 1e5`
- `1 <= a[i], b[i] <= 1e9`

### Examples

**Input**
```
4
1 5 5 2
3 1 3 1
```

**Output**
```
3 1
```

**Explanation:** Take `[1, 5, 5]` and `[3]`. The average is `(1+5+5+3)/4 = 3.5`, which is the maximum.

---

## What this problem really is

We want to maximize `(A_i + B_j) / (i + j)`, where `A_i` is the sum of the first `i` elements of `a`, and `B_j` is the sum of the first `j` elements of `b`, with `i, j >= 1`. This is a fraction, and the trick is to turn it into a yes/no question.

## What to use

| Tool | Why |
|------|-----|
| Binary search on the answer `t` | Asks "is there a pair with average at least `t`?", which is easy to check |
| Subtract `t` from every element | Turns "average at least `t`" into "sum of `(value - t)` is at least 0" |
| Best prefix of each array | For each array, the best prefix can be found in one pass |
| Final pass at the answer | Recovers the actual prefix sizes |

## Approach

**Root idea:** a pair `(i, j)` has average at least `t` exactly when `sum(a[1..i] - t) + sum(b[1..j] - t) >= 0`. Shifting by `t` removes the division. The two arrays can then be handled separately: pick the best `i` for `a` and the best `j` for `b`, and add them.

**Mental picture:** imagine each element is a sack of gold with value `a[k] - t`. Some sacks are worth more than `t` and others less. We can take any prefix of each row of sacks. If we can collect a total of at least `0` from the best prefix of `a` plus the best prefix of `b`, then some pair of prefixes has average at least `t`. Raising `t` makes every sack less valuable, so the answer to "can we reach `0`?" flips from yes to no at exactly one point. Binary search finds that point.

**How that becomes the method**

1. Binary search `t` in `[0, 1e9]` for about 100 iterations, or until the range is tiny.
2. For a candidate `t`:
   - Compute prefix sums of `a[k] - t` and take the maximum over `i >= 1`. Call it `bestA`, and record its index.
   - Do the same for `b`, giving `bestB` and its index.
   - If `bestA + bestB >= 0`, then `t` is achievable: set `lo = t`. Otherwise set `hi = t`.
3. After the search, rerun the check at `lo` and report the recorded indices.

**Data structures:**
- Two running prefix sums and running maxima, with the index of each maximum.
- Only `O(1)` extra memory per check.

## Walkthrough (on a sample)

`a = 1 5 5 2`, `b = 3 1 3 1`. The true answer is `3.5`.

**Check `t = 3.4`.**

- For `a`: values `a - t = -2.4, 1.6, 1.6, -1.4`. Prefix sums: `-2.4, -0.8, 0.8, -0.6`. Max is `0.8` at `i = 3`.
- For `b`: values `b - t = -0.4, -2.4, -0.4, -2.4`. Prefix sums: `-0.4, -2.8, -3.2, -5.6`. Max is `-0.4` at `j = 1`.
- `0.8 + (-0.4) = 0.4 >= 0`. Feasible, so `lo = 3.4`.

**Check `t = 3.6`.**

- For `a`: `-2.6, 1.4, 1.4, -1.6`. Prefix sums: `-2.6, -1.2, 0.2, -1.4`. Max `0.2` at `i = 3`.
- For `b`: `-0.6, -2.6, -0.6, -2.6`. Prefix sums: `-0.6, -3.2, -3.8, -6.4`. Max `-0.6` at `j = 1`.
- `0.2 + (-0.6) = -0.4 < 0`. Not feasible, so `hi = 3.6`.

The answer is between `3.4` and `3.6`, which matches `3.5`. Rerunning at `lo` gives `i = 3`, `j = 1`. Output `3 1`. Matches.

## Pitfalls

- Searching on the ratio directly with floating point and no check. Use the shifted-sum test.
- Forgetting that both prefixes must be non-empty. Start the maxima at prefix length `1`, not `0`.
- Recovering indices at the wrong `t`. Report the indices from a check at a feasible `t`, not from the last midpoint.
- Using too few iterations. The values reach `1e9`, so about 100 halvings are safe for `1e-6` relative precision.
- Assuming a unique answer. Any optimal pair is accepted.

## Complexity

- Time: `O(100 * n)`, about `1e7` operations.
- Space: `O(1)` extra.

## Key takeaway

To maximize a ratio like `sum / count` over choices, binary search the ratio `t` and check whether some choice has `sum - t * count >= 0`. The check becomes a simple maximum-prefix problem.
