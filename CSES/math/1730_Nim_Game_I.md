# [1730 — Nim Game I](https://cses.fi/problemset/task/1730)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** Nim, XOR, Sprague-Grundy  
**Difficulty / Rating:** Easy  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` heaps of sticks and two players who move alternately. On each move, a player chooses a non-empty heap and removes any number of sticks. The player who removes the last stick wins the game.

Your task is to find out who wins if both players play optimally.

### Input

The first input line contains an integer `t`: the number of tests. After this, `t` test cases are described:

The first line contains an integer `n`: the number of heaps.

The next line has `n` integers `x[1], x[2], ..., x[n]`: the number of sticks in each heap.

### Output

For each test case, print `"first"` if the first player wins the game and `"second"` if the second player wins the game.

### Constraints

- `1 <= t <= 2e5`
- `1 <= n <= 2e5`
- `1 <= x[i] <= 1e9`
- the sum of all `n` is at most `2e5`

### Examples

**Input**
```
3
4
5 7 2 5
2
4 1
3
3 5 6
```

**Output**
```
first
first
second
```

---

## What this problem really is

Standard multi-heap Nim (normal play: last move wins). Decide first- or second-player win under optimal play.

## What to use

| Tool | Why |
|------|-----|
| Nimbers / XOR | Grundy of a heap of size `x` is `x` itself (can move to any smaller size) |
| Sprague-Grundy theorem | Combined game XOR of heap grundies |
| `xor == 0` → second wins | Classic Nim criterion |

## Approach

**Root idea:** from a heap of size `x` you can move to any heap `0 .. x-1`, so the mex of `{0,1,...,x-1}` is `x`. Independent heaps combine by XOR.

**Mental picture:** each heap is a binary “balance token.” XOR all heap sizes; if the total is nonzero, first can always rebalance to XOR zero; if already zero, every move breaks balance and second can restore it.

**Steps**

1. Read heaps; compute `xorsum = x[1] ^ x[2] ^ ... ^ x[n]`.
2. If `xorsum != 0` print `first`, else print `second`.

## Walkthrough (on a sample)

- `5^7^2^5 = 5^7^2^5`. Note `5^5 = 0`, so `7^2 = 5 != 0` → first.  
- `4^1 = 5 != 0` → first.  
- `3^5^6 = 3^5^6 = 0` → second.

## Pitfalls

- Misreading as misère Nim (last move loses) — here last move wins.
- Thinking you must remove from only one specific heap size rule beyond “any positive amount.”
- Slow per-test algorithms — just XOR in `O(n)`.

## Complexity

- Time: `O(sum n)`  
- Space: `O(1)` extra

## Key takeaway

**Standard Nim: first wins iff XOR of all heap sizes is nonzero.**
