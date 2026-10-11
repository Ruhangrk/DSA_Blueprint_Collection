# [1134 — Prüfer Code](https://cses.fi/problemset/task/1134)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Graph Problems  
**Tags:** trees, Prüfer code, reconstruction  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A Prüfer code of a tree of `n` nodes is a sequence of `n-2` integers that uniquely specifies the structure of the tree.

The code is constructed as follows: As long as there are at least three nodes left, find a leaf with the smallest label, add the label of its only neighbor to the code, and remove the leaf from the tree.

Given a Prüfer code of a tree, your task is to construct the original tree.

### Input

The first input line contains an integer `n`: the number of nodes. The nodes are numbered `1,2,...,n`.

The second line contains `n-2` integers: the Prüfer code.

### Output

Print `n-1` lines describing the edges of the tree. Each line has to contain two integers `a` and `b`: there is an edge between nodes `a` and `b`. You can print the edges in any order.

### Constraints

- `3 <= n <= 2e5`
- `1 <= a,b <= n`

### Examples

**Input**
```
5
2 2 4
```

**Output**
```
1 2
2 3
2 4
4 5
```

---

## What this problem really is

Invert the Prüfer encoding: from a sequence of length `n-2`, rebuild the unique labeled tree on `1..n`.

## What to use

| Tool | Why |
|------|-----|
| Degree from code | Node `v` appears `deg(v)-1` times in the Prüfer code |
| Set / min-heap of leaves | Always connect the smallest current leaf to the next code value |
| Linear scan | `n` up to `2e5` needs `O(n log n)` or better |

## Approach

**Root idea:** each code entry is the neighbor of the leaf deleted at that step. Degrees tell you which nodes are still leaves; replay the deletion process forward.

**Mental picture:** start from the finished code. Nodes that never appear are the original leaves. Repeatedly attach the smallest unused leaf to the next code number, “use up” one occurrence of that neighbor, and when a neighbor’s remaining degree hits `1` it becomes a new leaf.

**How that becomes the method**

1. Read code `c[0..n-3]`. Set `deg[v] = 1` for all `v`, then for each `x` in the code do `deg[x]++`.
2. Put every `v` with `deg[v]==1` into a min-set / priority queue.
3. For `i = 0..n-3`:
   - `leaf =` smallest in the set; erase it.
   - Add edge `(leaf, c[i])`.
   - `deg[c[i]]--`; if it becomes `1`, insert `c[i]` into the set.
4. Two nodes remain with degree `1` — add the last edge between them.

**Data structures:** `deg[n+1]`; `set<int>` or min-heap of current leaves; edge list.

## Walkthrough (on a sample)

`n=5`, code `[2,2,4]`.

- Appearances: `2` twice, `4` once → degrees: `1:1, 2:3, 3:1, 4:2, 5:1`.
- Leaves set: `{1,3,5}`.
- Step `2`: take `1`, edge `1-2`, `deg[2]=2` (not a leaf yet). Set `{3,5}`.
- Step `2`: take `3`, edge `3-2`, `deg[2]=1` → insert `2`. Set `{2,5}`.
- Step `4`: take `2`, edge `2-4`, `deg[4]=1` → insert `4`. Set `{4,5}`.
- Last edge `4-5`.

Tree matches the sample.

## Pitfalls

- Off-by-one in degree init (`deg=1` base, not `0`).
- Using a slow `O(n^2)` “scan for min leaf” at `n=2e5`.
- Forgetting the final edge between the last two nodes.

## Complexity

- Time: `O(n log n)` with a set / heap
- Space: `O(n)`

## Key takeaway

Prüfer decode = degree counts + always link the current smallest leaf to the next code entry.
