# [2075 — Reversal Sorting](https://cses.fi/problemset/task/2075)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems II  
**Tags:** Permutation sorting, reversals, greedy placement, implicit treap  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a permutation of `1..n`. Sort it in increasing order using subarray reversals. Use at most `n` reversals. Print the number of reversals, then each reversal as `a b` (reverse positions `a` to `b`).

### Input

- The first line has `n`.
- The second line has the permutation.

### Output

Print `k`, then `k` lines with the reversals.

### Constraints

- `1 <= n <= 2e5`

### Examples

**Input**
```
4
2 3 1 4
```

**Output**
```
2
1 3
2 3
```

---

## What this problem really is

We fix the array one position at a time from the left. To put the right value at position `i`, we reverse a block that starts at `i` and ends where that value currently sits. The reversal is a single operation, so each position costs at most one reversal, which keeps the total within `n`.

## What to use

| Tool | Why |
|------|-----|
| Left-to-right placement | Once a position is correct, later reversals starting at or after it never touch it |
| Reversal `[i, p]` | Brings value `i` from position `p` to position `i`, since all smaller values are already to the left of `i` |
| Implicit treap with parent pointers | Reversing a block and finding a value's current position both take `O(log n)` |

## Approach

**Root idea:** at step `i`, positions `1..i-1` already hold values `1..i-1`. So every value still to place is at least `i`, and value `i` sits at some position `p >= i`. Reversing `[i, p]` moves value `i` to position `i`, and it only permutes values that are all `>= i`. The prefix stays correct.

**Mental picture:** think of a row of cards. The first `i - 1` cards are already sorted and locked. The card you need is somewhere to the right. Flip the stretch of cards from the locked boundary to that card, and the needed card lands right at the boundary. The locked cards are never flipped.

**How that becomes the method**

1. For `i = 1..n`:
   - Find `p`, the current position of value `i`.
   - If `p != i`, record the reversal `(i, p)` and reverse the block `[i, p]`.
2. Output the recorded reversals.

The number of reversals is at most `n`, one per position.

**Why the array operations must be fast:** a plain array reversal costs `O(p - i)`, which can total `O(n^2)` for `n = 2e5`. An implicit treap supports reversing a range by lazily flipping a flag, and it keeps parent pointers so that the current position of a given node can be found by walking up. Each step is then `O(log n)`.

**Data structures:**
- Implicit treap over the array, with a lazy reverse flag.
- A map from each value to its treap node, so value `i` can be located.
- The list of recorded reversals.

## Walkthrough (on a sample)

`x = 2 3 1 4`, `n = 4`.

- `i = 1`: value `1` is at position `3`. Record `(1, 3)` and reverse positions `1..3`: `[2, 3, 1, 4]` becomes `[1, 3, 2, 4]`.
- `i = 2`: value `2` is at position `3`. Record `(2, 3)` and reverse positions `2..3`: `[1, 3, 2, 4]` becomes `[1, 2, 3, 4]`.
- `i = 3`: value `3` is at position `3`. No reversal.
- `i = 4`: value `4` is at position `4`. No reversal.

Output: `2`, `1 3`, `2 3`. Matches the expected output.

## Pitfalls

- Using `O(n)` array reversals and scans. That is `O(n^2)`, which is too slow for `n = 2e5`.
- Reversing `[p, i]` instead of `[i, p]`. The block must start at the fixed boundary.
- Recording a reversal when `p == i`. Only record when a move happens.
- Losing track of positions after a reversal. Every reversal changes positions of many values, so the position lookup must reflect the current array.
- Assuming the number of reversals can exceed `n`. The greedy uses at most one per position.

## Complexity

- Time: `O(n log n)` with the implicit treap.
- Space: `O(n)`.

## Key takeaway

Fix a permutation from the left: each step reverses from the boundary to the wanted value, so one reversal per position suffices. Using an implicit treap makes the reversals and position lookups fast enough.
