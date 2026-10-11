# [1709 — Coin Grid](https://cses.fi/problemset/task/1709)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems II  
**Tags:** Bipartite graph, minimum vertex cover, König's theorem, maximum matching  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You have an `n x n` grid with some coins. One move removes all coins in a single row or a single column. Find the minimum number of moves that clears the grid, and print the moves.

### Input

- The first line has `n`.
- The next `n` lines each have `n` characters, `.` (empty) or `o` (coin).

### Output

First print `k`, the minimum number of moves. Then print `k` lines, each with `1 r` (row `r`) or `2 c` (column `c`).

### Constraints

- `1 <= n <= 100`

### Examples

**Input**
```
3
..o
o.o
...
```

**Output**
```
2
1 2
2 3
```

---

## What this problem really is

A coin at `(r, c)` is cleared exactly when row `r` or column `c` is chosen. So we need a set of rows and columns that touches every coin. That is a classic "cover all edges" problem if we treat rows and columns as vertices and each coin as an edge between its row and its column.

## What to use

| Tool | Why |
|------|-----|
| Bipartite graph: rows on one side, columns on the other | Each coin is an edge between its row and column |
| Maximum matching (Kuhn's algorithm) | By König's theorem, the minimum vertex cover size equals the maximum matching size |
| Alternating reachability from unmatched rows | Builds the actual minimum cover, not just its size |

## Approach

**Root idea:** choosing rows `R` and columns `C` clears everything exactly when `R` and `C` form a vertex cover of the coin graph. Minimizing moves means minimizing a vertex cover. In a bipartite graph, König's theorem says the minimum vertex cover equals the maximum matching.

**Mental picture:** think of each coin as a bridge between its row and its column. To cut every bridge, you can knock out a row or a column. A matching is a set of bridges that share no endpoints, so each one needs its own knock-out. If you can find that many disjoint bridges, you need at least that many moves, and König says that many always suffice.

**How that becomes the method**

1. Build the bipartite graph: left side rows `1..n`, right side columns `1..n`, and an edge for each coin.
2. Find a maximum matching with Kuhn's algorithm (DFS augmenting paths).
3. Let `U` be the unmatched left vertices. From `U`, walk alternating paths: from a left vertex, go to any right neighbor. From a right vertex, go along its matched edge back to the left. Let `Z` be all reached vertices.
4. The minimum cover is `(left vertices not in Z) ∪ (right vertices in Z)`.
5. Output each chosen row as `1 r` and each chosen column as `2 c`.

**Data structures:** adjacency lists, a match array for each side, and a visited marker for the alternating search.

## Walkthrough (on a sample)

Coins: `(1,3)`, `(2,1)`, `(2,3)`.

**Graph edges:** row 1 to column 3; row 2 to column 1; row 2 to column 3.

**Maximum matching (Kuhn):**
- Row 1 matches column 3.
- Row 2 is tried against column 3 first, but it is taken, and reassigning it is not needed. Row 2 then matches the free column 1.
- Row 3 has no coins, so it stays unmatched.

Matching size `2`, so the answer is `2` moves.

**Alternating search from unmatched rows:** start at row 3. It has no edges, so `Z = {row 3}`.

**Cover:** left vertices not in `Z` are rows `1` and `2`. Right vertices in `Z` are none. Cover `= {row 1, row 2}`.

**Check:** coin `(1,3)` is covered by row 1, coin `(2,1)` by row 2, coin `(2,3)` by row 2. Clearing rows `1` and `2` empties the grid.

Output: `2`, then `1 1` and `1 2`. This is a valid optimal answer, and it differs from the sample, which is allowed.

## Pitfalls

- Using greedy row/column removal. Greedy picking the densest line is not always optimal.
- Forgetting that a row or column with no coins is never needed.
- Mixing up the two sides of the cover in the alternating search. Left vertices are excluded from the cover when reached, right ones are included when reached, so use the König formula exactly.
- Reporting moves in the wrong format. Rows are `1 r`, columns are `2 c`.
- Assuming the cover is unique. Any minimum cover is accepted.

## Complexity

- Time: `O(V * E)` for Kuhn's algorithm, with `V = 2n` and `E` up to `n^2`. For `n = 100` this is tiny.
- Space: `O(n^2)` for the edges.

## Key takeaway

Turning "clear every coin with the fewest row or column moves" into a bipartite vertex cover makes it solvable by maximum matching, and König's theorem gives the cover itself.
