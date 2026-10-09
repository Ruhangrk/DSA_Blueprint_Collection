# [1729 — Stick Game](https://cses.fi/problemset/task/1729)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** game theory, DP, subtraction game  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Consider a game where two players remove sticks from a heap. The players move alternately, and the player who removes the last stick wins the game.

A set `P = {p[1], p[2], ..., p[k]}` determines the allowed moves. For example, if `P = {1, 3, 4}`, a player may remove `1`, `3` or `4` sticks.

Your task is find out for each number of sticks `1, 2, ..., n` if the first player has a winning or losing position.

### Input

The first input line has two integers `n` and `k`: the number of sticks and moves.

The next line has `k` integers `p[1], p[2], ..., p[k]` that describe the allowed moves. All integers are distinct, and one of them is `1`.

### Output

Print a string containing `n` characters: `W` means a winning position, and `L` means a losing position.

### Constraints

- `1 <= n <= 1e6`
- `1 <= k <= 100`
- `1 <= p[i] <= n`

### Examples

**Input**
```
10 3
1 3 4
```

**Output**
```
WLWWWWLWLW
```

---

## What this problem really is

Classic single-heap subtraction game with fixed move set `P`. Classify each heap size `1..n` as N-position (first wins) or P-position (second wins / first loses).

## What to use

| Tool | Why |
|------|-----|
| Win/lose DP | `lose[i]` if every move leaves a win for the opponent |
| `win[i] = OR over p in P of !win[i-p]` (when `i-p >= 0`) | Standard definition |
| `win[0] = false` | No sticks → previous player already took the last |

## Approach

**Root idea:** a position is losing iff you cannot move to any losing position for the opponent. Equivalently: winning iff there exists `p in P` with `i >= p` and `i-p` losing.

**Mental picture:** from a pile of `i`, you chop off an allowed `p`. If you can leave the opponent on a “dead” pile, you win.

**Steps**

1. `win[0] = false` (or think of 0 sticks as already lost for the player to move).
2. For `i = 1 .. n`:
   - `win[i] = false`
   - For each `p in P` with `p <= i`: if `!win[i-p]`, set `win[i] = true` and break.
3. Print `W` if `win[i]` else `L` for `i = 1..n`.

Because `1` is always in `P`, the recurrence is well-founded and every size is reachable.

## Walkthrough (on a sample)

`P = {1,3,4}`, `n = 10`:

| i | reason | |
|---|--------|---|
| 1 | take 1 → 0 | W |
| 2 | only to 1 (W) | L |
| 3 | to 0 via 3 | W |
| 4 | to 0 via 4 | W |
| … | | |
| Output | `WLWWWWLWLW` | |

## Pitfalls

- Treating `0` as a winning position.
- `O(n*k)` is OK (`1e6 * 100`); do not use slower methods.
- Output must be exactly `n` characters with no spaces.

## Complexity

- Time: `O(n * k)`  
- Space: `O(n)` (or bitset)

## Key takeaway

**Subtraction-game W/L: a size is `W` iff some allowed move reaches an `L` size** — fill `1..n` bottom-up.
