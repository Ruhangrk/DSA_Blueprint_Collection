# [1188 — Bit Inversions](https://cses.fi/problemset/task/1188)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems I  
**Tags:** Runs of equal bits, boundary set, multiset of gap lengths, dynamic updates  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You have a bit string of length `n`. There are `m` changes. Each change flips the bit at position `x`. After each change, report the length of the longest substring whose bits are all equal.

### Input

- The first line is the bit string of length `n`.
- The second line has `m`.
- The third line has `x[1], ..., x[m]`.

### Output

After each change, print the length of the longest block of equal bits.

### Constraints

- `1 <= n, m <= 2e5`
- `1 <= x[i] <= n`

### Examples

**Input**
```
001011
3
3 2 5
```

**Output**
```
4 2 3
```

**Explanation:** The string becomes `000011`, then `010011`, then `010001`.

---

## What this problem really is

The string splits into maximal blocks of equal bits. We need the longest block after every flip. A flip only affects the blocks next to the flipped position, so we can update those locally instead of rescanning the string.

## What to use

| Tool | Why |
|------|-----|
| Boundary set | Records the "walls" between blocks, so the block structure is stored explicitly |
| Multiset of block lengths | Gives the longest block in `O(log n)`, and supports removing and adding lengths |
| Two flip-affected walls | A flip at `x` can only change the walls between `x-1, x` and `x, x+1` |

## Approach

**Root idea:** a wall sits between two neighboring positions whose bits differ. Walls split the string into blocks. A block's length is the distance between two consecutive walls. Flipping bit `x` toggles the wall on its left and the wall on its right. Everything else stays the same.

**Mental picture:** imagine fence posts placed between the bits. A post stands where two neighbors differ. Flipping a bit is like moving the posts on both of its sides, one step each way: a post appears where there was none, or disappears where there was one. Each change only moves two posts, so only two blocks are affected. The longest block is just the longest gap between consecutive posts.

**How that becomes the method**

1. Index the positions `0..n`. Position `p` is a wall if `p = 0`, or `p = n`, or bit `p` differs from bit `p+1` (1-indexed). Store the walls in a sorted set, with `0` and `n` always present.
2. Store the lengths of all gaps between consecutive walls in a multiset.
3. For each change at `x`, toggle the walls at `x-1` and `x` (only those in `1..n-1`, since `0` and `n` are fixed).
   - Toggling a wall at `p`: find its neighbors in the set. Remove the old gap lengths on both sides, then insert or erase `p`, then insert the new gap length.
4. Print the largest element of the multiset.

**Data structures:**
- `set<int>` of walls, including `0` and `n`.
- `multiset<int>` of gap lengths.

## Walkthrough (on a sample)

Start `001011`. Bits: `b1=0, b2=0, b3=1, b4=0, b5=1, b6=1`.

Walls: position `0` (sentinel), `2` (b2 != b3), `3` (b3 != b4), `4` (b4 != b5), and `6` (sentinel). Gaps: `2, 1, 1, 2`. Longest is not needed yet.

**Change 1: flip position 3.** Bits become `0 0 0 0 1 1`. Toggle wall `2` and wall `3`:
- Wall `2` was present, now `b2 = b3 = 0`, so it is removed.
- Wall `3` was present, now `b3 = b4 = 0`, so it is removed.

Walls: `0, 4, 6`. Gaps: `4, 2`. Longest `4`. ✓

**Change 2: flip position 2.** Bits become `0 1 0 0 1 1`. Toggle wall `1` and wall `2`:
- Wall `1` (between `b1` and `b2`) was absent, now `b1=0, b2=1` differ, so it is added.
- Wall `2` (between `b2` and `b3`) was absent, now `b2=1, b3=0` differ, so it is added.

Walls: `0, 1, 2, 4, 6`. Gaps: `1, 1, 2, 2`. Longest `2`. ✓

**Change 3: flip position 5.** Bits become `0 1 0 0 0 1`. Toggle wall `4` and wall `5`:
- Wall `4` (between `b4` and `b5`) was present, now `b4=b5=0`, so it is removed.
- Wall `5` (between `b5` and `b6`) was absent, now `b5=0, b6=1` differ, so it is added.

Walls: `0, 1, 2, 5, 6`. Gaps: `1, 1, 3, 1`. Longest `3`. ✓

Output: `4 2 3`. Matches.

## Pitfalls

- Toggling walls by just checking the bit before and after. Always decide based on the bit values after the flip, not before.
- Forgetting the sentinels `0` and `n`. Without them the first and last blocks are missing.
- Mixing up 0-indexed and 1-indexed positions when naming walls.
- Removing a gap length from the multiset that was never inserted. Remove exactly one copy, not all copies with `erase(value)`.
- Recomputing the whole string each change. That is `O(n * m)`, which is too slow.

## Complexity

- Time: `O((n + m) log n)`.
- Space: `O(n)`.

## Key takeaway

Track changes as walls between blocks instead of as bits. A single flip moves at most two walls, so the longest block can be maintained with a sorted set and a multiset.
