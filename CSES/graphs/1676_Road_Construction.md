# [1676 — Road Construction](https://cses.fi/problemset/task/1676)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, Union-Find, DSU  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` cities and initially no roads between them. However, every day a new road will be constructed, and there will be a total of `m` roads.

A component is a group of cities where there is a route between any two cities using the roads. After each day, your task is to find the number of components and the size of the largest component.

### Input

The first input line has two integers `n` and `m`: the number of cities and roads. The cities are numbered `1, 2, ..., n`.

Then, there are `m` lines describing the new roads. Each line has two integers `a` and `b`: a new road is constructed between cities `a` and `b`.

You may assume that every road will be constructed between two different cities.

### Output

Print `m` lines: the required information after each day.

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
4 2
3 3
2 3
```

---

## What this problem really is

Start with `n` isolated nodes; add undirected edges online. After each edge, report **(#components, size of largest component)**.

## What to use

| Tool | Why |
|------|-----|
| DSU with component `size[]` | Merge cities; track sizes |
| Counters `components`, `largest` | Update in `O(1)` on each unite |

## Approach

**Root idea:** an edge merges two components only if endpoints differ. Then components decrease by 1 and the new size is `size[a]+size[b]`; maybe update the global max.

**Mental picture:** glue islands together day by day; track how many islands and the biggest one.

**Data structures**

- `parent[n+1]`, `sz[n+1]` init `1`
- `comp = n`, `best = 1`

**Steps**

1. Init DSU.
2. For each road `a-b`:
   - If `find(a)==find(b)`: print `comp best` unchanged.
   - Else unite, `comp--`, `best = max(best, new_size)`, print `comp best`.

## Walkthrough (on a sample)

Start: `5` comps, best `1`.

- `1-2` → comps `4`, best `2`
- `1-3` → comps `3`, best `3`
- `4-5` → comps `2`, best `3`

## Pitfalls

- Not handling already-connected pairs (redundant roads).
- Forgetting to maintain `sz` on unite.
- `n, m` large — must be near-`O(m α(n))`, not DFS each day.

## Complexity

- Time: `O(m α(n))`  
- Space: `O(n)`

## Key takeaway

**Online components = DSU.** On merge: `--components`, update max size from summed sizes.
