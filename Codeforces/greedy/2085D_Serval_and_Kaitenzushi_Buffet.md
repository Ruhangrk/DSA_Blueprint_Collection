# [2085D — Serval and Kaitenzushi Buffet](https://codeforces.com/problemset/problem/2085/D)

**Platform:** Codeforces
**Source:** Codeforces Round #1011 (Div. 2)
**Tags:** data structures, graph matchings, greedy
**Difficulty / Rating:** 2000
**Time limit:** 2 seconds
**Memory limit:** 256 megabytes
**Editorial:** [Codeforces Round #1011 (Div. 2) Editorial (Problem D section)](https://codeforces.com/blog/entry/140933)

---

## Problem Statement

Serval has found a Kaitenzushi buffet restaurant. A conveyor belt delivers plates of sushi in front of him. Each plate contains exactly `k` pieces of sushi, and the `i`-th plate has deliciousness `d[i]`. Serval eats for `n` minutes, and within those minutes he must eat all the pieces of sushi he took from the belt.

Let `r` be the counter of uneaten taken pieces. Initially `r = 0`. In minute `i` (`1 <= i <= n`), only the `i`-th plate is delivered in front of him, and he can do exactly one of:

- Take the `i`-th plate from the belt. `r` increases by `k`.
- Eat one uneaten piece he took earlier. `r` decreases by `1`. This is allowed only if `r > 0`.
- Do nothing. `r` stays the same.

After the `n` minutes, `r` must be `0`.

Serval wants to maximise the sum of the deliciousnesses of all plates he took. Find that maximum.

### Input

Each test contains multiple test cases. The first line contains the number of test cases `t` (`1 <= t <= 1e4`).

The first line of each test case contains two integers `n` and `k` (`1 <= k < n <= 2e5`), the number of minutes and the number of pieces per plate.

The second line contains `n` integers `d[1], ..., d[n]` (`1 <= d[i] <= 1e9`).

It is guaranteed that the sum of `n` over all test cases does not exceed `2e5`.

### Output

For each test case, print one integer: the maximum possible sum of deliciousnesses of the plates taken.

### Constraints

- `1 <= t <= 1e4`
- `1 <= k < n <= 2e5`
- `1 <= d[i] <= 1e9`
- Sum of `n` `<= 2e5`

### Examples

Input:

```
5
5 2
3 6 4 1 2
7 1
3 1 4 1 5 9 2
4 3
4 3 2 1
6 2
1 3 5 2 4 6
6 1
1000000000 1 1000000000 1 1000000000 1
```

Output:

```
6
16
4
6
3000000000
```

### Note

In the first test case, Serval can eat at most one plate. He takes the second plate (deliciousness `6`) in minute 2, then eats its `2` pieces in minutes 3 and 4, giving `6`.

In the second test case, it is optimal to take the first, third, and sixth plates, for a total of `3 + 4 + 9 = 16`.

---

## What this problem really is

Each taken plate creates a block of work: one minute to take it, plus `k` minutes to eat its pieces later. We choose which plates to take, subject to timing constraints, to maximise the sum of their values. Because every taken plate must be finished within the `n` minutes, the question is how many blocks can fit and which plates can be used for each block.

## What to use

| Tool | Why |
|------|-----|
| Counting how many plates can be taken | Each taken plate needs `k + 1` minutes in total (take + `k` eats). The number of takes is at most `n / (k + 1)`. |
| Deadlines counted from the end | Working backwards, the `j`-th last take must happen no later than a specific minute. These are the only real constraints. |
| Max-heap of available plates | As time moves forward, we collect plates delivered so far. At each deadline we pick the best one still available. |

## Approach

**Root idea:** Every plate taken occupies a block of `k + 1` minutes (one take, then `k` eats). Taking more plates is always better for the sum if values are positive, so we want as many takes as the timeline allows, and at each deadline we must commit to one plate that has already been delivered. The best choice at each deadline is the largest available plate. This is a classic "pick one item per deadline from items that have arrived" greedy.

**Mental picture:** Lay out the `n` minutes as a row. Stack blocks of length `k + 1` from the right end. The last block must end at minute `n`, so its take must be at or before minute `n - k`. The block before it must have its take at or before minute `n - 2(k + 1) + 1`, and so on. Each deadline is a checkpoint: by that minute, a plate must already have been taken. Walk forward through the minutes, keep a pool of plates delivered so far, and at each checkpoint grab the most valuable plate from the pool.

**How that becomes the method:**
1. Walk minutes `i` from `1` to `n` in chronological order.
2. Add `d[i]` to a max-heap of plates available so far.
3. If `n - i + 1` is a multiple of `k + 1`, then minute `i` is a checkpoint. Pop the largest plate from the heap and add it to the answer.
4. Each checkpoint corresponds to one taken plate, and there are exactly `floor(n / (k + 1))` checkpoints.

**Why the largest available plate is right at each checkpoint:** Once a checkpoint passes, its plate is fixed. Taking the largest plate now leaves the smaller ones for later checkpoints, and any later checkpoint can still use any plate delivered before it, so the exchange argument shows greedy never loses.

**Data structures:**
- A max-heap of deliciousness values for plates delivered so far. Each plate is pushed once when it arrives and popped at most once.
- A running 64-bit sum of the popped values.

## Walkthrough (on a sample)

Test 2: `n = 7`, `k = 1`, `d = [3, 1, 4, 1, 5, 9, 2]`. Each take uses `k + 1 = 2` minutes, so checkpoints are minutes where `(8 - i)` is even, which means `i = 2, 4, 6`.

- Minute 1: push `3`. Heap `{3}`. Not a checkpoint.
- Minute 2: push `1`. Heap `{3, 1}`. Checkpoint. Pop `3`. Answer `3`. Heap `{1}`.
- Minute 3: push `4`. Heap `{1, 4}`. Not a checkpoint.
- Minute 4: push `1`. Heap `{1, 4, 1}`. Checkpoint. Pop `4`. Answer `7`. Heap `{1, 1}`.
- Minute 5: push `5`. Heap `{1, 1, 5}`. Not a checkpoint.
- Minute 6: push `9`. Heap `{1, 1, 5, 9}`. Checkpoint. Pop `9`. Answer `16`.
- Minute 7: push `2`. Not a checkpoint.

Final answer `16`, matching the sample.

## Pitfalls

- **Using `n / k` as the number of takes:** Each take needs `k` eats after it, so each block is `k + 1` minutes. The number of takes is `n / (k + 1)`.
- **Missing the checkpoint condition:** Checkpoints are defined from the end (`n - i + 1` divisible by `k + 1`), not from the start.
- **Pushing after popping at a checkpoint:** The plate delivered at the checkpoint minute must be in the heap before popping.
- **Taking a plate delivered after the checkpoint:** The heap only contains plates seen so far, which is exactly the constraint.
- **Overflow:** The answer can be up to about `2e5 * 1e9 / 2`. Use 64-bit integers.
- **Trying DP:** The heap greedy is much simpler and runs in `O(n log n)`.

## Complexity

- Time: `O(n log n)` per test, one heap push per minute and at most `n / (k + 1)` pops.
- Space: `O(n)` for the heap.

## Key takeaway

When each chosen item must be committed by a deadline and only items that have already arrived are eligible, process time forward, push arrivals into a max-heap, and pop the best item at each deadline.
