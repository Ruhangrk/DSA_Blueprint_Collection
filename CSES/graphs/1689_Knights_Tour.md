# [1689 — Knight's Tour](https://cses.fi/problemset/task/1689)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, backtracking, Warnsdorff, Hamiltonian path on grid  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given a starting position of a knight on an `8 x 8` chessboard, your task is to find a sequence of moves such that it visits every square exactly once.

On each move, the knight may either move two steps horizontally and one step vertically, or one step horizontally and two steps vertically.

### Input

The only line has two integers `x` and `y`: the knight's starting position.

### Output

Print a grid that shows how the knight moves (according to the example). You can print any valid solution.

### Constraints

- `1 <= x, y <= 8`

### Examples

**Input**
```
2 1
```

**Output**
```
8 1 10 13 6 3 20 17
11 14 7 2 19 16 23 4
26 9 12 15 24 5 18 21
49 58 25 28 51 22 33 30
40 27 50 59 32 29 52 35
57 48 41 44 37 34 31 62
42 39 46 55 60 63 36 53
47 56 43 38 45 54 61 64
```

---

## What this problem really is

Find a **Hamiltonian path** for a knight on an `8x8` board starting at `(x,y)` (1-based). Output the visit order number in each cell (`1..64`).

## What to use

| Tool | Why |
|------|-----|
| DFS / backtracking | Search a path of length 64 |
| Warnsdorff’s rule | Always move to the unused square with fewest onward moves — prunes search heavily |
| 8 knight deltas | Generate legal neighbors on the board |

## Approach

**Root idea:** naive backtracking can explode; ordering candidates by **Warnsdorff** (degree to unused squares) finds a tour quickly on `8x8`.

**Mental picture:** the knight prefers cramped corners early so it doesn’t strand itself with isolated squares.

**Data structures**

- `board[8][8]` visit times (`0` = free).
- Start at `(y-1, x-1)` or match statement’s coordinate convention (`x` column, `y` row — check sample: input `2 1` puts `1` at that square).
- Delta arrays for knight moves.

**Steps**

1. Clear board; place move `1` at the start.
2. Recurse: if move `== 64`, success.
3. Else list unused knight-neighbors; sort by count of their unused neighbors (ascending); try in that order.
4. On success, print the `8x8` grid of times.

## Walkthrough (on a sample)

Start `(x,y)=(2,1)` marked `1`. Following Warnsdorff fills all 64 squares; one valid numbering is the sample grid.

## Pitfalls

- Coordinate swap (`x`/`y` vs row/col).
- Not using a heuristic — pure DFS may TLE.
- Allowing revisits.

## Complexity

- Worst-case exponential; with Warnsdorff, `8x8` is fast in practice.

## Key takeaway

**Knight’s tour on 8×8:** backtracking + Warnsdorff (always go to the square with fewest exits) finds a Hamiltonian path reliably.
