# [1666 — Building Roads](https://cses.fi/problemset/task/1666)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, DFS/BFS, connected components  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Byteland has `n` cities, and `m` roads between them. The goal is to construct new roads so that there is a route between any two cities.

Your task is to find out the minimum number of roads required, and also determine which roads should be built.

### Input

The first input line has two integers `n` and `m`: the number of cities and roads. The cities are numbered `1, 2, ..., n`.

After that, there are `m` lines describing the roads. Each line has two integers `a` and `b`: there is a road between those cities.

A road always connects two different cities, and there is at most one road between any two cities.

### Output

First print an integer `k`: the number of required roads.

Then, print `k` lines that describe the new roads. You can print any valid solution.

### Constraints

- `1 <= n <= 1e5`
- `1 <= m <= 2e5`
- `1 <= a, b <= n`

### Examples

**Input**
```
4 2
1 2
3 4
```

**Output**
```
1
2 3
```

---

## What this problem really is

Undirected graph may have several connected components. Make it connected by adding the fewest edges; also output one such set of edges.

## What to use

| Tool | Why |
|------|-----|
| Adjacency list | Sparse graph, large `n` |
| DFS or BFS / Union-Find | Find connected components |
| List of component reps | One city ID per component (e.g. any city found as a new component start) |

## Approach

**Root idea:** if there are `c` connected components, you need exactly `c - 1` new roads (connect them into a tree of components). Pick one representative city from each component and link them in a chain: `r1-r2`, `r2-r3`, … 

**Mental picture:** islands of cities. Each new bridge merges two islands. Fewest bridges to join `c` islands = `c - 1`.

**Data structures**

- `vector<vector<int>> g`
- `visited[n+1]`
- `vector<int> roots` — one node per component

**Steps**

1. Build the undirected graph from `m` edges.
2. For `city = 1 .. n`, if not visited, DFS/BFS the component and push `city` into `roots`.
3. `k = (int)roots.size() - 1` (or `0` if `n = 0` edge case; here `n >= 1`).
4. Print `k`. For `i = 0 .. k-1`, print `roots[i] roots[i+1]`.

## Walkthrough (on a sample)

`n = 4`, edges `1-2`, `3-4`.

Components: `{1,2}` and `{3,4}` → `c = 2` → need **`1`** road.

Representatives e.g. `1` and `3`. Output `2 3` (or `1 3`, etc.) connects the two components → whole graph connected.

## Pitfalls

- **`c = 1` already** → print `0` and no edges.
- **Isolated cities** — each alone is its own component; don’t skip them.
- **`n, m` large** — iterative DFS/BFS or careful recursion; Union-Find also fine.
- New roads must connect **different** components (using one rep each does that).

## Complexity

- Time: `O(n + m)`  
- Space: `O(n + m)`

## Key takeaway

**Connect `c` components → add `c-1` edges.** Find components, take one city each, link representatives in a chain.
