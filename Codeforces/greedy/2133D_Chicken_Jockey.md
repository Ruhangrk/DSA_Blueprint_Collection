# [2133D — Chicken Jockey](https://codeforces.com/problemset/problem/2133/D)

**Platform:** Codeforces
**Source:** Codeforces Round 1044 (Div. 2)
**Tags:** dp, greedy
**Difficulty / Rating:** 1900
**Time limit:** 2 seconds
**Memory limit:** 256 megabytes
**Editorial:** [Round 1044 editorial (Problem D section)](https://codeforces.com/blog/entry/145785)

---

## Problem Statement

Steve made the foolish decision to mine at night, and came across a monstrous creature: the chicken jockey `n`!

A chicken jockey `n` consists of `n` mobs stacked in order on top of each other, with mob `1` at the bottom and mob `n` at the top. Mob `i` initially has `h[i]` health.

In one attack, Steve can deal `1` damage to any mob. If any mob reaches `0` or less health, it dies, and all the mobs on top of it fall down and form a new stack. The bottom mob in the new stack takes `1` fall damage for every mob it was on top of before falling (i.e. the number of mobs below it in the previous stack, including the one that just died). This may kill it as well, in which case all mobs on top of it fall down again and the process repeats.

For example, consider a chicken jockey `6` with initial mob healths `[1, 2, 1, 3, 5, 2]`. If Steve damages the third mob in the stack, it dies and the mobs with health `[3, 5, 2]` fall down in a new stack. The bottom mob takes `3` units of fall damage so it also dies, and the mobs with health `[5, 2]` fall down in a new stack. The bottom mob takes `1` unit of fall damage. As a result, after Steve's first attack, there will be two stacks with healths `[1, 2]` and `[4, 2]`.

Steve's sword's durability is low, so he wishes to know the minimum attacks required to kill all the mobs.

### Input

Each test contains multiple test cases. The first line contains the number of test cases `t` (`1 <= t <= 1e4`). The description of the test cases follows.

The first line of each test case contains a single integer `n` (`2 <= n <= 2e5`) — the number of mobs.

The second line of each test case contains `n` integers `h[1], h[2], ..., h[n]` (`1 <= h[i] <= 1e9`) — the initial health of each mob.

It is guaranteed that the sum of `n` over all test cases does not exceed `2e5`.

### Output

For each test case, output a single integer — the minimum attacks required to kill all the mobs.

### Constraints

- `1 <= t <= 1e4`
- `2 <= n <= 2e5`
- `1 <= h[i] <= 1e9`
- Sum of `n` over all test cases `<= 2e5`

### Examples

Input:

```
5
5
3 1 4 1 2
4
1 1 1 1
6
1 2 1 3 5 2
6
3 1 1 3 2 1
3
1000000000 1000000000 1000000000
```

Output:

```
7
1
7
5
2999999998
```

### Note

In the first test case, the initial stack has mobs with health `[3, 1, 4, 1, 2]`. Steve can use one attack to damage the second mob in the stack and kill it. The third mob takes `2` units of fall damage. There are now two stacks: `[3]` and `[2, 1, 2]`. Now Steve can kill the second mob in the second stack. The third mob in the stack takes `2` units of fall damage, killing it. There are now two stacks: `[3]` and `[2]`. Finally, Steve can use five attacks to kill the remaining mobs.

In the second test case, Steve can deal `1` damage to the bottom mob in the stack. When it dies, the second mob will take `1` unit of fall damage and die; then the third mob will take `1` unit of fall damage and die; finally the fourth mob will take `1` unit of fall damage and die.

---

## What this problem really is

Minimize sword hits on a stack of mobs, using free fall damage that cascades when a mob dies.

## What to use

| Tool | Why |
|------|-----|
| Observation: at most one fall | After a mob falls, it sits at the bottom of its new stack, so it never falls again. |
| Local choice per mob | Either take `1` fall damage from the mob below dying after a fall, or take the full original-stack fall by killing the mob below with pure attacks. |
| Linear DP | `dp[i]` = min attacks to clear the first `i` mobs under an optimal local choice. |

## Approach

**Root idea (from the editorial):** A mob takes fall damage at most once. If it should take more than `1` fall damage, that must happen in the original stack (by killing the mob below with direct attacks), because only then are there many mobs below it. That local choice gives a simple DP.

**Mental picture:** Process the stack from bottom to top. For mob `i`, decide how it dies relative to mob `i - 1`:
- Soft fall: mob `i - 1` dies somehow, then mob `i` falls one step and loses `1` HP for free.
- Hard fall: kill mob `i - 1` entirely by attacks (no fall for it), so mob `i` falls the full `i - 1` levels in the original stack.

**How that becomes the method:**
1. Let `dp[0] = 0` and `dp[1] = h[1]`.
2. For `i = 2..n` (1-based):
   - Option A (1 fall damage): `dp[i - 1] + h[i] - 1`.
   - Option B (max fall damage `i - 1`): `dp[i - 2] + h[i - 1] + max(0, h[i] - (i - 1))`.
   - Set `dp[i] = min(option A, option B)`.
3. Answer is `dp[n]`.

**Data structures:**
- Array `dp[0..n]`.
- Only the last two DP values are needed if you want `O(1)` extra space.

## Walkthrough (on a sample)

Second test: `h = [1, 1, 1, 1]`.

- `dp[1] = 1`.
- `i = 2`: A = `1 + 1 - 1 = 1`. B = `0 + 1 + max(0, 1 - 1) = 1`. `dp[2] = 1`.
- `i = 3`: A = `1 + 1 - 1 = 1`. B = `1 + 1 + max(0, 1 - 2) = 2`. `dp[3] = 1`.
- `i = 4`: A = `1 + 1 - 1 = 1`. B = `1 + 1 + max(0, 1 - 3) = 2`. `dp[4] = 1`.

Answer `1`, matching the chain-reaction kill from the bottom.

## Pitfalls

- **Assuming unlimited cascade falls for the same mob:** Each mob falls at most once.
- **Using `h[i] - (i - 1)` without `max(0, ...)`:** Fall damage cannot overshoot into negative attack cost.
- **Off-by-one on fall height:** Mob `i` (1-based) has `i - 1` mobs below it in the original stack.
- **64-bit answers:** Health values are up to `1e9` and `n` up to `2e5`, so the answer needs 64-bit integers.

## Complexity

- Time: `O(n)` per test. Over all tests, `O(sum of n)`.
- Space: `O(n)` or `O(1)` extra with rolling DP.

## Key takeaway

Each mob falls at most once. Choose locally between a 1-damage soft fall and a max-damage hard fall, and stitch the choices with a linear DP.
