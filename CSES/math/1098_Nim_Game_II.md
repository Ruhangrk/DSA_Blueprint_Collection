# [1098 — Nim Game II](https://cses.fi/problemset/task/1098)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** Nim, subtraction game, Grundy, XOR  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` heaps of sticks and two players who move alternately. On each move, a player chooses a non-empty heap and removes `1`, `2`, or `3` sticks. The player who removes the last stick wins the game.

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
4 4 4
```

**Output**
```
first
first
second
```

---

## What this problem really is

Sum of independent heaps where each heap is a subtraction game with moves `{1,2,3}` (not full Nim). Still solved by XOR of Grundy numbers.

## What to use

| Tool | Why |
|------|-----|
| Grundy for one heap | `g(x) = mex { g(x-1), g(x-2), g(x-3) }` (when defined) |
| Period `4` | Pattern `g(x) = x % 4` |
| XOR of `x[i] % 4` | Multi-heap Sprague-Grundy |

## Approach

**Root idea:** restrict removals to at most 3, so a heap of size multiple of 4 is losing if alone (`0,4,8,...` cannot force). The Grundy sequence is `0,1,2,3,0,1,2,3,...`.

**Mental picture:** each heap contributes only its size mod 4 as a Nim heap. Playing optimally is XOR-balancing those residues.

**Why `x % 4`**

- `g(0) = 0`
- From `x`, moves to `x-1,x-2,x-3` → mex of three previous grundies.
- By induction this yields `g(x) = x % 4`.

**Steps**

1. `xorsum = 0`; for each heap `x`: `xorsum ^= (x % 4)`.
2. If `xorsum != 0` → `first`, else → `second`.

## Walkthrough (on a sample)

- `5,7,2,5` → `1^3^2^1 = 1^3^2^1`. `1^1=0`, `3^2=1 != 0` → first.  
- `4,1` → `0^1 = 1` → first.  
- `4,4,4` → `0^0^0 = 0` → second.

## Pitfalls

- XORing raw sizes (that is unrestricted Nim, wrong here).
- Using `% 3` instead of `% 4`.
- Misère variants — not this problem.

## Complexity

- Time: `O(sum n)`  
- Space: `O(1)`

## Key takeaway

**Remove 1–3 only → heap grundy is `x % 4`; XOR them all — nonzero means first wins.**
