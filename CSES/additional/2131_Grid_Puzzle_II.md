# [2131 — Grid Puzzle II](https://cses.fi/problemset/task/2131)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems II  
**Tags:** Max-weight bipartite b-matching, min-cost max-flow, Dijkstra with potentials  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You have an `n x n` grid where each cell has `c[i][j]` coins. Choose cells so that row `i` contains exactly `a[i]` chosen cells and column `j` contains exactly `b[j]` chosen cells. Maximize the total coins collected, and print one optimal grid. If impossible, print `-1`.

### Input

- The first line has `n`.
- The second line has `a[1..n]`.
- The third line has `b[1..n]`.
- Then `n` lines, each with `n` values `c[i][j]`.

### Output

First print the maximum total coins. Then print the `n` lines describing the chosen cells (`X` chosen, `.` not chosen). If impossible, print `-1`.

### Constraints

- `1 <= n <= 50`
- `0 <= a[i], b[j] <= n`
- `0 <= c[i][j] <= 1000`

### Examples

**Input**
```
5
0 1 3 2 0
1 2 2 0 1
2 5 1 5 1
0 2 5 1 2
3 8 9 3 5
1 4 3 7 3
0 3 6 2 8
```

**Output**
```
32
.....
..X..
.XX.X
XX...
.....
```

---

## What this problem really is

This is Grid Puzzle I with weights. Each cell is an edge between its row and its column, with value `c[i][j]`. We want a subgraph where each row has degree `a[i]` and each column has degree `b[j]`, with maximum total weight. That is a min-cost flow problem on a bipartite network.

## What to use

| Tool | Why |
|------|-----|
| Flow network | Source feeds rows with capacity `a[i]`, rows send to columns through cells, columns drain to sink with capacity `b[j]` |
| Cost `1000 - c[i][j]` on each cell | Turns "maximize coins" into "minimize cost" with non-negative costs |
| Successive shortest paths with Dijkstra and potentials | Finds the min-cost flow quickly, even with about `2500` units of flow |
| Check that the full flow is reached | If the max flow is less than `sum a`, no valid selection exists |

## Approach

**Root idea:** build the network:
- Source `S` to row `i`, capacity `a[i]`, cost `0`.
- Row `i` to column `j`, capacity `1`, cost `1000 - c[i][j]`.
- Column `j` to sink `T`, capacity `b[j]`, cost `0`.

Send `F = sum a` units of flow from `S` to `T`. Each unit that goes row `i` to column `j` means "choose cell `(i, j)`". Because every cell has capacity `1`, each cell is chosen at most once. The cost of a full flow equals `1000 * F - (total coins)`, so minimizing cost maximizes coins: total coins `= 1000 * F - min cost`.

**Mental picture:** rows pour water into a grid of pipes, one pipe per cell, into columns that drain to the sink. Each pipe can carry at most one unit. Each cheap pipe is a cell with many coins. Successive shortest paths repeatedly push one more unit along the currently cheapest route, rerouting earlier units if that makes room.

**How that becomes the method**

1. If `sum a != sum b`, print `-1`.
2. Build the network above, with costs `1000 - c[i][j]` (all non-negative).
3. Run min-cost flow from `S` to `T`, pushing `F` units with Dijkstra and potentials. If the flow reached is less than `F`, print `-1`.
4. Answer = `1000 * F - min cost`.
5. Read the chosen cells from the cell edges that carry flow, and print the grid.

**Data structures:** an adjacency list with edge capacity, cost, and reverse-edge index; potentials; a Dijkstra heap.

## Walkthrough (on a sample)

`n = 5`, `a = 0 1 3 2 0`, `b = 1 2 2 0 1`. Here `F = 6`.

The expected answer `32` comes from a flow that chooses: row 2 column 3 (coins 5), row 3 columns 2, 3, 5 (coins 8, 9, 5), and row 4 columns 1, 2 (coins 1, 4). That is `5 + 22 + 5 = 32`.

Check that this selection satisfies the constraints:
- Row sums: row 2 has 1 ✓, row 3 has 3 ✓, row 4 has 2 ✓, rows 1 and 5 have 0 ✓.
- Column sums: column 1 has 1 (row 4) ✓, column 2 has 2 (rows 3, 4) ✓, column 3 has 2 (rows 2, 3) ✓, column 4 has 0 ✓, column 5 has 1 (row 3) ✓.

The min-cost flow finds the selection of maximum coins among all valid selections, so it reaches `32`. I checked that this selection is valid and totals `32`. I did not trace every augmenting path of the flow by hand, so the optimality here rests on the min-cost flow method rather than on a full hand run.

The output grid is the one in the sample.

## Pitfalls

- Using maximum-weight flow directly with negative costs and Bellman-Ford. Converting to `1000 - c` keeps costs non-negative, so Dijkstra with potentials works.
- Forgetting to subtract from `1000 * F`. The min cost gives the negative of the coins, offset by `1000` per unit.
- Accepting a partial flow. If the flow is less than `F`, the answer is `-1`.
- Mixing up capacities: row-to-source and column-to-sink capacities are `a[i]` and `b[j]`, while each cell edge has capacity `1`.
- Forgetting to read the grid from the flow on the cell edges, not from the costs.

## Complexity

- Time: `O(F * E log V)` with `F <= n^2 = 2500`, `E about n^2 = 2500`, and `V = 2n + 2 = 102`. This is far under the limit.
- Space: `O(n^2)`.

## Key takeaway

A grid selection with row and column counts is a bipartite flow. Turn coins into costs by subtracting from a large constant, and min-cost flow gives the maximum coins with a valid selection.
