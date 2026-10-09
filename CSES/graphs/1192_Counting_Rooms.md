# [1192 — Counting Rooms](https://cses.fi/problemset/task/1192)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, dfs, bfs, flood fill, grid  
**Difficulty / Rating:** Easy (introductory grid graphs)  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a map of a building, and your task is to count the number of its rooms. The size of the map is `n x m` squares, and each square is either floor or wall. You can walk left, right, up, and down through the floor squares.

### Input

The first input line has two integers `n` and `m`: the height and width of the map.

Then there are `n` lines of `m` characters describing the map. Each character is either `.` (floor) or `#` (wall).

### Output

Print one integer: the number of rooms.

### Constraints

- `1 <= n, m <= 1000`

### Examples

**Input**
```
5 8
########
#..#...#
####.#.#
#..#...#
########
```

**Output**
```
3
```

---

## What this problem really is

Treat floor cells as nodes; 4-directional adjacency as undirected edges. A **room** is a connected component of floor cells. Count the components.

## What to use

| Tool | Why |
|------|-----|
| Grid-as-graph | Each `.` is a vertex; edges to up/down/left/right neighbors that are also `.` |
| DFS / BFS / flood fill | Visit an entire component from one unvisited floor cell |
| Visited mark (or overwrite `.` → `#`) | Avoid recounting the same room |

## Approach

1. Read the `n x m` grid.
2. Initialize `rooms = 0`.
3. For every cell `(r, c)`:
   - If it is floor and not yet visited, start a DFS/BFS from it, mark the whole reachable floor region visited, and increment `rooms` by `1`.
4. Print `rooms`.

Walls are never entered; they only block movement.

## Walkthrough (on a sample)

Map (0-indexed rows/cols of floor cells):

```
########
#..#...#     row 1: floors at cols 1,2 and 4,5,6
####.#.#     row 2: floors at cols 4 and 6
#..#...#     row 3: floors at cols 1,2 and 4,5,6
########
```

Scan for unvisited `.`:

1. Hit `(1,1)` → flood `{(1,1), (1,2)}`. Blocked south by walls on row 2 → **room 1**. Mark visited.
2. Hit `(1,4)` → flood the right region: `(1,4)(1,5)(1,6)`, down to `(2,4)(2,6)`, down to `(3,4)(3,5)(3,6)`. **Room 2**.
3. Hit `(3,1)` → flood `{(3,1), (3,2)}`. Separated from room 1 by the wall row → **room 3**.

No more unvisited floors → answer `3`.

## Pitfalls

- **4 directions only**: no diagonals — touching at a corner does **not** merge rooms.
- **`n, m = 1000`**: up to `1e6` cells — recursion DFS can blow the call stack; prefer iterative DFS/BFS or raise stack carefully.
- **Not marking visited**: infinite loops or inflated counts.
- **Counting walls or empty maps**: only start floods on `.`; an all-`#` map answers `0`.
- **Off-by-one on bounds**: always check `0 <= nr < n` and `0 <= nc < m` before indexing.

## Complexity

- Time: `O(n * m)` — each cell entered a constant number of times  
- Space: `O(n * m)` for the grid / visited structure (BFS queue worst-case `O(n * m)`)

## Key takeaway

**Count rooms = count connected components on a grid.** Flood fill from every unseen floor cell; each fill is one room. Same pattern as “number of islands.”
