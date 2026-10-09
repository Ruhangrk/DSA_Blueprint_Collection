# [1693 — Teleporters Path](https://cses.fi/problemset/task/1693)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, directed Eulerian path, Hierholzer  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A game has `n` levels and `m` teleporters between them. You win the game if you move from level `1` to level `n` using every teleporter exactly once.

Can you win the game, and what is a possible way to do it?

### Input

The first input line has two integers `n` and `m`: the number of levels and teleporters. The levels are numbered `1, 2, ..., n`.

Then, there are `m` lines describing the teleporters. Each line has two integers `a` and `b`: there is a teleporter from level `a` to level `b`.

You can assume that each pair `(a,b)` in the input is distinct.

### Output

Print `m+1` integers: the sequence in which you visit the levels during the game. You can print any valid solution.

If there are no solutions, print `"IMPOSSIBLE"`.

### Constraints

- `2 <= n <= 1e5`
- `1 <= m <= 2e5`
- `1 <= a, b <= n`

### Examples

**Input**
```
5 6
1 2
1 3
2 4
2 5
3 1
4 2
```

**Output**
```
1 3 1 2 4 2 5
```

---

## What this problem really is

Directed multigraph: find an **Eulerian path** from `1` to `n` (use every directed edge exactly once), or `IMPOSSIBLE`.

## What to use

| Tool | Why |
|------|-----|
| In/out degree conditions | Directed Euler path `s→t` needs specific degree imbalances |
| Hierholzer (directed) | Construct the path |
| Edge-used flags | Large sparse digraph |

## Approach

**Root idea:** traverse each teleporter once from `1` to `n` ↔ directed Eulerian path start `1` end `n`.

**Degree conditions (besides weak connectivity of edges):**

- `outdeg[1] = indeg[1] + 1`
- `indeg[n] = outdeg[n] + 1`
- For every other `v`: `indeg[v] = outdeg[v]`

**Mental picture:** at the start you “leave one extra time”; at the end you “enter one extra time”; elsewhere balanced.

**Data structures**

- Adj lists of outgoing edge ids; `used[m]`
- Hierholzer stack from `1`; build node sequence

**Steps**

1. Build digraph; compute in/out degrees; verify conditions (and that edges are in one weakly connected piece touching `1`/`n` as required).
2. Directed Hierholzer from `1`.
3. If the path doesn’t contain exactly `m` edges → `IMPOSSIBLE`.
4. Else print the `m+1` node visits.

## Walkthrough (on a sample)

Degrees allow Euler path `1→…→5` using all 6 teleporters, e.g. **`1 3 1 2 4 2 5`**.

## Pitfalls

- Wrong start/end degree pattern.
- Undirected Hierholzer logic on a digraph.
- Not checking the reconstructed path length.

## Complexity

- Time: `O(n + m)`  
- Space: `O(n + m)`

## Key takeaway

**Use every directed edge once from `1` to `n` = directed Eulerian path.** Check in/out degrees, then Hierholzer from `1`.
