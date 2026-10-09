# [2207 — Grundy's Game](https://cses.fi/problemset/task/2207)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** Sprague-Grundy, impartial games, splitting heaps  
**Difficulty / Rating:** Medium–Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There is a heap of `n` coins and two players who move alternately. On each move, a player chooses a heap and divides into two nonempty heaps that have a different number of coins. The player who makes the last move wins the game.

Your task is to find out who wins if both players play optimally.

### Input

The first input line contains an integer `t`: the number of tests.

After this, there are `t` lines that describe the tests. Each line has an integer `n`: the number of coins in the initial heap.

### Output

For each test case, print `"first"` if the first player wins the game and `"second"` if the second player wins the game.

### Constraints

- `1 <= t <= 1e5`
- `1 <= n <= 1e6`

### Examples

**Input**
```
3
6
7
8
```

**Output**
```
first
second
first
```

---

## What this problem really is

Classic **Grundy's game**: split a heap into two **unequal** positive heaps. Multiple heaps appear as the game progresses; the overall game is their Sprague-Grundy XOR. Start from one heap of size `n`.

## What to use

| Tool | Why |
|------|-----|
| Grundy / nimber `g[x]` | Value of a single heap of size `x` |
| Move: `x -> (a) + (x-a)` with `1 <= a < x-a` | Unequal nonempty parts |
| `g[x] = mex { g[a] ^ g[x-a] }` | Definition under Sprague-Grundy |
| Precompute to ~2000 | For this constraint range, all `g[n]=0` losing starts are small; larger `n` are first-win |

## Approach

**Root idea:** heaps of size `<= 2` cannot be split unequally → `g[0]=g[1]=g[2]=0`. Larger heaps move to a **sum** of two independent heaps, whose combined grundy is the XOR of the parts. The starting position is winning iff `g[n] != 0`.

**Mental picture:** breaking a pile into two different-sized piles; the game ends when every pile is size 1 or 2.

**Computation**

```
g[0]=g[1]=g[2]=0
for x = 3 .. LIMIT:
  g[x] = mex over a=1..(x-1)/2 of (g[a] ^ g[x-a])
```

**For CSES `n <= 1e6`:** precompute up to a few thousand (e.g. 2000). Empirically / by full precomputation, every losing single-heap start with `g[n]=0` satisfies `n <= 1222`; for larger `n` answer is always `first`. You can also hardcode the finite losing set:

`{1,2,4,7,10,20,23,26,50,53,270,273,276,282,285,288,316,334,337,340,346,359,362,365,386,389,392,566,630,633,636,639,673,676,682,685,923,926,929,932,1222}`.

**Steps**

1. Precompute `g[0..LIMIT]` (or store the losing set).
2. Per query: if `g[n]==0` (or `n` in losing set) → `second`, else → `first`.

## Walkthrough (on a sample)

- `n=6`: `g[6] != 0` → first.  
- `n=7`: `g[7] = 0` → second.  
- `n=8`: `g[8] != 0` → first.

## Pitfalls

- Allowing equal splits (`n/2 + n/2`) — illegal.  
- Treating the game as a single subtraction without XOR of parts.  
- Slow mex with huge sets inside `O(n^2)` without care — limit the precompute range.  
- Assuming periodicity of Grundy's game (open problem) — do not invent a wrong period formula.

## Complexity

- Time: `O(LIMIT^2)` precompute + `O(t)` queries  
- Space: `O(LIMIT)`

## Key takeaway

**Grundy's game = split into unequal heaps; win iff grundy of `n` is nonzero** — precompute `g[x] = mex { g[a]^g[x-a] }`.
