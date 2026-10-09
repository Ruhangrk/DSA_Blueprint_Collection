# [1679 — Course Schedule](https://cses.fi/problemset/task/1679)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, topological sort, Kahn / DFS  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You have to complete `n` courses. There are `m` requirements of the form "course `a` has to be completed before course `b`". Your task is to find an order in which you can complete the courses.

### Input

The first input line has two integers `n` and `m`: the number of courses and requirements. The courses are numbered `1, 2, ..., n`.

After this, there are `m` lines describing the requirements. Each line has two integers `a` and `b`: course `a` has to be completed before course `b`.

### Output

Print an order in which you can complete the courses. You can print any valid order that includes all the courses.

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
3 1
4 5
```

**Output**
```
3 4 1 5 2
```

---

## What this problem really is

Directed edges `a → b` mean “`a` before `b`”. Find a **topological order** of all `n` nodes, or report a cycle (`IMPOSSIBLE`).

## What to use

| Tool | Why |
|------|-----|
| Digraph + `indegree[]` | Kahn’s algorithm |
| Queue of zero-indegree nodes | Always take a course with no unmet prereqs |
| Or DFS topo with cycle detect | Same result |

## Approach

**Root idea:** repeatedly take any course whose prerequisites are all done. If you finish all `n`, you have an order; if you get stuck with courses left, there is a cycle.

**Mental picture:** checklist of courses. Unlock queues fill as prereqs complete.

**Data structures**

- `g[a].push_back(b)`, `indeg[b]++`
- Queue; vector `order`

**Steps (Kahn)**

1. Build graph and indegrees from requirements `a → b`.
2. Push all `i` with `indeg[i]==0`.
3. While queue not empty: pop `u`, append to `order`; for each `v` in `g[u]`, `--indeg[v]`, if zero push `v`.
4. If `order.size() < n` → `IMPOSSIBLE`, else print `order`.

## Walkthrough (on a sample)

Edges: `1→2`, `3→1`, `4→5`. One valid order: **`3 4 1 5 2`** (`3` before `1` before `2`, and `4` before `5`).

## Pitfalls

- Edge direction: “`a` before `b`” → edge `a → b` (not the reverse) for Kahn depleting indegrees of successors.
- Need **all** courses in the order, including isolates.
- Self-loops / cycles → impossible.

## Complexity

- Time: `O(n + m)`  
- Space: `O(n + m)`

## Key takeaway

**Prerequisites = topological sort.** Kahn (indegrees) or DFS; leftover nodes mean a cycle → `IMPOSSIBLE`.
