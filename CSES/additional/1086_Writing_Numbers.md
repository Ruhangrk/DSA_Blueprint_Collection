# [1086 — Writing Numbers](https://cses.fi/problemset/task/1086)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems I  
**Tags:** Digit counting, binary search on the answer, monotone predicate, number theory  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You write the positive integers `1, 2, 3, ...` in order. Each digit key `0` to `9` can be pressed at most `n` times in total. What is the last number you can write completely?

### Input

- One line with `n`.

### Output

Print the last number you can write.

### Constraints

- `1 <= n <= 1e18`

### Examples

**Input**
```
5
```

**Output**
```
12
```

**Explanation:** Writing `1..12` presses key `1` five times. Writing `13` would need a sixth `1`.

---

## What this problem really is

Writing `1..X` uses some number of presses for each digit. The count for each digit grows as `X` grows. We want the largest `X` where no digit's count exceeds `n`. Since the counts only grow, "can we write `1..X`?" is a yes-then-no question, so binary search applies.

## What to use

| Tool | Why |
|------|-----|
| Binary search on `X` | The predicate "all digit counts in `1..X` are at most `n`" is monotone |
| Digit-count formula | Counts how many times digit `d` appears in `1..X` without listing numbers |
| 128-bit integers | The answer can be near `1e19`, which overflows signed 64-bit |

## Approach

**Root idea:** to write `1..X`, we need, for every digit `d`, the number of times `d` appears in all numbers from `1` to `X`. If every one of these counts is at most `n`, we can write `1..X`. So find the largest `X` that passes.

**Mental picture:** imagine a long strip of tiles. Each number is a few tiles. Each digit has its own bucket of `n` tiles. Writing numbers in order empties buckets. The moment one bucket is empty and the next number needs that digit, we stop. Counting a digit's tiles in `1..X` can be done place by place, like counting how many times the digit shows up in the ones column, the tens column, and so on.

**How that becomes the method**

1. Let `count(d, X)` = number of times digit `d` appears in the decimal representations of `1..X`.
2. Predicate `ok(X)`: `count(d, X) <= n` for all `d` in `0..9`.
3. Binary search for the largest `X` with `ok(X)`. The upper bound can be `10 * n + 10`, since the digit `1` alone appears at least `X / 10` times among the last digits.
4. Compute `count(d, X)` by looping over each place value `p = 1, 10, 100, ...` up to `X`:
   - `high = X / (p * 10)`, `cur = (X / p) % 10`, `low = X % p`.
   - For `d > 0`: add `high * p`, then add `p` if `cur > d`, add `low + 1` if `cur == d`, add `0` otherwise.
   - For `d = 0`: skip if `high = 0` (no leading zeros). Otherwise add `(high - 1) * p`, then add `p` if `cur > 0`, else add `low + 1`.

**Data structures:**
- Only a few integers. Use `__int128` (or unsigned 64-bit carefully) for counts and the upper bound.

## Walkthrough (on a sample)

`n = 5`.

Check `X = 12`:
- Digit `1`: numbers `1, 10, 11, 11, 12` contain `1` as follows: `1` (once), `10` (once), `11` (twice), `12` (once). Total `5`. Allowed: `5 <= 5`. ✓
- Digit `2`: `2` and `12`. Total `2`. ✓
- Digit `0`: `10`. Total `1`. ✓
- Other digits are fewer than `5`. ✓

So `ok(12)` holds.

Check `X = 13`:
- Digit `1`: the same `5`, plus one more from `13`. Total `6`. `6 > 5`. ✗

So `ok(13)` fails. Binary search over the range finds `12` as the largest passing value.

Output: `12`. Matches.

## Pitfalls

- Overflow. The answer can be near `1e19`, which does not fit in signed 64-bit. Use `__int128` for `X`, counts, and bounds.
- Forgetting that leading zeros do not count. The zero-digit formula must skip positions where `high = 0`.
- Off-by-one in the binary search. Remember to return the last `X` where the predicate holds.
- Counting by listing numbers. That is far too slow for `n = 1e18`.
- Thinking the answer is simply `n` or `10 * n`. The counts of different digits interact through the place values.

## Complexity

- Time: `O(log(n) * 10 * 19)`, about a few thousand operations.
- Space: `O(1)`.

## Key takeaway

When a process only gets harder as you go further, binary search the stopping point. Count each digit's occurrences place by place without listing numbers.
