# [1670 — Swap Game](https://cses.fi/problemset/task/1670)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems I  
**Tags:** BFS, shortest path, state graph  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a `3 x 3` grid containing the numbers `1,2,...,9`. Your task is to perform a sequence of moves so that the grid will look like this:

```
1 2 3
4 5 6
7 8 9
```

On each move, you can swap the numbers in any two adjacent squares (horizontally or vertically). What is the minimum number of moves required?

### Input

The input has three lines, and each of them has three integers.

### Output

Print one integer: the minimum number of moves.

### Examples

**Input**
```
2 1 3
7 5 9
8 4 6
```

**Output**
```
4
```

---

## What this problem really is

Shortest path on the graph of all permutations of the `3x3` board, edges = adjacent (4-neighbor) swaps.

## What to use

| Tool | Why |
|------|-----|
| BFS | Unweighted moves → minimum moves |
| Compact state id | `9! = 362880` states; encode board as int / factorial number system |
| Visited array | Avoid reprocessing states |

## Approach

**Root idea:** each board is a vertex; one adjacent swap is an edge of length 1. BFS from the start (or from the sorted target) finds the distance.

**Mental picture:** sliding puzzle without a blank — every tile is always filled; you only transpose neighbors. Still a tiny Cayley graph you can flood-fill.

**How that becomes the method**

1. Read 9 numbers into an array `a[0..8]`.
2. Map the array to a state id (e.g. treat as base-9 / pack into a 32-bit int, or `std::array` hashed, or factorial-number ranking of the permutation).
3. Target = sorted `1..9` in row-major order.
4. BFS from the start state; for each state, try all adjacent pairs `(r,c)-(r,c+1)` and `(r,c)-(r+1,c)`, swap, push if unvisited.
5. Distance when target is reached is the answer.

Bidirectional BFS is optional speedup; plain BFS is enough.

**Data structures:** queue; `dist[9!]` or `vis` map; decode/encode helpers.

## Walkthrough (on a sample)

Start differs from identity in several places. BFS finds a sequence of 4 neighbor swaps restoring `1..9`.

## Pitfalls

- Forgetting vertical edges or only allowing “blank” moves (there is no blank).
- Using DFS / Dijkstra with worse constants.
- Incorrect permutation encoding collisions.

## Complexity

- Time: `O(9! * edges_per_state)` ≈ few million ops
- Space: `O(9!)`

## Key takeaway

`3x3` adjacent-swap sorting → BFS over `9!` board states.
