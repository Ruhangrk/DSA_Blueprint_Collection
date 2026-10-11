# [1704 — Network Renovation](https://cses.fi/problemset/task/1704)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Graph Problems  
**Tags:** trees, leaf pairing, 2-edge-connectivity  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Syrjälä's network consists of `n` computers and `n-1` connections between them. It is possible to send data between any two computers.

However, if any connection breaks down, it will no longer be possible to send data between some computers. Your task is to add the minimum number of new connections in such a way that you can still send data between any two computers even if any single connection breaks down.

### Input

The first input line has an integer `n`: the number of computers. The computers are numbered `1,2,...,n`.

After this, there are `n-1` lines describing the connections. Each line has two integers `a` and `b`: there is a connection between computers `a` and `b`.

### Output

First print an integer `k`: the minimum number of new connections. After this, print `k` lines describing the connections. You can print any valid solution.

### Constraints

- `3 <= n <= 1e5`
- `1 <= a,b <= n`

### Examples

**Input**
```
5
1 2
1 3
3 4
3 5
```

**Output**
```
2
2 4
4 5
```

---

## What this problem really is

Add the fewest edges to a tree so the result is **2-edge-connected** (no bridges) — still connected after deleting any one original or new edge’s… actually: remain connected after deleting any *single* edge. That means the final graph must be 2-edge-connected.

## What to use

| Tool | Why |
|------|-----|
| Leaf count | Each leaf is a dead-end that needs another way out |
| Pair leaves along an Euler / DFS order | Standard construction: `ceil(#leaves / 2)` edges |
| DFS list of leaves | Order leaves as they appear in a walk |

## Approach

**Root idea:** every bridge must be put on a cycle. In a tree every edge is a bridge; the cheapest way to cycle-cover them is to connect leaves in pairs so each tree edge lies on a leaf-to-leaf path that is closed by a new edge.

**Mental picture:** hang ornaments (leaves) around the tree. Tie them with strings in a circle: leaf1–leaf2, leaf2–leaf3, … wrapping around. About half as many strings as ornaments.

**How that becomes the method**

1. Build the tree; collect all leaves (`deg == 1`). Let `L` be the list of leaves in the order found by a DFS (or any order that works with the classic pairing — DFS order is safe).
2. `k = (sz + 1) / 2` where `sz = |L|`.
3. Connect `L[i]` with `L[i + sz/2]` for `i = 0..k-1` (indices mod `sz` as needed for the standard pairing).  
   Common pattern: for `i = 0 .. k-1`, add edge `(L[i], L[min(i + k, sz-1)])` or pair `L[i]` with `L[i + ceil(sz/2)] mod sz` carefully so all are covered.
4. Print `k` and the edges.

**Why `ceil(leaves/2)` is optimal:** each new edge can “cover” at most two leaves’ unique pending bridges in the leaf-pairing lower bound.

**Data structures:** adj; degree array or leaf vector from DFS.

## Walkthrough (on a sample)

Tree: `2` and `4`,`5` are leaves (and maybe structure under `1-3`). Leaves e.g. `[2,4,5]`, `k = 2`. Pairing `2-4` and `4-5` (as in sample) makes every tree edge sit on a cycle.

## Pitfalls

- Pairing arbitrary leaves without a global order can leave a bridge.
- `n=3` path: 2 leaves → 1 edge between the two leaves → triangle. Good.
- Forgetting `k = ceil(leaves/2)`.

## Complexity

- Time: `O(n)`
- Space: `O(n)`

## Key takeaway

Make a tree 2-edge-connected with min edges → pair up leaves in DFS order using `ceil(#leaves / 2)` new edges.
