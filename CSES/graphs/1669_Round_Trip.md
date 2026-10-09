# [1669 — Round Trip](https://cses.fi/problemset/task/1669)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, cycle detection, DFS  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Byteland has `n` cities and `m` roads between them. Your task is to design a round trip that begins in a city, goes through two or more other cities, and finally returns to the starting city. Every intermediate city on the route has to be distinct.

### Input

The first input line has two integers `n` and `m`: the number of cities and roads. The cities are numbered `1, 2, ..., n`.

Then, there are `m` lines describing the roads. Each line has two integers `a` and `b`: there is a road between those cities.

Every road is between two different cities, and there is at most one road between any two cities.

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
5 6
1 3
1 2
5 3
1 5
2 4
4 5
```

**Output**
```
4
3 5 1 3
```

---

## What this problem really is

Undirected graph: find **any simple cycle** of length at least `3` (start repeated at the end in the printed list). Print the city sequence or `IMPOSSIBLE`.

## What to use

| Tool | Why |
|------|-----|
| Adjacency list | Large undirected graph |
| DFS with `parent` | Spot a back edge to a visited non-parent → cycle |
| `parent[]` / stack path | Reconstruct the cycle nodes |

## Approach

**Root idea:** in an undirected graph, a cycle appears in DFS as an edge to an already-visited vertex that is **not** your parent. That back edge closes a loop with the DFS tree path.

**Mental picture:** walk into the city graph without repeating cities on the current trail. If you see a road back to someone still on your trail (other than where you just came from), you’ve found a round trip.

**Data structures**

- `g`, `visited[n+1]`, `parent[n+1]`
- When a cycle is found: from the current node walk via `parent` until the back-edge target, then output that list plus the start again.

**Steps**

1. Build undirected graph.
2. For each unvisited `s`, DFS(`s`, parent=`0`).
3. In DFS at `u`, for each neighbor `v`:
   - If `v == parent[u]`, skip.
   - If `v` visited → cycle: build path `u → ... → v`, then append `v` again (or start from `v` and end at `v`). Length of printed cities is `cycle_nodes + 1` (closing repeat).
   - Else set `parent[v]=u`, recurse.
4. If no cycle in any component → `IMPOSSIBLE`.

Need **at least 3 distinct** intermediate cities in the problem wording: “begins in a city, goes through two or more other cities, and returns” → cycle length ≥ 3 edges / ≥ 3 distinct cities before closing. Ignore tiny weirdness; normal undirected simple cycle ≥ 3 is what you want. (A 2-cycle can’t exist without multi-edges.)

## Walkthrough (on a sample)

One cycle is `3-5-1-3` (print `4` then those cities). Distinct cities `3,5,1`, then return to `3`.

DFS might discover this via a back edge from `1` to `3` while `parent` chain holds `5` and `3`, etc.

## Pitfalls

- **Treating parent as a cycle** — the edge back to parent is not a new cycle.
- **Printing without closing city** — output should repeat the start at the end; `k` includes that repeated visit in the sample (`4` numbers for a 3-city cycle).
- **Stopped after finding one** — any valid cycle is enough.
- Large `n` — iterative stack DFS if recursion depth is a concern.

## Complexity

- Time: `O(n + m)`  
- Space: `O(n + m)`

## Key takeaway

**Undirected cycle = DFS back edge to a visited non-parent.** Reconstruct with parent pointers; print the loop with the start city repeated at the end.
