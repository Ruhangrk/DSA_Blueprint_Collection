# [1697 — Chess Tournament](https://cses.fi/problemset/task/1697)

**Platform:** CSES  
**Source:** CSES Problem Set — Construction Problems  
**Tags:** graphic degree sequence, Havel–Hakimi, greedy  
**Difficulty / Rating:** Medium–Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There will be a chess tournament of `n` players. Each player has announced the number of games they want to play.

Each pair of players can play at most one game. Your task is to determine which games will be played so that everybody will be happy.

### Input

The first input line has an integer `n`: the number of players. The players are numbered `1,2,...,n`.

The next line has `n` integers `x1, x2, ..., xn`: for each player, the number of games they want to play.

### Output

First print an integer `k`: the number of games. Then, print `k` lines describing the games. You can print any valid solution.

If there are no solutions, print `IMPOSSIBLE`.

### Constraints

- `1 <= n <= 1e5`
- `sum xi <= 2e5`

### Examples

**Input**
```
5
1 3 2 0 2
```

**Output**
```
4
1 2
2 3
2 5
3 5
```

---

## What this problem really is

Realize an undirected simple graph with a given degree sequence `x[1..n]` (no loops/multiedges), output the edge list — or `IMPOSSIBLE`.

## What to use

| Tool | Why |
|------|-----|
| Havel–Hakimi / greedy | Repeatedly connect highest remaining degree to the next stubs |
| Priority queue of degrees | `n` large but `sum x` small → sparse |
| Parity check | `sum x` must be even |

## Approach

**Root idea:** a sequence is graphic iff Havel–Hakimi succeeds. Always take a vertex with remaining degree `d`, connect it to the `d` other vertices with currently largest remaining degrees, reduce those, repeat.

**Mental picture:** players hold out that many hands; always let the neediest shake hands with the other currently neediest players.

**How that becomes the method**

1. If `sum(x)` odd → `IMPOSSIBLE`. Also `x[i] > n-1` → impossible.  
2. Put pairs `(x[i], i)` in a max-heap (or sort each round).  
3. While heap nonempty: pop `(d, u)`. If `d=0` continue/skip. Pop `d` next largest `(d_j, v_j)`, add edges `u-v_j`, push back `(d_j-1, v_j)` if positive. If you cannot pop `d` vertices → `IMPOSSIBLE`.  
4. Print edge count and edges.

Because `sum x <= 2e5`, total handshakes are manageable.

**Data structures:** priority queue; vector of edges; careful with lazy outdated heap entries.

## Walkthrough (on a sample)

Degrees `1,3,2,0,2` realize a 4-edge graph as printed.

## Pitfalls

- Allowing multiple edges to the same pair.  
- Not checking `sum` even.  
- `O(n^2)` Havel without heap when `n=1e5` but sparse sum — use heap keyed by degree.

## Complexity

- Time: about `O(sum x * log n)`  
- Space: `O(n + sum x)`

## Key takeaway

**Degree sequence → graph = Havel–Hakimi with a heap** (and even-sum / bounds checks).
