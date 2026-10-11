# [2164E — Journey](https://codeforces.com/problemset/problem/2164/E)

**Platform:** Codeforces
**Source:** Codeforces Global Round 30 (Div. 1 + Div. 2)
**Tags:** data structures, dfs and similar, dsu, graphs, greedy
**Difficulty / Rating:** 2300
**Time limit:** 3 seconds
**Memory limit:** 512 megabytes
**Editorial:** [Codeforces Global Round 30 Editorial (Problem E section)](https://codeforces.com/blog/entry/148126)

---

## Problem Statement

You are on an undirected connected graph with `n` vertices and `m` weighted edges. Edges are indexed `1..m`. Edge `i` connects `u[i]` and `v[i]` and has weight `w[i]`.

From vertex `x`, you can:

- Mark an edge between `x` and `y`, photograph it, and move to `y`. This costs exactly the edge's weight.
- Transfer to another vertex `z != x` by train. You may choose any path `x -> z` (not necessarily simple). The cost is the weight of the edge with the maximum **index** on that path.

You start at vertex `1`. You must mark every edge at least once and return to vertex `1`. Find the minimum total cost.

### Input

The first line contains `T` (`1 <= T <= 1e4`).

Each test case starts with `n` and `m` (`1 <= n <= 1e6`, `0 <= m <= 1e6`). Then `m` lines follow with `u[i]`, `v[i]`, `w[i]` (`1 <= w[i] <= 1e9`). The graph is connected. Self-loops and multiple edges are allowed.

The sums of `n` and `m` over all test cases do not exceed `1e6` each.

### Output

For each test case, print the minimum cost.

### Constraints

- `1 <= T <= 1e4`
- `1 <= n <= 1e6`, `0 <= m <= 1e6`
- `1 <= w[i] <= 1e9`
- Sums of `n` and `m` `<= 1e6` each

### Examples

Input:

```
5
5 6
2 4 15
2 5 4
1 3 6
2 3 9
1 2 10
3 4 7
4 3
1 2 3
1 3 2
1 4 1
2 3
1 2 1
2 1 3
1 1 4
6 6
2 3 10
1 3 10
5 6 10
6 6 1
4 5 10
3 4 10
5 5
1 2 4
5 1 5
4 3 6
2 4 10
1 4 7
```

Output:

```
58
8
8
71
43
```

### Note

In the first test case, the route is `1 -> 3` (edge 3, cost 6), `3 -> 4` (edge 6, cost 7), `4 -> 2` (edge 1, cost 15), `2 -> 3` (edge 4, cost 9), then a transfer `3 -> 5` costing `7` (the path `3 -(edge 6)- 4 -(edge 1)- 2 -(edge 2)- 5` has maximum index `6`, weight `7`), then `5 -> 2` (edge 2, cost 4), `2 -> 1` (edge 5, cost 10). Total `58`.

---

## What this problem really is

Marking every edge costs at least the sum of all weights. The only extra cost comes from the walk not being a closed walk that uses every edge exactly once. Vertices with odd degree break an Eulerian circuit, so we must add "virtual" transfers to pair them up. The real question is: what is the cheapest way to pair up odd-degree vertices, where the cost of pairing two vertices is the cheapest train transfer between them?

## What to use

| Tool | Why |
|------|-----|
| Sum of all weights as the base | Each edge is marked once, so this is always paid. |
| Odd-degree vertices must be paired | An Eulerian circuit exists iff all degrees are even. Each pair of odd vertices can be fixed by one extra transfer. |
| Kruskal-style reconstruction tree | Adds edges in index order with DSU. The LCA of two vertices tells which edge index is the smallest possible maximum on a path between them. |
| `f[node]` = minimum weight on the node and its ancestors | A path can use a higher-index edge with a smaller weight, so the transfer cost is the minimum over the ancestor chain. |
| Greedy pairing up the tree | Pair odd vertices in the same subtree as early as possible, at the cost of the subtree's root. |

## Approach

**Root idea:** The answer is `sum of all w` plus the minimum total cost of pairing the odd-degree vertices, where a pair `(u, v)` costs `f[LCA(u, v)]` in the reconstruction tree.

Why `f`: Build the reconstruction tree by processing edges `1..m` in order with DSU. When an edge joins two different components, create a new tree node for that edge index, with the two components as children. Two vertices `u` and `v` first become connected at the node `LCA(u, v)`. A path between them must use an edge whose index is at least that node's index, and the best such choice is the minimum weight over that node and its ancestors, because ancestors have larger indices and may have smaller weights. That minimum is `f[node]`.

Why greedy pairing works: Since each ancestor's `f` is at most the value of anything below it, it is always best to pair two odd vertices as soon as they meet at a node. Walk the tree bottom-up. At each internal node `x`, gather the unmatched odd vertices coming from its children. Pair them up at cost `f[x]`. At most one unmatched odd vertex is passed up to the parent, because two can always be paired at the cost of the current node, which is no worse than pairing them higher up.

**Mental picture:** Think of the reconstruction tree as a family tree of merged cities. Each merge happens at a toll `f` (the cheapest road available from that merge point upward). Each odd vertex is a person who needs a partner. Within each family, partners meet at the first merge where they are both present, paying that merge's toll. The only person left over moves up to the next family.

**How that becomes the method:**
1. Sum all `w[i]` into the answer.
2. Compute the degree of each vertex (self-loops count twice and still give even parity).
3. Build the reconstruction tree with DSU over edges `1..m`. Every edge `i` creates a node `i`. If its endpoints are in different components, node `i` gets those two component roots as children. If they are already connected, node `i` simply sits above the current root of that component with one child. Store each node's weight `w[i]` and compute `f[node] = min(w[node], f[parent])` from the root down.
4. Collect the odd-degree vertices. Traverse the reconstruction tree bottom-up. At each internal node, pair unmatched odd leaves from its children and add `f[node]` per pair. Pass at most one leftover up.
5. Output the total.

**Data structures:**
- DSU with path compression for the reconstruction tree.
- Tree arrays: children, edge weight per internal node, `f` per node.
- A degree array.

## Walkthrough (on a sample)

Test 1: `n = 5`, edges (index: endpoints, weight):

1: `2-4`, 15. 2: `2-5`, 4. 3: `1-3`, 6. 4: `2-3`, 9. 5: `1-2`, 10. 6: `3-4`, 7.

Degrees: vertex 1 has edges 3, 5 (degree 2). Vertex 2 has edges 1, 2, 4, 5 (degree 4). Vertex 3 has edges 3, 4, 6 (degree 3, odd). Vertex 4 has edges 1, 6 (degree 2). Vertex 5 has edge 2 (degree 1, odd).

Odd vertices: `3` and `5`. Base sum: `15 + 4 + 6 + 9 + 10 + 7 = 51`.

Reconstruction tree (every edge makes a node, in index order):

- Edge 1 joins `{2}` and `{4}`: node 1 with children `2`, `4`.
- Edge 2 joins `{2, 4}` and `{5}`: node 2 with children node 1, `5`.
- Edge 3 joins `{1}` and `{3}`: node 3 with children `1`, `3`.
- Edge 4 joins `{2, 4, 5}` and `{1, 3}`: node 4 with children node 2, node 3. Vertices 3 and 5 first meet here.
- Edge 5 (`1-2`) joins vertices already connected: node 5 sits above node 4 with that single child.
- Edge 6 (`3-4`) is also already connected: node 6 sits above node 5.

`LCA(3, 5) = node 4`. The ancestor chain is node 4 (weight `9`), node 5 (weight `10`), node 6 (weight `7`). So `f[4] = min(9, 10, 7) = 7`. That is the cheapest transfer, matching the note's path through edge 6.

Pairing: vertices `3` and `5` meet at node 4 with cost `7`. Total `51 + 7 = 58`. Matches.

Test 2: `n = 4`, edges 1: `1-2` (3), 2: `1-3` (2), 3: `1-4` (1). Degrees: vertex 1 has 3 (odd), vertices 2, 3, 4 each have 1 (odd). Base `6`. Reconstruction tree: node 1 (edge 1, joins 1 and 2), node 2 (edge 2, joins with 3), node 3 (edge 3, joins with 4). `f[node 2] = min(2, f[node 3] = 1) = 1` and `f[node 3] = 1`. Pair `(2, 3)` at node 2 costs `1`; pair `(1, 4)` at node 3 costs `1`. Total `6 + 2 = 8`. Matches.

## Pitfalls

- **Using the maximum weight instead of the maximum index:** The train cost is the weight of the edge with the largest index on the path. Build the reconstruction tree by index, not by weight.
- **Forgetting the ancestor minimum:** The transfer cost at a node is `min(w[node], f[parent])`, not just the node's own weight. A higher-index edge may be cheaper.
- **Self-loops:** A self-loop adds `2` to a vertex's degree (parity unchanged), and it still must be marked, so its weight is in the base sum.
- **Multiple edges:** Each copy is a separate edge with its own index and weight in the base sum.
- **Wrong parity check:** Only odd-degree vertices need pairing. Zero odd vertices means the answer is just the sum.
- **Stack depth:** The reconstruction tree can be a path of length `1e6`. Use an iterative traversal.
- **Overflow:** The base sum can reach `1e6 * 1e9`. Use 64-bit integers.

## Complexity

- Time: `O((n + m) * alpha(n))` for the DSU, plus linear work on the tree.
- Space: `O(n + m)`.

## Key takeaway

When every edge must be covered at minimum cost and the only extra cost is pairing odd vertices, build a Kruskal reconstruction tree by edge index: each pair's cost becomes a minimum over an ancestor chain, and a bottom-up greedy pairing achieves the minimum.
