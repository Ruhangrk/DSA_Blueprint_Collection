# [1193 — Labyrinth](https://cses.fi/problemset/task/1193)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, BFS, shortest path, path reconstruction  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a map of a labyrinth, and your task is to find a path from start to end. You can walk left, right, up and down.

### Input

The first input line has two integers `n` and `m`: the height and width of the map.

Then there are `n` lines of `m` characters describing the labyrinth. Each character is `.` (floor), `#` (wall), `A` (start), or `B` (end). There is exactly one `A` and one `B` in the input.

### Output

First print `"YES"`, if there is a path, and `"NO"` otherwise.

If there is a path, print the length of the shortest such path and its description as a string consisting of characters `L` (left), `R` (right), `U` (up), and `D` (down). You can print any valid solution.

### Constraints

- `1 <= n, m <= 1000`

### Examples

**Input**
```
5 8
########
#.A#...#
#.##.#B#
#......#
########
```

**Output**
```
YES
9
LDDRRRRRU
```

---

## What this problem really is

Unweighted grid graph (4-neighbors, walls blocked). Find a **shortest** path from `A` to `B` and print its move string (or `NO`).

## What to use

| Tool | Why |
|------|-----|
| BFS | Equal edge weights → first time you reach `B` is shortest |
| Queue of cells | Standard BFS frontier |
| Parent / move from parent | Reconstruct the path string after BFS |
| Visited grid | Avoid revisiting; `n*m <= 1e6` |

## Approach

**Root idea:** each step costs `1`, so BFS layers are exact distances. Remember, for every cell, which move led into it; then walk backward from `B` to `A` and reverse the string.

**Mental picture:** flood fill from `A` like a wave. When the wave first touches `B`, freeze that route and read the arrows back to the start.

**Data structures**

- `char g[n][m]`; find coordinates of `A` and `B`.
- `dist[n][m]` or just `visited`.
- `parent_move[n][m]` storing `'L'/'R'/'U'/'D'` (move that **arrived** here), or store previous cell coordinates.
- `queue<pair<int,int>>`.

**Steps**

1. Locate `A`, push it, mark visited.
2. BFS: for each cell, try 4 directions; skip walls/`#` and OOB; on success store the move char and enqueue.
3. If `B` never reached → print `NO`.
4. Else walk from `B` using stored moves backward to `A`, collect chars, reverse → path string. Length = string size.
5. Print `YES`, length, string.

## Walkthrough (on a sample)

Start `A` at roughly `(1,2)`, end `B` at `(2,6)` (0-based). BFS finds a shortest path of length **9**, one description `LDDRRRRRU` (any shortest is OK).

The reconstruction: from `B`, parent move `U` means you came from below, step back, keep going until `A`, then reverse the recorded moves.

## Pitfalls

- **DFS for “a” path** may find a long route — judge wants **shortest** length (any shortest string).
- **Reconstruct direction carefully** — store the move that entered the cell, or store previous coordinates and derive the letter when walking back.
- **`A` and `B` are walkable** — treat them as floors.
- Stack recursion DFS can blow on `1000x1000`; prefer iterative BFS.

## Complexity

- Time: `O(n * m)`  
- Space: `O(n * m)`

## Key takeaway

**Shortest path in an unweighted grid = BFS + parent pointers.** First hit on the target is optimal; walk parents to rebuild the move string.
