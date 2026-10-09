# [1099 — Stair Game](https://cses.fi/problemset/task/1099)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** staircase Nim, XOR, game theory  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There is a staircase consisting of `n` stairs, numbered `1, 2, ..., n`. Initially, each stair has some number of balls.

There are two players who move alternately. On each move, a player chooses a stair `k` where `k != 1` and it has at least one ball. Then, the player moves any number of balls from stair `k` to stair `k-1`. The player who moves last wins the game.

Your task is to find out who wins the game when both players play optimally.

Note that if there are no possible moves at all, the second player wins.

### Input

The first input line has an integer `t`: the number of tests. After this, `t` test cases are described:

The first line contains an integer `n`: the number of stairs.

The next line has `n` integers `p[1], p[2], ..., p[n]`: the initial number of balls on each stair.

### Output

For each test, print `"first"` if the first player wins the game and `"second"` if the second player wins the game.

### Constraints

- `1 <= t <= 2e5`
- `1 <= n <= 2e5`
- `0 <= p[i] <= 1e9`
- the sum of all `n` is at most `2e5`

### Examples

**Input**
```
3
3
0 2 1
4
1 1 1 1
2
5 3
```

**Output**
```
first
second
first
```

---

## What this problem really is

**Staircase Nim:** balls only move one step down; stair `1` is a dead end (cannot move from it). Last move wins.

## What to use

| Tool | Why |
|------|-----|
| Staircase-Nim theorem | Only balls on **even 1-based** stairs (2,4,6,...) matter |
| XOR of those counts | Equivalent to ordinary Nim heaps |
| `xor != 0` → first | Same as Nim |

## Approach

**Root idea:** a move from an **odd** 1-based stair (3,5,…) down to an even stair can be mirrored by the opponent (push those balls one step further), so it does not change the “real” game. A move from an **even** stair down to an odd stair permanently removes those balls from the odd/even strategic view — like taking objects from a Nim heap.

**Mental picture:** odd stairs are “mirrors”; even stairs are the actual Nim piles. Stair `1` is the ground trash bin.

**Indexing (1-based `p[1..n]`)**

XOR `p[2] ^ p[4] ^ p[6] ^ ...`  
(equivalently, in 0-based array `a[0..n-1]`, XOR entries with odd index).

**Steps**

1. Read `p[1..n]`.
2. `x = 0`; for `i = 2; i <= n; i += 2`: `x ^= p[i]`.
3. Print `first` if `x != 0`, else `second`.

## Walkthrough (on a sample)

- `0 2 1` → XOR stair 2 = `2` → first.  
- `1 1 1 1` → `1 ^ 1 = 0` → second.  
- `5 3` → XOR stair 2 = `3` → first.

## Pitfalls

- XORing all stairs (including odd ones) — wrong.
- Off-by-one: confusing 0-based “odd index” with 1-based “odd stair.”
- Thinking balls on stair 1 still count as a Nim heap.

## Complexity

- Time: `O(sum n)`  
- Space: `O(1)` extra

## Key takeaway

**Staircase Nim = XOR ball counts on even 1-based stairs** — nonzero XOR means first wins.
