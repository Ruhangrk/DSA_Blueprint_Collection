# [2079 — Finding a Centroid](https://cses.fi/problemset/task/2079)

**Platform:** CSES  
**Source:** CSES Problem Set — Tree Algorithms  
**Tags:** centroid, subtree sizes  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given a tree of `n` nodes, your task is to find a centroid, i.e., a node such that when it is appointed the root of the tree, each subtree has at most `floor(n/2)` nodes.

### Input

The first input line contains an integer `n`: the number of nodes. The nodes are numbered `1, 2, ..., n`.

Then there are `n-1` lines describing the edges. Each line contains two integers `a` and `b`: there is an edge between nodes `a` and `b`.

### Output

Print one integer: a centroid node. If there are several possibilities, you can choose any of them.

### Constraints

- `1 <= n <= 2e5`
- `1 <= a, b <= n`

### Examples

**Input**
```
5
1 2
2 3
3 4
3 5
```

**Output**
```
3
```

---

## What this problem really is

Find a node where every connected component after removal (equivalently: every child subtree when rooted there, and nothing “above” larger than `n/2`) has size `<= n/2`. Trees have one or two centroids.

## What to use

| Tool | Why |
|------|-----|
| Subtree sizes from an arbitrary root | Walk toward the heavy side until balanced |
| Centroid check | All neighbor-components `<= n/2` |

## Approach

**Root idea:** compute sizes rooted at `1`. Start at `1`; while the current node has a child subtree with size `> n/2`, move to that child. When no such child exists, the “rest of the tree” above also has size `n - sz[u] <= n/2` automatically — `u` is a centroid.

**Mental picture:** keep sliding to the overweight branch until the tree balances around you.

**Steps**

1. DFS `sz[]` from root `1`.
2. `u = 1`; loop: find child `v` with `sz[v] > n/2`; if none break; else `u = v` (still using sizes from the original root — when moving down, `sz[v]` is correct for the downward component; the upward part is `n - sz[u]`).
3. Print `u`.

**Verify view:** for candidate `u`, every child of `u` (in a rerooted sense) must have size `<= n/2`; the walk finds such a `u`.

## Walkthrough (on a sample)

`n=5`. Node `3` splits into parts of sizes at most `2` → **3** is a centroid.

## Pitfalls

- Stopping at the first node without walking down the heavy child.
- Using `>= n/2` instead of `> n/2` (centroids allow `floor(n/2)`).
- Confusing with centroid **decomposition** (multi-layer) — here only one node.

## Complexity

- Time: `O(n)`  
- Space: `O(n)`

## Key takeaway

**Tree centroid = walk from any root toward the unique child subtree larger than `n/2` until none remains.**
