# [1979D — Fixing a Binary String](https://codeforces.com/problemset/problem/1979/D)

**Platform:** Codeforces
**Source:** Codeforces Round 951 (Div. 2)
**Tags:** bitmasks, brute force, constructive algorithms, dp, greedy, hashing, strings
**Difficulty / Rating:** 1800
**Time limit:** 2 seconds
**Memory limit:** 256 megabytes
**Editorial:** [Round 951 editorial (Problem 1979D section)](https://codeforces.com/blog/entry/130213)

---

## Problem Statement

You are given a binary string `s` of length `n`, consisting of zeros and ones. You can perform the following operation exactly once:

1. Choose an integer `p` (`1 <= p <= n`).
2. Reverse the substring `s[1] s[2] ... s[p]`. After this step, the string becomes `s[p] s[p - 1] ... s[1] s[p + 1] ... s[n]`.
3. Then, perform a cyclic shift of the string `s` to the left `p` times. After this step, the initial string becomes `s[p + 1] s[p + 2] ... s[n] s[p] s[p - 1] ... s[1]`.

For example, if you apply the operation to the string `110001100110` with `p = 3`, after the second step the string becomes `011001100110`, and after the third step it becomes `001100110011`.

A string `s` is called `k`-proper if two conditions are met:

- `s[1] = s[2] = ... = s[k]`;
- `s[i + k] != s[i]` for any `i` (`1 <= i <= n - k`).

For example, with `k = 3`, the strings `000`, `111000111`, and `111000` are `k`-proper, while the strings `000000`, `001100`, and `1110000` are not.

You are given an integer `k`, which is a divisor of `n`. Find an integer `p` (`1 <= p <= n`) such that after performing the operation, the string `s` becomes `k`-proper, or determine that it is impossible. Note that if the string is initially `k`-proper, you still need to apply exactly one operation to it.

### Input

Each test consists of multiple test cases. The first line contains one integer `t` (`1 <= t <= 1e4`) — the number of test cases. The description of the test cases follows.

The first line of each test case contains two integers `n` and `k` (`1 <= k <= n`, `2 <= n <= 1e5`) — the length of the string `s` and the value of `k`. It is guaranteed that `k` is a divisor of `n`.

The second line of each test case contains a binary string `s` of length `n`, consisting of the characters `0` and `1`.

It is guaranteed that the sum of `n` over all test cases does not exceed `2e5`.

### Output

For each test case, output a single integer — the value of `p` to make the string `k`-proper, or `-1` if it is impossible.

If there are multiple solutions, output any of them.

### Constraints

- `1 <= t <= 1e4`
- `2 <= n <= 1e5`
- `1 <= k <= n`, and `k` divides `n`
- Sum of `n` over all test cases `<= 2e5`

### Examples

Input:

```
7
8 4
11100001
4 2
1110
12 3
111000100011
5 5
00000
6 1
101001
8 4
01110001
12 2
110001100110
```

Output:

```
3
-1
7
5
4
-1
3
```

### Note

In the first test case, if you apply the operation with `p = 3`, after the second step the string becomes `11100001`, and after the third step it becomes `00001111`. This string is `4`-proper.

In the second test case, it can be shown that there is no operation after which the string becomes `2`-proper.

In the third test case, if you apply the operation with `p = 7`, after the second step the string becomes `100011100011`, and after the third step it becomes `000111000111`. This string is `3`-proper.

In the fourth test case, after the operation with any `p`, the string becomes `5`-proper.

---

## What this problem really is

One operation moves a reversed prefix of length `p` to the end of the string. Choose `p` so the result is a perfect alternating pattern of blocks of length `k`.

## What to use

| Tool | Why |
|------|-----|
| Trailing run length `x` | The final string must end with a block of length exactly `k`. The operation cannot shorten the current trailing run, so `x` decides the case. |
| Run-length scan | Locate a block that can supply the missing `k - x` characters for the new trailing block. |
| Direct check | After building the candidate string, verify the two `k`-proper conditions. |

## Approach

**Root idea (from the editorial):** After the operation, the new string is `s[p+1..n] + reverse(s[1..p])`. Focus on the block of identical characters at the end of the original string. That trailing run cannot shrink, so its length `x` splits the problem into three cases.

**Mental picture:** A `k`-proper string looks like equal-sized tiles of length `k` that flip color each tile: `000 111 000 111 ...` or `111 000 111 000 ...`. The operation peels a prefix, flips it, and sticks it on the end. The sticky trailing run of the original string becomes part of (or the whole of) the new last tile, so you must complete that tile to length exactly `k`.

**How that becomes the method:**
1. Let `x` be the length of the longest suffix of `s` made of the same character `c = s[n]`.
2. Case `x > k`: impossible. Print `-1`.
3. Case `x = k`: the trailing tile is already complete. Find any index where a tile boundary fails in the original string (`s[i] == s[i + k]` for some `i`), and set `p = i + 1` to cut there; if none, `p = n` works. Apply the operation and verify.
4. Case `x < k`: you still need `k - x` more copies of `c` glued onto the end. Scan blocks equal to `c`. If a block has length `L` with `L + x = k`, cut right after that block (`p = end of block`). If a block has length `L` with `L - k + x = k` (equivalently `L = 2k - x`), cut so that exactly `k - x` characters from it move to the end. If no such block exists, try `p = n` (and the check will reject it when needed).
5. Apply the operation for the chosen `p` and check the `k`-proper conditions; if they fail, answer `-1`.

**Data structures:**
- The string itself and a few integer indices for run lengths.
- No heavy structures; everything is a linear scan.

## Walkthrough (on a sample)

First test: `s = 11100001`, `k = 4`.

- Trailing run: one `1`, so `x = 1 < 4`. Need `k - x = 3` more ones.
- Blocks of `1`: the prefix `111` has length `3`, and `3 + 1 = 4`. Cut after that block: `p = 3`.
- Operation: move reverse(`111`) = `111` to the end of `00001` → `00001111`.
- Check: first `4` chars are `0`, and every shift by `4` flips. Valid. Answer `3`.

Second test: `s = 1110`, `k = 2`.
- Trailing run: one `0`, `x = 1 < 2`. Need one more `0`.
- There is no block of `0` that can supply it. Answer `-1`.

## Pitfalls

- **Skipping the mandatory operation:** Even if `s` is already `k`-proper, you must still perform exactly one operation.
- **Ignoring `x > k`:** A trailing run longer than `k` can never become a valid last tile.
- **Forgetting verification:** Construct a candidate `p`, build the resulting string, and check both `k`-proper conditions before accepting it.
- **Off-by-one on `p`:** `p` is the prefix length before the move; block ends are 1-based positions in the statement's indexing.

## Complexity

- Time: `O(n)` per test for the scans and the check. Over all tests, `O(sum of n)`.
- Space: `O(n)` for the string (or `O(1)` extra besides the input).

## Key takeaway

The trailing run length decides everything: if it is already `k`, cut at a broken tile boundary; if it is shorter, find a matching block that completes it to exactly `k`; if it is longer, give up.
