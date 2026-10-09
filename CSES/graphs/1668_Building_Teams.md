# [1668 — Building Teams](https://cses.fi/problemset/task/1668)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, bipartite check, BFS/DFS coloring  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` pupils in Uolevi's class, and `m` friendships between them. Your task is to divide the pupils into two teams in such a way that no two pupils in a team are friends. You can freely choose the sizes of the teams.

### Input

The first input line has two integers `n` and `m`: the number of pupils and friendships. The pupils are numbered `1, 2, ..., n`.

Then, there are `m` lines describing the friendships. Each line has two integers `a` and `b`: pupils `a` and `b` are friends.

Every friendship is between two different pupils. You can assume that there is at most one friendship between any two pupils.

### Output

Print an example of how to build the teams. For each pupil, print `"1"` or `"2"` depending on to which team the pupil will be assigned. You can print any valid team.

If there are no solutions, print `"IMPOSSIBLE"`.

### Constraints

- `1 <= n <= 1e5`
- `1 <= m <= 2e5`
- `1 <= a, b <= n`

### Examples

**Input**
```
5 3
1 2
1 3
4 5
```

**Output**
```
1 2 2 1 2
```

---

## What this problem really is

Undirected graph: friendships are edges. Color vertices with **2 colors** so every edge joins different colors (proper 2-coloring) = check **bipartite**. Print one coloring or `IMPOSSIBLE`.

## What to use

| Tool | Why |
|------|-----|
| Adjacency list | Large graph |
| `team[v]` in `{0,1,2}` | `0` = unassigned; `1`/`2` = teams |
| BFS or DFS per component | Graph may be disconnected — color each component |

## Approach

**Root idea:** friends must sit in opposite teams. Along any path, colors must alternate. If you ever see an edge inside the same color, the graph has an odd cycle → impossible.

**Mental picture:** paint pupils red/blue so every friendship is red–blue. Isolated groups are painted separately.

**Data structures**

- `g`, `team[n+1]` init `0`
- Queue for BFS (or recursion/stack for DFS)

**Steps**

1. Build undirected friendship graph.
2. For each pupil `s` still `team[s]==0`:
   - Set `team[s]=1`, BFS/DFS.
   - For edge `u-v`: if `v` uncolored, set `team[v] = 3 - team[u]`; if colored and `team[v]==team[u]` → fail.
3. On fail → `IMPOSSIBLE`.
4. Else print `team[1] .. team[n]`.

## Walkthrough (on a sample)

Edges: `1-2`, `1-3`, `4-5`.

- Component `{1,2,3}`: put `1` in team `1` → `2` and `3` in team `2`.
- Component `{4,5}`: put `4` in team `1` → `5` in team `2`.

One valid line: `1 2 2 1 2`.

## Pitfalls

- **Only coloring one component** — leftover pupils must also be assigned (any team is fine if isolated).
- **Odd cycle** anywhere → whole answer `IMPOSSIBLE`.
- Don’t require teams to be non-empty beyond what’s forced — sizes are free.

## Complexity

- Time: `O(n + m)`  
- Space: `O(n + m)`

## Key takeaway

**Two teams with no internal friendship = bipartite coloring.** Alternating BFS/DFS; conflict on a same-color edge means impossible.
