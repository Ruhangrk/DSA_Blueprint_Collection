# [1138 — Path Queries](https://cses.fi/problemset/task/1138)

**Platform:** CSES  
**Source:** CSES Problem Set — Tree Algorithms  
**Tags:** Euler tour, Fenwick, path to root  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a rooted tree consisting of `n` nodes. The nodes are numbered `1, 2, ..., n`, and node `1` is the root. Each node has a value.

Your task is to process following types of queries:

1. change the value of node `s` to `x`
2. calculate the sum of values on the path from the root to node `s`

### Input

The first input line contains two integers `n` and `q`: the number of nodes and queries. The nodes are numbered `1, 2, ..., n`.

The next line has `n` integers `v1, v2, ..., vn`: the value of each node.

Then there are `n-1` lines describing the edges. Each line contains two integers `a` and `b`: there is an edge between nodes `a` and `b`.

Finally, there are `q` lines describing the queries. Each query is either of the form `"1 s x"` or `"2 s"`.

### Output

Print the answer to each query of type `2`.

### Constraints

- `1 <= n, q <= 2e5`
- `1 <= a, b, s <= n`
- `1 <= vi, x <= 1e9`

### Examples

**Input**
```
5 3
4 2 5 2 1
1 2
1 3
3 4
3 5
2 4
1 3 2
2 4
```

**Output**
```
11
8
```

---

## What this problem really is

Point update of a node value + query sum of values on the path **root → s**.

## What to use

| Tool | Why |
|------|-----|
| Euler tour difference trick | Path-to-root becomes a prefix sum in DFS order |
| Fenwick | Point add on ranges / prefix query |
| `tin` / `tout` | Add value on `[tin[s], tout[s])` meaning “applies to all nodes in subtree” for path sums |

## Approach

**Root idea:** store in a BIT a difference array on Euler order so that the prefix sum up to `tin[u]` equals the root-to-`u` path sum.

When node `s` has value `v`, every path from the root that still goes through `s` (i.e. every node in `s`’s subtree) should include `v`. So:

- Set value `v` at `s`: Fenwick range-add `+v` on `[tin[s], tout[s])` (or `+v` at `tin[s]`, `-v` at `tout[s]`).
- Query path root→`s`: Fenwick prefix sum at `tin[s]`.

**Mental picture:** each node’s value is a stamp applied to its whole subtree in the tour; reading the stamp total at `tin[s]` collects every ancestor’s stamp (and itself).

**Steps**

1. DFS `tin`/`tout` from root `1`.
2. For each node, apply its initial value as a range update on the subtree interval.
3. Type `1 s x`: range-add `x - old` on `[tin[s], tout[s])`; update stored value.
4. Type `2 s`: print prefix sum at `tin[s]`.

## Walkthrough (on a sample)

Path `1-3-4`: values `4+5+2=11`. After node `3` becomes `2`: `4+2+2=8`.

## Pitfalls

- Using subtree-sum structure (previous problem) — wrong query type.
- Updating only `tin[s]` without the `-` at `tout` (then path sums break).
- `long long` for BIT.

## Complexity

- Time: `O((n+q) log n)`  
- Space: `O(n)`

## Key takeaway

**Root-path sums with updates = Euler tour + range-add on subtree, prefix-query at `tin[s]`.**
