# [1194 — Monsters](https://cses.fi/problemset/task/1194)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, multi-source BFS, grid  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You and some monsters are in a labyrinth. When taking a step to some direction in the labyrinth, each monster may simultaneously take one as well. Your goal is to reach one of the boundary squares without ever sharing a square with a monster.

Your task is to find out if your goal is possible, and if it is, print a path that you can follow. Your plan has to work in any situation; even if the monsters know your path beforehand.

### Input

The first input line has two integers `n` and `m`: the height and width of the map.

After this there are `n` lines of `m` characters describing the map. Each character is `.` (floor), `#` (wall), `A` (start), or `M` (monster). There is exactly one `A` in the input.

### Output

First print `"YES"` if your goal is possible, and `"NO"` otherwise.

If your goal is possible, also print an example of a valid path (the length of the path and its description using characters `D`, `U`, `L`, and `R`). You can print any path, as long as its length is at most `n * m` steps.

### Constraints

- `1 <= n, m <= 1000`

### Examples

**Input**
```
5 8
########
#M..A..#
#.#.M#.#
#M#..#..
#.######
```

**Output**
```
YES
5
RRDDR
```

---

## What this problem really is

Grid, 4-moves, walls. You and monsters move **at the same speed**. You need a path to any **border** cell such that for every cell on your path, you arrive **strictly before** any monster can (monsters play adversarially). Print one such path or `NO`.

## What to use

| Tool | Why |
|------|-----|
| Multi-source BFS from all `M` | `mon_dist[r][c]` = earliest time any monster reaches the cell |
| BFS from `A` | Your arrival time; only enter a cell if `your_time < mon_dist` |
| Parent moves | Reconstruct path to the border exit you found |

## Approach

**Root idea:** because monsters know your plan and move optimally to intercept, a cell is safe for you at time `t` only if no monster can be there at time `<= t`. Precompute monster ETA with one multi-source BFS; then BFS yourself only through cells where you are faster.

**Mental picture:** monsters flood the maze together from every `M`. You run on cells that stay dry for one extra turn compared to their flood.

**Data structures**

- Grid; list of monster cells; start `A`.
- `mon[n][m]` init `INF`; BFS queue seeded with all `M` at time `0`.
- `distA`, `parent_move` for your BFS.
- Border = row `0` or `n-1` or col `0` or `m-1`.

**Steps**

1. Multi-source BFS → `mon[r][c]`.
2. BFS from `A` with `time = 0`. When moving to neighbor at `ntime = time+1`, require floor and `ntime < mon[nr][nc]` (and not wall).
3. If you ever dequeue a **border** cell (including start if `A` is already on border), success — reconstruct path from that cell back to `A`.
4. Else `NO`.

Note: sharing a square is forbidden, so equality `ntime == mon[...]` is unsafe.

## Walkthrough (on a sample)

Monsters’ flood fills times on the grid; from `A`, one safe escape is **`RRDDR`** length `5` reaching the right/bottom border before monsters can occupy those cells.

If `A` starts on the border already, answer can be `YES` / `0` / empty path.

## Pitfalls

- **`<=` vs `<`** against monster times — equal time means you meet → invalid.
- Forgetting **multi-source** (running separate BFS per monster is slower but OK; wrong is only BFS from nearest monster incorrectly).
- Start on border → immediate `YES`.
- Path length cap `n*m` is fine for a simple path.

## Complexity

- Time: `O(n * m)`  
- Space: `O(n * m)`

## Key takeaway

**Adversarial same-speed monsters → compare two BFS clocks.** Multi-source monster distances, then your BFS only on cells you reach strictly sooner; exit on the border.
