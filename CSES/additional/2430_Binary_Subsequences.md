# [2430 — Binary Subsequences](https://cses.fi/problemset/task/2430)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems II  
**Tags:** Distinct subsequences, DP recurrence with last occurrence, shortest construction, exhaustive search on short strings  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Find a shortest bit string that has exactly `n` distinct non-empty subsequences. Print any valid string.

### Input

- One line with `n`.

### Output

Print one binary string of minimum length with exactly `n` distinct non-empty subsequences.

### Constraints

- `1 <= n <= 1e6`

### Examples

**Input**
```
6
```

**Output**
```
101
```

**Explanation:** The distinct non-empty subsequences of `101` are `0, 1, 01, 10, 11, 101`, which is six.

---

## What this problem really is

Appending a character can at most double the number of distinct subsequences. So the length is at least `log2(n + 1)`, which is about `20` for `n = 1e6`. Since the answer is that short, we can try every string of each length, in order, and stop at the first one with the right count.

## What to use

| Tool | Why |
|------|-----|
| Counting with the empty subsequence | A clean recurrence: appending a character doubles the count, minus the duplicates |
| Last-occurrence correction | When a character repeats, some subsequences are double counted; the correction is the count from before its previous occurrence |
| Enumeration of short strings | Answer length is about `log2(n)`, so `2^length` is about `2n`, which is small |

## Approach

**Recurrence.** Keep `D`, the number of distinct subsequences including the empty one. Start with `D = 1`, since only the empty string exists. For each character `c` in the string:
- `D_new = 2 * D - last[c]`, where `last[c]` is the value of `D` just before the previous occurrence of `c` was appended (`0` if `c` has not appeared).
- Then set `last[c] = D` (the value before this append).

The answer for the string is `D - 1`, since we exclude the empty subsequence.

**Why the correction:** appending `c` gives every existing subsequence a copy with `c` added. The copies that already existed before the last `c` were already produced, so they are counted twice. Those duplicates are exactly the subsequences counted at the time of the last `c`, which is `last[c]`.

**Mental picture:** imagine a row of boxes, one per distinct subsequence. Adding a bit doubles the number of boxes by giving every box a copy with the new bit appended. But copies that land in boxes that already exist are merged rather than added. The merged ones are the boxes that were created before the previous occurrence of the same bit, so we subtract them.

**How that becomes the method**

1. Let `L` start at `1`. Compute `L` as the smallest value with `2^L - 1 >= n`, then increase it if needed.
2. For each length `L`, enumerate all `2^L` bit strings. For each, run the recurrence to get `D`. If `D - 1 = n`, print the string and stop.
3. Since the first `L` that works is the answer, the output is minimal.

**Data structures:** two integers `last[0]` and `last[1]`, and a running `D`.

## Walkthrough (on a sample)

Target `n = 6`, so `D` must become `7`.

Length `1` cannot work: the largest count is `2^1 - 1 = 1` non-empty subsequence, below `6`.

Length `2`: the largest count is `3` (for example `01`), still below `6`.

Length `3`: try strings in order. Consider `101`:

| Step | Char | `last` before | `D` before | `D_new = 2D - last[c]` | Update `last[c] = D before` |
|---|---|---|---|---|---|
| start | | | `1` | | |
| 1 | `1` | `last[1] = 0` | `1` | `2 - 0 = 2` | `last[1] = 1` |
| 2 | `0` | `last[0] = 0` | `2` | `4 - 0 = 4` | `last[0] = 2` |
| 3 | `1` | `last[1] = 1` | `4` | `8 - 1 = 7` | `last[1] = 4` |

Final `D = 7`, so the number of non-empty subsequences is `7 - 1 = 6`. Matches.

Check by listing: `0, 1, 01, 10, 11, 101` is six. ✓

Other strings of length `3` (for example `111`, which gives `3`) do not reach `6`, so `101` is a valid minimal answer.

## Pitfalls

- Forgetting to subtract the empty subsequence. The recurrence counts it, so the answer is `D - 1`.
- Setting `last[c]` to the value after the append instead of before it. The correction must use the value from before the previous occurrence was appended.
- Starting `D` at `0`. It must start at `1`, the empty string.
- Searching lengths in the wrong order. Try lengths upward so the first match is the shortest.
- Expecting a unique answer. Any minimal string is accepted.

## Complexity

- Time: about `2^L * L` where `L` is about `log2(n)`. For `n = 1e6`, this is about `2 * 10^6 * 20`, which is well within the limit.
- Space: `O(L)`.

## Key takeaway

A shortest construction whose length is logarithmic can be found by trying all strings of each length. Track distinct subsequences with a doubling recurrence and subtract the duplicates that come from the last occurrence of each bit.
