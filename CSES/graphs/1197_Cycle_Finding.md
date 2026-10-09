# [1197 — Cycle Finding](https://cses.fi/problemset/task/1197)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, Bellman-Ford, negative cycle  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a directed graph, and your task is to find out if it contains a negative cycle, and also give an example of such a cycle.

### Input

The first input line has two integers `n` and `m`: the number of nodes and edges. The nodes are numbered `1, 2, ..., n`.

After this, the input has `m` lines describing the edges. Each line has three integers `a`, `b`, and `c`: there is an edge from node `a` to node `b` whose length is `c`.

### Output

If the graph contains a negative cycle, print first `"YES"`, and then the nodes in the cycle in their correct order. If there are several negative cycles, you can print any of them. If there are no negative cycles, print `"NO"`.

### Constraints

- `1 <= n <= 2500`
- `1 <= m <= 5000`
- `1 <= a, b <= n`
- `-1e9 <= c <= 1e9`

### Examples

**Input**
```
4 5
1 2 1
2 4 1
3 1 1
4 1 -3
4 3 -2
```

**Output**
```
YES
1 2 4 1
```

---

## What this problem really is

Directed graph with possibly negative edge weights. Detect a **negative cycle** and print one cycle’s node list (start repeated at end), or `NO`.

## What to use

| Tool | Why |
|------|-----|
| Bellman-Ford from a super-source / all zeros | Cycle may be anywhere, not only from node `1` |
| `parent[v]` | Reconstruct cycle after an improving edge on the `n`-th pass |
| Edge list | BF relaxes all edges each round |

## Approach

**Root idea:** after `n-1` successful relaxations, any further improvement means a negative cycle. Follow parent pointers from a node that still improves; eventually you enter the cycle — walk until repeat.

**Mental picture:** shortest-path estimates keep dropping forever along a negative loop. Parent links draw the loop when that happens.

**Data structures**

- Edges `{a,b,c}`
- `dist[n+1]` init `0` for all nodes (or connect virtual source `0` to every node with weight `0` and start BF from `0`) — so every component is reached
- `parent[n+1]`

**Steps**

1. Init `dist` to `0` (virtual connectivity), `parent` to `0`.
2. Repeat `n` times: for every edge `a→b` with `dist[b] > dist[a] + c`, set `dist[b] = dist[a] + c`, `parent[b] = a`, remember `x = b` on the **n-th** round.
3. If no update on round `n` → `NO`.
4. Else from `x`, walk `parent` about `n` times to land on the cycle, then walk until you return to that node, collecting the cycle; reverse if needed; print `YES` and nodes with start repeated.

## Walkthrough (on a sample)

Cycle `1 → 2 → 4 → 1` has weights `1+1+(-3) = -1` < 0. BF’s last-pass update points into this loop; parent reconstruction yields **`1 2 4 1`**.

## Pitfalls

- Starting BF only from node `1` misses cycles in other components — use all-zero init or a super-source.
- Printing a path that isn’t closed — must repeat the first city at the end.
- Walking parents fewer than `n` steps may stop before entering the cycle.

## Complexity

- Time: `O(n * m)`  
- Space: `O(n + m)`

## Key takeaway

**Negative cycle = improvement after `n-1` Bellman-Ford rounds.** Keep parents on relaxations; climb them to extract one cycle.
