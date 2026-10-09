# [1139 — Distinct Colors](https://cses.fi/problemset/task/1139)

**Platform:** CSES  
**Source:** CSES Problem Set — Tree Algorithms  
**Tags:** DSU on tree, small-to-large, subtree distinct  
**Difficulty / Rating:** Medium–Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a rooted tree consisting of `n` nodes. The nodes are numbered `1, 2, ..., n`, and node `1` is the root. Each node has a color.

Your task is to determine for each node the number of distinct colors in the subtree of the node.

### Input

The first input line contains an integer `n`: the number of nodes. The nodes are numbered `1, 2, ..., n`.

The next line consists of `n` integers `c1, c2, ..., cn`: the color of each node.

Then there are `n-1` lines describing the edges. Each line contains two integers `a` and `b`: there is an edge between nodes `a` and `b`.

### Output

Print `n` integers: for each node `1, 2, ..., n`, the number of distinct colors.

### Constraints

- `1 <= n <= 2e5`
- `1 <= a, b <= n`
- `1 <= ci <= 1e9`

### Examples

**Input**
```
5
2 3 2 2 1
1 2
1 3
3 4
3 5
```

**Output**
```
3 1 2 1 1
```

---

## What this problem really is

For every subtree, count distinct color values. Naive sets per node merge up → `O(n^2)`. Need small-to-large / DSU on tree.

## What to use

| Tool | Why |
|------|-----|
| DSU on tree (small-to-large) | Merge child color sets into parent in `O(n log^2 n)` |
| `map` / hash set of colors | Track distinct counts |
| Keep heavy child’s set | Avoid clearing the largest child |

## Approach

**Root idea:** DFS. For each node, compute answers for light children (and throw away their sets), keep the heavy child’s set, then add light children’s nodes into that set, then add own color. `ans[u] = set.size()`.

**Mental picture:** each subtree builds a bag of colors; always pour smaller bags into the biggest one so each color moves `O(log n)` times across merges.

**Steps**

1. Root at `1`; compute subtree sizes; pick heavy child.
2. `dfs(u, keep)`:
   - Recurse light children with `keep=false` (build then discard).
   - Recurse heavy with `keep=true`.
   - Take heavy’s set as current; add all nodes from light subtrees; add `color[u]`.
   - `ans[u] = sz(set)`.
   - If `!keep`, clear the set / undo additions.
3. Print `ans[1..n]`.

(Alternate: Mo on tree / Euler tour + Mo — also works.)

## Walkthrough (on a sample)

Subtree `3`: colors `2,2,1` → **2** distinct. Root: colors `2,3,2,2,1` → **3**. Leaves → **1**.

## Pitfalls

- Merging without small-to-large → TLE.
- Clearing the heavy set accidentally.
- Colors up to `1e9` — use hash/map, not an array of size `1e9`.

## Complexity

- Time: `O(n log n)` with small-to-large sets (hash) / `O(n log^2 n)` with ordered maps  
- Space: `O(n)`

## Key takeaway

**Distinct colors per subtree = DSU on tree (small-to-large set merging).**
