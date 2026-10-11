# [2059D — Graph and Graph](https://codeforces.com/problemset/problem/2059/D)

**Platform:** Codeforces
**Source:** Codeforces Round #1002 (per editorial title)
**Tags:** data structures, graphs, greedy, shortest paths
**Difficulty / Rating:** 1900
**Time limit:** 2 seconds
**Memory limit:** 256 megabytes
**Editorial:** [Codeforces Round #1002 Editorial (Problem D section)](https://codeforces.com/blog/entry/139094)

---

## Problem Statement

You are given two connected undirected graphs with the same number of vertices. In both graphs, there is a token located at some vertex. In the first graph, the token is initially at vertex `s1`, and in the second graph, the token is initially at vertex `s2`.

The following operation is repeated an infinite number of times:

- Let the token currently be at vertex `v1` in the first graph and at vertex `v2` in the second graph.
- A vertex `u1`, adjacent to `v1`, is chosen in the first graph.
- A vertex `u2`, adjacent to `v2`, is chosen in the second graph.
- The tokens are moved to the chosen vertices: in the first graph, the token moves from `v1` to `u1`, and in the second graph, from `v2` to `u2`.
- The cost of such an operation is equal to `|u1 - u2|`.

Determine the minimum possible total cost of all operations or report that this value will be infinitely large.

### Input

Each test consists of multiple test cases. The first line contains one integer `t` (`1 <= t <= 500`), the number of test cases.

The first line of each test case contains three integers `n`, `s1`, `s2` (`2 <= n <= 1000`, `1 <= s1, s2 <= n`).

The second line contains one integer `m1` (`1 <= m1 <= 1000`), the number of edges in the first graph.

The next `m1` lines each contain two integers `a[i]`, `b[i]` (`1 <= a[i], b[i] <= n`, `a[i] != b[i]`), the endpoints of an edge in the first graph.

The next line contains one integer `m2` (`1 <= m2 <= 1000`), the number of edges in the second graph.

The next `m2` lines each contain two integers `c[j]`, `d[j]` (`1 <= c[j], d[j] <= n`, `c[j] != d[j]`), the endpoints of an edge in the second graph.

The sum of `n`, the sum of `m1`, and the sum of `m2` over all test cases do not exceed 1000.

Both graphs are connected.

### Output

For each test case, output one integer: the minimum total cost of all operations, or `-1` if this value will be infinitely large.

### Constraints

- `1 <= t <= 500`
- `2 <= n <= 1000`
- `1 <= s1, s2 <= n`
- `1 <= m1, m2 <= 1000`
- Sums of `n`, `m1`, `m2` over all tests `<= 1000`
- Both graphs are connected

### Examples

Input:

```
3
4 1 1
4
1 2
2 3
3 4
4 1
4
1 2
2 3
3 4
4 1
4 1 2
4
1 2
2 3
3 4
4 1
4
1 2
2 3
3 4
4 1
7 7 2
7
1 6
2 1
3 2
3 4
5 1
7 3
7 5
6
5 1
5 6
5 7
6 3
7 2
7 4
```

Output:

```
0
-1
7
```

### Note

In the first test case, an infinite sequence of transitions can be constructed to the vertices `2, 3, 4, 1, 2, 3, 4, 1, ...`, along which the token can move in both the first and the second graphs.

In the second test case, it can be proven that the cost of any operation will be greater than `0`; therefore, the total cost of all operations will be infinitely large.

---

## What this problem really is

Two tokens walk on two graphs at the same time, one step each per operation, and each step costs the gap between the two new positions. We want the cheapest infinite walk. A walk costs `0` forever only if both tokens can sit on the same vertex number and bounce along an edge that exists in both graphs. So the question becomes: what is the cheapest way to reach a "meeting vertex" where both tokens stand on the same number and that number has a shared edge?

## What to use

| Tool | Why |
|------|-----|
| Pair state `(v1, v2)` | Each configuration of the two tokens is one node of a new graph with `n*n` nodes. |
| Dijkstra on the pair graph | Every move costs `|u1 - u2| >= 0`, so the cheapest path to each pair is well defined. |
| Shared-edge marking | A vertex is "good" if an edge `(v, u)` exists in both graphs. Once both tokens sit on a good `v`, they can bounce along that edge at cost `0` forever. |

## Approach

**Root idea:** Infinite total cost is avoided only by reaching the diagonal state `(v, v)` where `v` is good. Anything else either costs something on every loop or cannot be stabilised. So the answer is the shortest distance from `(s1, s2)` to any diagonal pair `(v, v)` with `v` good.

**Mental picture:** Picture a grid of `n` by `n` cells. Cell `(a, b)` means "token one is at `a`, token two is at `b`". A move is a step from one cell to a neighbouring cell, where the neighbours are the product of the two adjacency lists. Each step is priced by how far apart the two new coordinates are. The goal is to reach the main diagonal at a cell whose index is a good vertex.

**How that becomes the method:**
1. Mark good vertices: for every edge in graph 2, if the same edge (unordered) also exists in graph 1, mark both endpoints as good.
2. Run Dijkstra on pair states starting from `(s1, s2)` with distance `0`. From state `(v1, v2)`, for every neighbour `u1` of `v1` in graph 1 and every neighbour `u2` of `v2` in graph 2, relax the state `(u1, u2)` with cost `dist + |u1 - u2|`.
3. The answer is the minimum `dist[i][i]` over all good `i`. If no good diagonal state is reachable, the answer is `-1`.

**Why the diagonal + good condition is enough:** Reaching `(v, v)` with `v` good gives a cost-`0` loop: both tokens use the shared edge `v <-> u` back and forth, and the two steps cost `|u - u| = 0`. Conversely, any infinite walk with finite total cost must eventually stay in a bounded set of states, which forces it to end in such a loop, so reaching a good diagonal state is what matters.

**Data structures:**
- `dist[v1][v2]`: best known cost to reach pair `(v1, v2)`.
- Adjacency lists `g1`, `g2`: used for the product transitions.
- A priority queue (a `set` of `(dist, (v1, v2))` in the editorial) to always expand the cheapest pair first.
- `good[v]`: boolean, true if a shared edge touches `v`.

## Walkthrough (on a sample)

Use test 2: `n = 4`, `s1 = 1`, `s2 = 2`, and both graphs are the same 4-cycle `1-2-3-4-1`. Every edge is shared, so every vertex is good.

Start: `dist[1][2] = 0`.

- Pop `(1, 2)` with dist `0`. Token one from `1` can go to `2` or `4`. Token two from `2` can go to `1` or `3`. Relax `(2,1)` cost `1`, `(2,3)` cost `1`, `(4,1)` cost `3`, `(4,3)` cost `1`.
- Pop `(2,1)` with dist `1`. Token one from `2` can go to `1` or `3`; token two from `1` can go to `2` or `4`. Relax `(1,2)` (no gain), `(1,4)` cost `1 + |1 - 4| = 4`, `(3,2)` cost `1 + |3 - 2| = 2`, `(3,4)` cost `1 + |3 - 4| = 2`.
- Keep popping. Every state reached has one token on an odd vertex and the other on an even vertex.

The key is the parity pattern, not the numbers. Vertex `1` is odd and vertex `2` is even. Every edge in a 4-cycle joins an odd vertex to an even vertex, so after each operation both tokens flip parity. Token one stays on odd vertices at even steps, and token two stays on even vertices at even steps. The parities of the two tokens always differ, so a state `(v, v)` (same vertex, so same parity) is never reachable. Every `dist[i][i]` stays `INF`, and the answer is `-1`.

Test 1 is the trivial case: `s1 = s2 = 1`, so the start state is already a diagonal state. Vertex `1` is good (edge `1-2` is in both graphs), so `dist[1][1] = 0` and the answer is `0`.

## Pitfalls

- **Forgetting that the edge must be identical in both graphs:** Sharing only a vertex is not enough. The loop needs the same pair of endpoints in both graphs.
- **Ignoring parity or reachability:** A diagonal state may be unreachable (test 2). Always initialise `dist` to infinity and output `-1` when no good diagonal has finite distance.
- **Checking the start only:** The start can be on the diagonal, but the answer still needs the minimum over all good diagonal states.
- **Using `int` for infinity sums:** Costs can be large over many steps; use 64-bit values to be safe.
- **Only considering one graph's neighbours:** Every transition is the product of neighbours in both graphs, so a move is a pair choice, not two independent moves.

## Complexity

- Time: the total number of relaxations is bounded by `m1 * m2` (each pair of edges from the two graphs is used once per state), each costing a priority-queue operation. With the given limits this is comfortably fast.
- Space: `O(n * n)` for `dist`, plus `O(n + m1 + m2)` for adjacency lists.

## Key takeaway

When two independent walkers must cooperate and the goal is "zero cost forever", model the joint positions as a product state graph, run shortest paths on it, and check only the states where the walkers meet on a shared zero-cost loop.
