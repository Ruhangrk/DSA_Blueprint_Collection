# [1133 — Tree Distances II](https://cses.fi/problemset/task/1133)

**Platform:** CSES  
**Source:** CSES Problem Set — Tree Algorithms  
**Tags:** trees, rerooting DP, subtree sizes  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a tree consisting of `n` nodes.

Your task is to determine for each node the sum of the distances from the node to all other nodes.

### Input

The first input line contains an integer `n`: the number of nodes. The nodes are numbered `1, 2, ..., n`.

Then there are `n-1` lines describing the edges. Each line contains two integers `a` and `b`: there is an edge between nodes `a` and `b`.

### Output

Print `n` integers: for each node `1, 2, ..., n`, the sum of the distances.

### Constraints

- `1 <= n <= 2e5`
- `1 <= a, b <= n`

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
6 9 5 8 8
```

---

## What this problem really is

For each root `u`, compute `sum over v of dist(u,v)`. Naive multi-source BFS is too slow — use rerooting.

## What to use

| Tool | Why |
|------|-----|
| Subtree sizes `sz[u]` | How many nodes move when rerooting across an edge |
| First DFS | `ans[1]` = sum of distances from root `1`; fill `sz` |
| Second DFS (reroot) | Transition `ans[child]` from `ans[parent]` in `O(1)` |
| `long long` | Sums up to `O(n^2)` |

## Approach

**Root idea:** after fixing root `1`, when you move the “viewpoint” from `u` to child `v`, every node in `v`’s subtree gets `1` closer, and every node outside gets `1` farther:

```
ans[v] = ans[u] + (n - sz[v]) - sz[v]
       = ans[u] + n - 2*sz[v]
```

**Mental picture:** slide your standing point along an edge; the subtree on the far side shrinks by one hop for each member, everyone else grows by one hop.

**Steps**

1. Root the tree at `1`. DFS1: compute `sz[u]`, and `down[u] = sum of distances from `u` to nodes in its subtree` (or accumulate into `ans[1]` only).
   - Classic: `sz[u]=1`; for child `v`: DFS; `sz[u]+=sz[v]`; `ans[1] += ans_contrib` where distances: after DFS, `ans[1]` can be built as sum of `sz[v]` over all edges toward root… Easiest: in DFS1 compute `in[u]` = sum distances in subtree; `in[u] = sum (in[v] + sz[v])`.
2. Set `ans[1] = in[1]` (distances only inside — wait, from root that’s all nodes). Yes `in[1]` is the full answer for root `1`.
3. DFS2: for each child `v` of `u`, `ans[v] = ans[u] + n - 2*sz[v]`, then recurse.
4. Print `ans[1..n]`.

## Walkthrough (on a sample)

Root `1`: distances to others sum to **6**. Move to `2`: subtree size of `2` is `1` → `ans[2] = 6 + 5 - 2 = 9`. Move to `3`: `sz[3]=3` → `ans[3] = 6 + 5 - 6 = 5`. Matches sample.

## Pitfalls

- Using `int` for answers.
- Wrong reroot formula (`n - sz` vs `sz` signs).
- Forgetting `sz` includes the child itself.

## Complexity

- Time: `O(n)`  
- Space: `O(n)`

## Key takeaway

**Sum of distances to all nodes = reroot DP** — compute for one root, then `ans[v] = ans[u] + n - 2*sz[v]` across each edge.
