# [2165 — Tower of Hanoi](https://cses.fi/problemset/task/2165)

**Platform:** CSES  
**Source:** CSES Problem Set — Introductory Problems  
**Tags:** recursion, construction  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

The Tower of Hanoi game consists of three stacks (left, middle and right) and `n` round disks of different sizes. Initially, the left stack has all the disks, in increasing order of size from top to bottom.

The goal is to move all the disks to the right stack using the middle stack. On each move you can move the uppermost disk from a stack to another stack. In addition, it is not allowed to place a larger disk on a smaller disk.

Your task is to find a solution that minimizes the number of moves.

### Input

The only input line has an integer `n`: the number of disks.

### Output

First print an integer `k`: the minimum number of moves.

After this, print `k` lines that describe the moves. Each line has two integers `a` and `b`: you move a disk from stack `a` to stack `b`.

### Constraints

- `1 <= n <= 16`

### Examples

**Input**
```
2
```

**Output**
```
3
1 2
1 3
2 3
```

---

## What this problem really is

Classic Tower of Hanoi: move `n` disks from peg `1` to peg `3` using peg `2`, never putting a larger disk on a smaller one. Minimum moves = `2^n - 1`; also print the move list. Stacks numbered `1,2,3`.

## What to use

| Tool | Why |
|------|-----|
| Recursion | Optimal strategy has a clear recursive structure |
| Helper peg | Move `n-1` aside, move largest, move `n-1` on top |
| `n <= 16` | `2^16 - 1 = 65535` moves — OK to list |

## Approach

**Root idea:** to move `n` disks from `src` to `dst` using `aux`:

1. Move `n-1` from `src` to `aux` (using `dst`).
2. Move the largest disk `src → dst`.
3. Move `n-1` from `aux` to `dst` (using `src`).

Base: `n = 1` → single move `src → dst`.

**Mental picture:** clear the tower onto the spare peg, slide the base disk home, rebuild the tower on top.

**Steps**

1. Print `k = (1 << n) - 1`.
2. Recursively emit moves for `solve(n, 1, 3, 2)`.

## Walkthrough (on a sample)

`n=2`, move from 1 to 3 via 2:

- Move top from 1→2  
- Move base 1→3  
- Move top 2→3  

Three moves — matches the sample.

## Pitfalls

- Wrong peg numbering (left=1, middle=2, right=3).
- Trying iterative “shortest path” search — recursion is enough and optimal.
- Forgetting to print `k` first.

## Complexity

- Time: `O(2^n)` to generate all moves  
- Space: `O(n)` recursion depth

## Key takeaway

**Hanoi = move `n-1` aside, move base, move `n-1` on top** — `2^n - 1` moves, print them recursively.
