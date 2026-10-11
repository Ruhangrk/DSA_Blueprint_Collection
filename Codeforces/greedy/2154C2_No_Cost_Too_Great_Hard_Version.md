# [2154C2 — No Cost Too Great (Hard Version)](https://codeforces.com/problemset/problem/2154/C2)

**Platform:** Codeforces
**Source:** Codeforces Round 1060 (Div. 2)
**Tags:** greedy, math, number theory
**Difficulty / Rating:** 2000
**Time limit:** 3 seconds
**Memory limit:** 256 megabytes
**Editorial:** [Codeforces Round 1060 (Div. 2) Editorial (Problem C2 section)](https://codeforces.com/blog/entry/147471)

---

## Problem Statement

This is the hard version of the problem. The difference between the versions is that in this version, `1 <= b[i] <= 1e9` for all `i` (`1 <= i <= n`). You can hack only if you solved all versions of this problem.

You find yourself with two arrays of positive integers `a` and `b`, both of length `n`. You will perform the following operation any number of times (possibly none):

- Select an integer `i` (`1 <= i <= n`) and increase `a[i]` by `1`. This has a cost of `b[i]`.

Determine the minimum total cost to make it so that there exist two integers `i, j` where `1 <= i < j <= n` and `gcd(a[i], a[j]) > 1`.

`gcd(x, y)` denotes the greatest common divisor (GCD) of integers `x` and `y`.

### Input

Each test contains multiple test cases. The first line contains the number of test cases `t` (`1 <= t <= 1e4`).

The first line of each testcase contains an integer `n` (`2 <= n <= 2e5`), the length of the array `a`.

The second line contains `n` integers `a[1], ..., a[n]` (`1 <= a[i] <= 2e5`).

The third line contains `n` integers `b[1], ..., b[n]` (`1 <= b[i] <= 1e9`).

The sum of `n` across all testcases does not exceed `2e5`.

### Output

For each testcase, output the minimum cost.

### Constraints

- `1 <= t <= 1e4`
- `2 <= n <= 2e5`
- `1 <= a[i] <= 2e5`
- `1 <= b[i] <= 1e9`
- Sum of `n` `<= 2e5`

### Examples

Input:

```
6
2
1 1
1 2
2
4 8
41 67
5
1 1 727 1 1
1 1 1000 1 1
2
3 11
1 1
3
2 7 11
1 6 6
3
2 7 11
100 1 2
```

Output:

```
3
0
2
1
5
1
```

### Note

In the first testcase we can do the following: `[1, 1] -> [2, 1]` (`x = 1`), then `[2, 1] -> [2, 2]` (`x = 2`). Cost `1 + 2 = 3`. Now `gcd(a[1], a[2]) = gcd(2, 2) = 2 > 1`. It can be proven that this is the minimum cost required.

In the second testcase it is already true that `gcd(a[1], a[2]) = 4 > 1`. So no operations are required.

---

## What this problem really is

Two numbers must end up sharing a prime factor. Each increment costs the per-element price `b[i]`, which can be very different across elements. We need the cheapest way to create a common prime between some pair. The answer is at most a small cost built from the two cheapest elements, so the search is over a few candidate patterns, not over all possibilities.

## What to use

| Tool | Why |
|------|-----|
| Prime divisors of numbers up to `2e5 + 1` | Sharing a common factor is the same as sharing a common prime. Precomputing prime lists makes each check fast. |
| Counting primes already in use | Tells in `O(1)` per prime whether two current elements already share it. |
| Sorting by `b` | Lets us identify the cheapest one or two elements immediately. |
| Greedy argument on `b` | Proves that only the cheapest element ever needs many increments. |

## Approach

**Root idea:** Any pair can be fixed at cost at most `b[x] + b[y]` by making both values even (one increment each if they are odd). So the answer is at most the sum of the two smallest costs. The real question is whether we can do something cheaper, and that happens in only three ways:

1. **Cost `0`:** Some prime already divides two different elements.
2. **Cost `b[i]` (one increment on one element):** Some element `a[i] + 1` shares a prime with another unchanged element. One increment is enough.
3. **Many increments on the cheapest element `x` (smallest `b`):** Choose a prime `p` that divides some other unchanged element `a[j]`. The number of increments needed to make `a[x]` divisible by `p` is `(p - a[x] % p) % p`. Pay that times `b[x]`.

**Mental picture:** Each element is a box with a price tag per increment. A shared prime is a bridge between two boxes. The cheapest bridge is either already built, one step away on a single box, or reached by pushing the cheapest box forward until it lands on a prime that another box carries. Pushing a box forward is only worth it on the cheapest box, because every other box costs at least as much per step.

**Why only the cheapest element for many steps:** Suppose a non-cheapest element `x` gets `k >= 2` increments. That costs `k * b[x]`. The alternative of pushing the cheapest element `y` by one step and `x` by one step costs at most `b[y] + b[x]`, and since `b[y] <= b[x]` and `k >= 2`, `b[y] + b[x] <= k * b[x]`. So this is never strictly better.

**Why two elements never both need many steps:** If two elements each get increments, you can make both even with at most one increment each, which costs at most `b[i] + b[j]`. That is already covered by the initial bound.

**Data structures:**
- `pfac[v]`: list of distinct primes of `v`, for `v` up to `2e5 + 1`, built with a sieve.
- `cnt[p]`: how many elements (in the current working set) have prime `p`. Used to detect shared primes and to test whether a prime is present in another element.
- `ord`: indices sorted by `b`, to get the cheapest element `idx`.

**Method (in words):**
1. Initialise `ans = b_min1 + b_min2` (the two smallest costs).
2. Count primes over all `a[i]`. If any prime count exceeds `1`, set `ans = 0`.
3. For each `i`, remove `a[i]`'s primes from `cnt`. Then if any prime of `a[i] + 1` is still present in `cnt`, set `ans = min(ans, b[i])`. Restore `cnt`.
4. Let `idx` be the cheapest element. Gather the primes of every other element. For each such prime `p`, compute `t = (p - a[idx] % p) % p` and set `ans = min(ans, t * b[idx])`.
5. Output `ans`.

## Walkthrough (on a sample)

Test 5: `n = 3`, `a = [2, 7, 11]`, `b = [1, 6, 6]`.

- Initial bound: the two smallest `b` are `1` and `6`, so `ans = 7`.
- Shared prime check: primes are `{2}`, `{7}`, `{11}`. No overlap, so `ans` stays.
- One increment on one element:
  - Element `2` becomes `3` (prime `3`). No other element has `3`.
  - Element `7` becomes `8` (prime `2`). Element `2` has prime `2`, so cost `b = 6`. `ans = 6`.
  - Element `11` becomes `12` (primes `2, 3`). Element `2` has `2`, cost `6`.
- Cheapest element is index of `a = 2` with `b = 1`. Other primes are `7` and `11`:
  - Prime `7`: `t = (7 - 2 % 7) % 7 = 5`, cost `5 * 1 = 5`. `ans = 5`.
  - Prime `11`: `t = 9`, cost `9`.

Answer `5`, matching the sample.

Test 6: same `a`, but `b = [100, 1, 2]`. Now the cheapest element is `7` (`b = 1`). Other primes include `2` from element `2`: `t = (2 - 7 % 2) % 2 = 1`, cost `1 * 1 = 1`. The answer is `1`, which matches the sample. The greedy choice of the cheapest element is what makes this cheap.

## Pitfalls

- **Forgetting the zero-cost check:** If two elements already share a prime, the answer is `0`, even if the initial bound is smaller or larger.
- **Using `a[i]` instead of `a[i] + 1`:** The single-increment check must test the incremented value.
- **Removing your own primes before checking:** When testing `a[i] + 1`, first remove `a[i]`'s primes from the counts so you do not match against yourself.
- **Using the wrong modulus:** If `a[idx]` is already divisible by `p`, the increment count is `0`, not `p`. Use `(p - r) % p` with `r = a[idx] % p`.
- **Overflow:** `t * b[idx]` can reach about `2e5 * 1e9`, which needs 64-bit integers.
- **Sieve bound:** `a[i] + 1` can be `2e5 + 1`, so the prime lists must cover at least that value.

## Complexity

- Time: sieve is `O(N log log N)` with `N = 2e5 + 1`. Per test, the work is about `O(n * w)` where `w` is the number of distinct primes per value (at most about 6 for this range), plus sorting `O(n log n)`.
- Space: `O(N * w)` for prime lists, plus `O(n)` per test.

## Key takeaway

For "make two values share a factor" with unequal costs, the answer is always bounded by the two cheapest elements, and any deeper gain comes from pushing only the single cheapest element until it lands on a prime another element already carries.
