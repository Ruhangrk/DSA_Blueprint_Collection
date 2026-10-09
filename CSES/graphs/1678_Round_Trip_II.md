# [1678 — Round Trip II](https://cses.fi/problemset/task/1678)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, directed cycle, DFS coloring  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Byteland has `n` cities and `m` flight connections. Your task is to design a round trip that begins in a city, goes through one or more other cities, and finally returns to the starting city. Every intermediate city on the route has to be distinct.

### Input

The first input line has two integers `n` and `m`: the number of cities and flights. The cities are numbered `1, 2, ..., n`.

Then, there are `m` lines describing the flights. Each line has two integers `a` and `b`: there is a flight connection from city `a` to city `b`. All connections are one-way flights from a city to another city.

### Output

First print an integer `k`: the number of cities on the route. Then print `k` cities in the order they will be visited. You can print any valid solution.

If there are no solutions, print `"IMPOSSIBLE"`.

### Constraints

- `1 <= n <= 1e5`
- `1 <= m <= 2e5`
- `1 <= a, b <= n`

### Examples

**Input**
```
4 5
1 3
2 1
2 4
3 2
3 4
```

**Output**
```
4
2 1 3 2
```

---

## What this problem really is

**Directed** graph: find any directed cycle (print nodes with start repeated at end), or `IMPOSSIBLE`.

## What to use

| Tool | Why |
|------|-----|
| Directed adj list | One-way flights |
| 3-color DFS (`0` unvis, `1` on stack, `2` done) | Back edge to a `1` node = cycle |
| `parent[]` | Reconstruct the cycle |

## Approach

**Root idea:** in a directed graph, a cycle is a back edge to a vertex still on the current DFS recursion stack (color `1`), not merely “visited before.”

**Mental picture:** follow flights depth-first. If you fly to a city you’re still exploring on this trail, you’ve looped.

**Data structures**

- `g`, `color[n+1]`, `parent[n+1]`
- On back edge `u → v` with `color[v]==1`: walk parents from `u` to `v`, then append `v` again.

**Steps**

1. Build digraph.
2. For each unvisited node, DFS.
3. When neighbor `v` has color `1`, build and print the cycle → done.
4. If none → `IMPOSSIBLE`.

## Walkthrough (on a sample)

Cycle `2 → 1 → 3 → 2`. Printed as `4` cities: **`2 1 3 2`**.

## Pitfalls

- Using undirected “parent skip” logic — wrong for digraphs.
- Treating color `2` as a cycle (forward/cross edges are not cycles by themselves).
- Must close the printed route by repeating the start city.

## Complexity

- Time: `O(n + m)`  
- Space: `O(n + m)`

## Key takeaway

**Directed cycle = DFS edge to a node still on the stack (gray).** Reconstruct with parents; print with the start repeated.
