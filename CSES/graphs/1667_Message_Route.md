# [1667 — Message Route](https://cses.fi/problemset/task/1667)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, BFS, shortest path, path reconstruction  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Syrjälä's network has `n` computers and `m` connections. Your task is to find out if Uolevi can send a message to Maija, and if it is possible, what is the minimum number of computers on such a route.

### Input

The first input line has two integers `n` and `m`: the number of computers and connections. The computers are numbered `1, 2, ..., n`. Uolevi's computer is `1` and Maija's computer is `n`.

Then, there are `m` lines describing the connections. Each line has two integers `a` and `b`: there is a connection between those computers.

Every connection is between two different computers, and there is at most one connection between any two computers.

### Output

If it is possible to send a message, first print `k`: the minimum number of computers on a valid route. After this, print an example of such a route. You can print any valid solution.

If there are no routes, print `"IMPOSSIBLE"`.

### Constraints

- `2 <= n <= 1e5`
- `1 <= m <= 2e5`
- `1 <= a, b <= n`

### Examples

**Input**
```
5 5
1 2
1 3
1 4
2 3
5 4
```

**Output**
```
3
1 4 5
```

---

## What this problem really is

Undirected unweighted graph. Shortest path from `1` to `n` by **number of nodes** on the route (i.e. `edges + 1`). Print the node list or `IMPOSSIBLE`.

## What to use

| Tool | Why |
|------|-----|
| Adjacency list | Large sparse graph |
| BFS from `1` | Unweighted → first reach of `n` is fewest edges |
| `parent[v]` | Reconstruct the computer sequence |

## Approach

**Root idea:** minimize computers on the route = minimize edges, then add 1. BFS finds that.

**Mental picture:** message hops along cables; each hop is one edge. Explore in waves from computer `1` until you hit `n`.

**Data structures**

- `vector<vector<int>> g`
- `parent[n+1]` init `-1`; `parent[1] = 0` (sentinel)
- `queue<int>`, `visited`

**Steps**

1. Build undirected graph.
2. BFS from `1`; when visiting neighbor `v` from `u`, set `parent[v] = u` the first time.
3. If `n` never visited → `IMPOSSIBLE`.
4. Walk `n → parent[n] → ... → 1`, reverse the list.
5. Print `k = path.size()`, then the path.

## Walkthrough (on a sample)

Edges link `1` to `2,3,4` and `4-5`. BFS: from `1` reach `4`, then `5`. Path **`1 4 5`**, length **`3`**.

## Pitfalls

- Printing **edge count** instead of **computer count** — sample wants `3` nodes, not `2`.
- Reconstruct forward without parents — store `parent` during BFS.
- `n, m` large — iterative BFS only.

## Complexity

- Time: `O(n + m)`  
- Space: `O(n + m)`

## Key takeaway

**Shortest route in an unweighted graph = BFS + parent pointers.** Node count on the path is `edges + 1`.
