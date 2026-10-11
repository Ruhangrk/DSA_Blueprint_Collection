# [2042C — Competitive Fishing](https://codeforces.com/problemset/problem/2042/C)

**Platform:** Codeforces
**Source:** Educational Codeforces Round 172 (Rated for Div. 2)
**Tags:** greedy
**Difficulty / Rating:** 1800
**Time limit:** 2 seconds
**Memory limit:** 512 megabytes
**Editorial:** [Edu Round 172 editorial (Problem C section)](https://codeforces.com/blog/entry/136886)

---

## Problem Statement

Alice and Bob participate in a fishing contest! In total, they caught `n` fishes, numbered from `1` to `n` (the bigger the fish, the greater its index). Some of these fishes were caught by Alice, others — by Bob.

Their performance will be evaluated as follows. First, an integer `m` will be chosen, and all fish will be split into `m` non-empty groups. The first group should contain several (at least one) smallest fishes, the second group — several (at least one) next smallest fishes, and so on. Each fish should belong to exactly one group, and each group should be a contiguous subsegment of fishes. Note that the groups are numbered in exactly that order; for example, the fishes from the second group cannot be smaller than the fishes from the first group, since the first group contains the smallest fishes.

Then, each fish will be assigned a value according to its group index: each fish in the first group gets value equal to `0`, each fish in the second group gets value equal to `1`, and so on. So, each fish in the `i`-th group gets value equal to `(i - 1)`.

The score of each contestant is simply the total value of all fishes that contestant caught.

You want Bob's score to exceed Alice's score by at least `k` points. What is the minimum number of groups (`m`) you have to split the fishes into? If it is impossible, you should report that.

### Input

The first line contains a single integer `t` (`1 <= t <= 1e4`) — the number of test cases.

The first line of each test case contains two integers `n` and `k` (`2 <= n <= 2e5`; `1 <= k <= 1e9`).

The second line contains a string, consisting of exactly `n` characters. The `i`-th character is either `0` (denoting that the `i`-th fish was caught by Alice) or `1` (denoting that the `i`-th fish was caught by Bob).

Additional constraint on the input: the sum of `n` over all test cases does not exceed `2e5`.

### Output

For each test case, print a single integer — the minimum number of groups you have to split the fishes into; or `-1` if it's impossible.

### Constraints

- `1 <= t <= 1e4`
- `2 <= n <= 2e5`
- `1 <= k <= 1e9`
- String length `n`, characters only `0` / `1`
- Sum of `n` over all test cases `<= 2e5`

### Examples

Input:

```
7
4 1
1001
4 1
1010
4 1
0110
4 2
0110
6 3
001110
10 20
1111111111
5 11
11111
```

Output:

```
2
-1
2
-1
3
4
-1
```

### Note

In the first test case of the example, you can split the fishes into groups as follows: the first three fishes form the 1st group, the last fish forms the 2nd group. Then, Bob's score will be `1`, and Alice's score will be `0`.

In the third test case of the example, you can split the fishes into groups as follows: the first fish forms the 1st group, the last three fishes form the 2nd group. Then, Bob's score will be `2`, and Alice's score will be `1`.

---

## What this problem really is

Cut the sequence of fishes into the fewest contiguous groups so that Bob's weighted score beats Alice's by at least `k`.

## What to use

| Tool | Why |
|------|-----|
| Suffix balance `s[i]` | A cut just before fish `i` adds `+1` to every fish from `i` onward, so it changes the Bob−Alice difference by (#Bob − #Alice) in that suffix. |
| Greedy on largest positives | Minimizing the number of cuts means picking the largest positive contributions until the sum reaches `k`. |

## Approach

**Root idea (from the editorial):** Each border between groups increases the value of every fish after that border by `1`. So the final Bob−Alice score difference is exactly the sum of certain suffix balances. To minimize the number of groups, take the largest positive balances as cuts.

**Mental picture:** Imagine walking from the smallest fish to the largest. Every time you open a new group, every fish still ahead on the line gets `+1` value. Opening a new group just before position `i` is worth `s[i] = (Bob count − Alice count)` among fishes `i..n`. Negative worth cuts never help Bob relative to Alice, so discard them. Then grab the biggest remaining worths until you have at least `k`.

**How that becomes the method:**
1. Scan the string from right to left. Maintain a running balance `sum`, adding `+1` for Bob (`'1'`) and `-1` for Alice (`'0'`).
2. For every possible cut position `i` from `n` down to `2` (after updating with fish `i`), if the current `sum` is positive, store it.
3. Sort the stored values ascending (or keep a max-heap).
4. Start with `ans = 1` groups and `need = k`. While `need > 0` and values remain, subtract the largest value and increase `ans` by `1`.
5. If `need` is still positive, answer `-1`; otherwise answer `ans`.

**Data structures:**
- A vector of positive suffix balances.
- Sorting (or a priority queue) to pick the largest ones.

## Walkthrough (on a sample)

First test: `n = 4`, `k = 1`, string `1001` (Alice, Alice, Alice, Bob) — wait, chars are for fishes `1..4`: `1,0,0,1` means Bob, Alice, Alice, Bob.

Right-to-left balances after each step (editorial code style):
- After fish 4 (`1`): `sum = 1`. Cut before 4: store `1`.
- After fish 3 (`0`): `sum = 0`. Not stored.
- After fish 2 (`0`): `sum = -1`. Not stored.

Positive values: `[1]`. Take it: `need = 1 - 1 = 0`, `ans = 2`. Answer `2`, matching the sample.

Third test: `0110`, `k = 1`.
- Fish 4 `0`: `sum = -1` (not stored).
- Fish 3 `1`: `sum = 0` (not stored).
- Fish 2 `1`: `sum = 1` (store `1`).

Take `1`, answer `2`. Matches.

## Pitfalls

- **Using prefix instead of suffix:** A cut adds value only to fishes after the border.
- **Keeping negative balances:** They cannot help reach a positive target `k`.
- **Answering the number of cuts:** The answer is cuts + `1` (number of groups).
- **Forgetting `-1`:** If even all positive cuts sum to less than `k`, print `-1`.

## Complexity

- Time: `O(n log n)` per test for the sort. Over all tests, `O(sum of n * log n)`.
- Space: `O(n)`.

## Key takeaway

Each cut is worth the Bob−Alice balance of the suffix after it. Take the largest positive cut values until the difference reaches `k`; groups = cuts + 1.
