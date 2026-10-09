# [1696 — School Dance](https://cses.fi/problemset/task/1696)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, bipartite matching, max flow  
**Difficulty / Rating:** Medium–Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` boys and `m` girls in a school. Next week a school dance will be organized. A dance pair consists of a boy and a girl, and there are `k` potential pairs.

Your task is to find out the maximum number of dance pairs and show how this number can be achieved.

### Input

The first input line has three integers `n`, `m` and `k`: the number of boys, girls, and potential pairs. The boys are numbered `1, 2, ..., n`, and the girls are numbered `1, 2, ..., m`.

After this, there are `k` lines describing the potential pairs. Each line has two integers `a` and `b`: boy `a` and girl `b` are willing to dance together.

### Output

First print one integer `r`: the maximum number of dance pairs. After this, print `r` lines describing the pairs. You can print any valid solution.

### Constraints

- `1 <= n, m <= 500`
- `1 <= k <= 1000`
- `1 <= a <= n`
- `1 <= b <= m`

### Examples

**Input**
```
3 2 4
1 1
1 2
2 1
3 1
```

**Output**
```
2
1 2
3 1
```

---

## What this problem really is

Bipartite graph boys–girls with allowed edges. **Maximum matching** (each person in at most one pair); also output the matched edges.

## What to use

| Tool | Why |
|------|-----|
| Bipartite matching (DFS/BFS Hopcroft or flow) | Classic max pairs |
| Flow model | Source → boys → girls → sink, capacities `1` |
| Pair recovery | Read saturated boy→girl edges after max flow / matching |

## Approach

**Root idea:** one boy dances with at most one girl and vice versa → matching. Max number of pairs = size of maximum matching.

**Mental picture:** connect willing partners; pick the largest set of edges with no shared endpoint.

**Flow construction**

- Nodes: `S`, boys `1..n`, girls `1..m`, `T`.
- Edges: `S→boy` cap `1`, `boy→girl` cap `1` for each potential pair, `girl→T` cap `1`.
- Max flow = `r`.

**Steps**

1. Build matching/flow network.
2. Compute maximum matching / max flow.
3. Print `r` and each used boy–girl edge (flow `1` on boy→girl, or `match[girl]=boy`).

## Walkthrough (on a sample)

Max matching size **`2`**, e.g. boy `1`–girl `2` and boy `3`–girl `1`.

## Pitfalls

- Allowing one person in two pairs.
- Only counting without listing pairs.
- Duplicate potential pairs — treat as one edge.

## Complexity

- Time: fine for `n,m<=500`, `k<=1000` with DFS matching or Dinic  
- Space: `O(n + m + k)`

## Key takeaway

**Max dance pairs = bipartite maximum matching** (or unit-cap max flow through boys and girls).
