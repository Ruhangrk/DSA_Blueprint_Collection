# [1705 — Forbidden Cities](https://cses.fi/problemset/task/1705)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Graph Problems  
**Tags:** block-cut tree, articulation points, LCA  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` cities and `m` roads between them. Kaaleppi is currently in city `a` and wants to travel to city `b`.

However, there is a problem: Kaaleppi has recently robbed a bank in city `c` and can't enter the city, because the local police would catch him. Your task is to find out if there is a route from city `a` to city `b` that does not visit city `c`.

As an additional challenge, you have to process `q` queries where `a`, `b` and `c` vary.

### Input

The first input line has three integers `n`, `m` and `q`: the number of cities, roads and queries. The cities are numbered `1,2,...,n`.

Then, there are `m` lines describing the roads. Each line has two integers `a` and `b`: there is a road between cities `a` and `b`. Each road is bidirectional.

Finally, there are `q` lines describing the queries. Each line has three integers `a`, `b` and `c`: is there a route from city `a` to city `b` that does not visit city `c`?

You can assume that there is a route between any two cities.

### Output

For each query, print `"YES"`, if there is such a route, and `"NO"` otherwise.

### Constraints

- `1 <= n <= 1e5`
- `1 <= m <= 2e5`
- `1 <= q <= 1e5`
- `1 <= a,b,c <= n`

### Examples

**Input**
```
5 6 3
1 2
1 3
2 3
2 4
3 4
4 5
1 4 2
3 5 4
3 5 2
```

**Output**
```
YES
NO
YES
```

---

## What this problem really is

Connected undirected graph; for many triples `(a,b,c)`, decide if `a` and `b` stay connected after deleting vertex `c` (paths that avoid `c`).

## What to use

| Tool | Why |
|------|-----|
| Block-cut tree | Captures exactly how articulation points separate biconnected components |
| BCC DFS (`tin`/`low`) | Build the tree of BCCs + articulation nodes |
| LCA on block-cut tree | `c` lies on all `a–b` paths iff it lies on the BC-tree path between `a` and `b` as an articulation |

## Approach

**Root idea:** removing `c` disconnects `a` from `b` iff `c` is an articulation point that separates them — visible on the block-cut tree as `c` lying strictly between `a` and `b`.

**Mental picture:** biconnected components are “rooms”; articulation points are “doors.” You cannot avoid a door that every walk from room-of-`a` to room-of-`b` must use.

**How that becomes the method**

1. Run BCC / articulation-point DFS; build the **block-cut tree**:
   - one node per BCC;
   - one node per articulation point;
   - edges between an articulation and every BCC that contains it.
   - Non-articulation vertices live inside exactly one BCC (attach them to that BCC node, or treat queries via their BCC).
2. Root the block-cut tree; preprocess LCA + depths.
3. For query `(a,b,c)`:
   - If `c == a` or `c == b`: typically `NO` (cannot start/end in the forbidden city) — handle per statement intent; usually you need a path whose *internal* and endpoints… the path must not visit `c`, so if `a=c` or `b=c` answer is `NO` unless `a=b≠c` edge cases. (If `a=b` and `a≠c`, `YES`.)
   - Otherwise: map `a,b,c` into block-cut tree vertices; `NO` iff `c` is an articulation and lies on the unique tree path between `a` and `b` (check with LCA: `c` is between iff `lca` relationships place `c` on the path).
4. If `c` is not an articulation point, removing it never disconnects the graph → `YES` (when `c` ≠ `a,b`).

**Data structures:** adj; `tin`,`low`, stack for BCC; block-cut adj; binary lifting LCA.

## Walkthrough (on a sample)

`4` is a cut toward `5`. Query `3 5 4`: every path to `5` goes through `4` → `NO`. Query `3 5 2`: `2` is avoidable via `3-4-5` → `YES`. Query `1 4 2`: path `1-3-4` avoids `2` → `YES`.

## Pitfalls

- Answering with bridges only (vertex cuts ≠ edge cuts).
- Forgetting `a=b` or `c` equal to an endpoint.
- Building BCCs incorrectly when handling the DFS parent edge.

## Complexity

- Time: `O((n + m + q) log n)` with LCA
- Space: `O(n + m)`

## Key takeaway

Avoiding city `c` on paths `a–b` → block-cut tree: `NO` iff articulation `c` lies on the BC-tree path between `a` and `b`.
