# [1191 — Cyclic Array](https://cses.fi/problemset/task/1191)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems I  
**Tags:** Greedy partition, circular array, binary lifting, two pointers  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You have a cyclic array of `n` values. Divide it into contiguous pieces (a piece may wrap around from the end to the start) so that each piece has sum at most `k`. Minimize the number of pieces.

### Input

- The first line has `n` and `k`.
- The next line has `x[1..n]`.

It is guaranteed that no value exceeds `k`.

### Output

Print one integer: the minimum number of pieces.

### Constraints

- `1 <= n <= 2e5`
- `1 <= x[i] <= 1e9`
- `1 <= k <= 1e18`

### Examples

**Input**
```
8 5
2 2 2 1 3 1 2 1
```

**Output**
```
3
```

**Explanation:** The pieces `[2,2,1]`, `[3,1]`, and `[2,1,2]` each have sum at most 5.

---

## What this problem really is

On a line, the greedy rule "take as much as you can in each piece" gives the minimum number of pieces. On a circle, we do not know where to cut first. The trick is to try every possible starting cut, but do it quickly.

## What to use

| Tool | Why |
|------|-----|
| Greedy from a fixed start | On a line, taking the longest allowed piece each time is optimal |
| Next-jump table | For each position, the position after one greedy piece |
| Binary lifting | Counts how many greedy pieces cover `n` elements from a given start, in `O(log n)` |
| Doubled array | Makes wrap-around pieces ordinary intervals |

## Approach

**Root idea:** if we cut the circle at a fixed starting point, the rest is a line problem. For a line, the greedy choice is optimal: take the longest piece that fits, then continue. The answer is the minimum over all starting points of the number of greedy pieces needed to cover one full lap.

**Mental picture:** imagine the array laid out twice, end to end, so a wrap-around piece is just a normal piece in the longer strip. From a start position, each greedy piece is a jump forward as far as the sum allows. Jumps form a chain. We want, for each start, how many jumps are needed to go a full lap. Binary lifting lets us answer that by doubling the jump lengths, instead of walking the chain.

**How that becomes the method**

1. Double the array to length `2n`.
2. Use two pointers to compute `nxt[i]`: the first index `j` such that the sum of `x[i..j-1]` is more than `k`, capped at `2n`. So one greedy piece starting at `i` ends just before `nxt[i]`.
3. Build binary lifting: `up[0][i] = nxt[i]`, and `up[t][i] = up[t-1][up[t-1][i]]`.
4. For each start `s` in `0..n-1`, the target is to reach index `s + n`:
   - Start at `pos = s` with `cnt = 0`.
   - From the largest power down to `0`, if `up[t][pos] < s + n`, jump: `pos = up[t][pos]`, `cnt += 2^t`.
   - Then one more piece finishes the lap, so the count for this start is `cnt + 1`.
5. The answer is the minimum over all starts.

**Data structures:**
- Prefix sums over the doubled array (`long long`, since sums reach `4e14`).
- `up[t][i]` table, about `18` levels for `2n = 4e5`.

## Walkthrough (on a sample)

`n = 8`, `k = 5`, `x = 2 2 2 1 3 1 2 1`. Use 1-based positions for this walk.

Consider the start at position `2` (the sample explanation's first piece starts here).

Greedy pieces from position `2`:
- Piece 1: `x2 + x3 + x4 = 2 + 2 + 1 = 5`. Adding `x5 = 3` gives `8`, so stop. Piece covers positions `2..4`. Next start `5`.
- Piece 2: `x5 + x6 = 3 + 1 = 4`. Adding `x7 = 2` gives `6`, so stop. Piece covers `5..6`. Next start `7`.
- Piece 3: `x7 + x8 + x1 = 2 + 1 + 2 = 5` (wrapping to position `1`). Adding `x2` would exceed, so stop. Piece covers `7, 8, 1`. Next start `2`, which is where we began, and one full lap of `8` positions is covered.

So from start `2`, the greedy count is `3`.

Check the lap condition: the pieces cover `2..4` (3), `5..6` (2), `7..8,1` (3), total `8`. ✓

In the doubled array, the same walk is `2 -> 5 -> 7 -> 10`. The target for start `2` is `s + n = 2 + 8 = 10`, which the third jump reaches exactly. Binary lifting only takes jumps that stay strictly below the target: here `2 -> 5` and `5 -> 7`, which is `2` jumps. Then one more piece finishes the lap, giving `2 + 1 = 3`.

The minimum over all starts is `3` (the sample answer). Matches.

## Pitfalls

- Starting at a single fixed point. The best cut can be anywhere, so all starts must be tried.
- Forgetting the doubled array. Without it, pieces that wrap around are missed.
- Overflow in prefix sums: `k` up to `1e18`, and sums up to `4e14`, which fit in 64-bit. Use `long long` for sums and compare with `k`.
- Off-by-one when checking the lap: the target is `s + n`, not `s + n - 1`.
- Using `O(n^2)` checking every start by walking the chain. Binary lifting avoids this.

## Complexity

- Time: `O(n log n)`.
- Space: `O(n log n)` for the lifting table.

## Key takeaway

Circular partition becomes a linear one after cutting at a start. Greedy solves each linear case, and binary lifting makes trying every start fast.
