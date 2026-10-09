# [3217 — Knight Moves Grid](https://cses.fi/problemset/task/3217)

**Platform:** CSES  
**Source:** CSES Problem Set — Introductory Problems  
**Tags:** BFS, knight moves, shortest path  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There is a knight on an `n x n` chessboard. For each square, print the minimum number of moves the knight needs to do to reach the top-left corner.

### Input

The only line has an integer `n`.

### Output

Print the number of moves for each square.

### Constraints

- `4 <= n <= 1000`

### Examples

**Input**
```
8
```

**Output**
```
0 3 2 3 2 3 4 5
3 4 1 2 3 4 3 4
...
5 4 5 4 5 4 5 6
```

---

## What this problem really is

Unweighted shortest paths for a knight from every cell to `(1,1)` / `(0,0)`. Same as one BFS from the corner, reading distances on all cells.

## What to use

| Tool | Why |
|------|-----|
| BFS from top-left | Knight graph edges have equal weight |
| 8 knight deltas | `(±1,±2)`, `(±2,±1)` |
| `dist[][]` init `-1` | Mark visited when enqueueing |
| `n <= 1000` | `O(n^2)` BFS is OK |

## Approach

**Root idea:** run BFS starting at the top-left corner; the knight’s move set defines neighbors. Because every move costs `1`, the first time you reach a square is optimal.

**Mental picture:** ink spreading from the corner along knight jumps; the time ink hits a square is the answer.

**Data structures**

- `dist[n][n]`, queue of `(r,c)`.
- Deltas: all 8 knight offsets.

**Steps**

1. Set `dist[0][0] = 0` (0-based); enqueue it; others `-1`.
2. BFS: for each pop, try 8 moves; if inside board and `dist == -1`, set `dist = dist[cur]+1`, enqueue.
3. Print the full `dist` grid.

(No special infinite-board knight formulas needed — the board is finite and large enough that BFS is the reliable approach.)

## Walkthrough (on a sample)

On `8x8`, corner is `0`. A knight from `b1`-style nearby squares needs a few moves (e.g. distance `1` appears at knight-attack squares from the corner such as `(2,1)` and `(1,2)` in 0-based). Matches the sample’s `0` and nearby `3,2,1,...` pattern.

## Pitfalls

- DFS / Dijkstra without 0-1 care — BFS is enough.
- Forgetting some of the 8 deltas.
- 1-based vs 0-based when printing.
- Trying to use unbounded-plane knight distance tables without handling board edges — wrong near borders.

## Complexity

- Time: `O(n^2)`  
- Space: `O(n^2)`

## Key takeaway

**Knight distance to a corner on an `n x n` board = BFS from that corner** with the eight knight moves.
