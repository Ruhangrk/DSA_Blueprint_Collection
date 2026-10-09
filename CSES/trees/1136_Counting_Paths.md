# [1136 — Counting Paths](https://cses.fi/problemset/task/1136)

**Platform:** CSES  
**Source:** CSES Problem Set — Tree Algorithms  
**Tags:** LCA, difference array on trees, DFS  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a tree consisting of `n` nodes, and `m` paths in the tree.

Your task is to calculate for each node the number of paths containing that node.

### Input

The first input line contains integers `n` and `m`: the number of nodes and paths. The nodes are numbered `1, 2, ..., n`.

Then there are `n-1` lines describing the edges. Each line contains two integers `a` and `b`: there is an edge between nodes `a` and `b`.

Finally, there are `m` lines describing the paths. Each line contains two integers `a` and `b`: there is a path between nodes `a` and `b`.

### Output

Print `n` integers: for each node `1, 2, ..., n`, the number of paths containing that node.

### Constraints

- `1 <= n, m <= 2e5`
- `1 <= a, b <= n`

### Examples

**Input**
```
5 3
1 2
1 3
3 4
3 5
1 3
2 5
1 4
```

**Output**
```
3 1 3 1 1
```

---

## What this problem really is

Many paths given as endpoints. For each vertex, count how many of those paths cover it. Cannot mark each path explicitly at `2e5`.

## What to use

| Tool | Why |
|------|-----|
| LCA | Path `a–b` = `a→lca` + `b→lca` |
| Tree difference / prefix | `+1` at `a` and `b`, `-1` at `lca`, `-1` at `parent[lca]` (for node coverage) |
| DFS subtree sum | Push counts down to get final coverage |

## Approach

**Root idea:** for a path from `a` to `b` with `L = lca(a,b)`, every node on the path should get `+1`. Using a difference array on the rooted tree:

```
diff[a] += 1
diff[b] += 1
diff[L] -= 1
diff[parent[L]] -= 1   // if L is not root; skip if parent doesn't exist
```

After processing all paths, a DFS that sets `ans[u] = diff[u] + sum(ans[child])` (or equivalently accumulates upward) yields the number of paths through `u`.

Why it works: the two `+1`s climb toward the root; the `-1` at `L` stops double-counting at the LCA; the `-1` at `parent[L]` stops the mark from continuing above the path.

**Mental picture:** paint the path by dripping +1 from both ends and wiping at the LCA so paint doesn’t leak past the path.

**Steps**

1. Root at `1`; build LCA + parents.
2. For each path, apply the four (or three) diff updates.
3. DFS: `ans[u] = diff[u]`; for each child, DFS then `ans[u] += ans[child]`.
4. Print `ans[1..n]`.

## Walkthrough (on a sample)

Paths `1–3`, `2–5`, `1–4`. Node `1` lies on all three → **3**; node `3` on all three → **3**; leaves on one each as in the sample.

## Pitfalls

- Forgetting `-1` on `parent[lca]` (overcounts ancestors).
- Using edge-difference formulas when the problem asks for **nodes**.
- `a == b`: path is a single node — still `diff[a]+=1`, `diff[lca]-=1`, etc. carefully (often `diff[a]+=1` only once and adjust).

## Complexity

- Time: `O((n+m) log n)` with binary lifting LCA  
- Space: `O(n log n)`

## Key takeaway

**Count paths through each node = LCA + tree difference** (`+1` at ends, `-1` at LCA and parent of LCA), then DFS sum.
