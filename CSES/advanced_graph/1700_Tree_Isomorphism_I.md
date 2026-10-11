# [1700 — Tree Isomorphism I](https://cses.fi/problemset/task/1700)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Graph Problems  
**Tags:** trees, hashing, rooted isomorphism  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given two rooted trees, your task is to find out if they are isomorphic, i.e., it is possible to draw them so that they look the same.

### Input

The first input line has an integer `t`: the number of tests. Then, there are `t` tests described as follows:

The first line has an integer `n`: the number of nodes in both trees. The nodes are numbered `1,2,...,n`, and node `1` is the root.

Then, there are `n-1` lines describing the edges of the first tree, and finally `n-1` lines describing the edges of the second tree.

### Output

For each test, print `"YES"`, if the trees are isomorphic, and `"NO"` otherwise.

### Constraints

- `1 <= t <= 1000`
- `2 <= n <= 1e5`
- the sum of all values of `n` is at most `1e5`

### Examples

**Input**
```
2
3
1 2
2 3
1 2
1 3
3
1 2
2 3
1 3
3 2
```

**Output**
```
NO
YES
```

---

## What this problem really is

Decide if two **rooted** trees (root fixed at `1`) are the same up to renaming children — structure identical when rooted.

## What to use

| Tool | Why |
|------|-----|
| AHU / tree hash | Canonical ID of a rooted tree from sorted child IDs |
| Map `vector -> int` | Assign small integers to distinct child-multiset signatures |
| DFS from root `1` | Roots are given |

## Approach

**Root idea:** two rooted trees are isomorphic iff their roots get the same canonical label, where a node’s label is determined by the multiset of its children’s labels.

**Mental picture:** at every node, sort the “shapes” of the subtrees hanging off it and glue them into one fingerprint. Same fingerprints at both roots ⇒ same tree.

**How that becomes the method**

1. Build undirected adj lists for each tree (parent handled in DFS).
2. Keep a global `map<vector<int>,int>` (or hash) from sorted child-id lists to a fresh integer id.
3. `dfs(u, p)`: recursively get ids of children ≠ `p`, sort them, look up / assign an id for that vector, return it.
4. Compare `dfs(1,-1)` on tree A vs tree B.

Clear the map between tests (or use a fresh one). Sum of `n` is `1e5`, so total map work is fine.

**Data structures:** `adj[n+1]`; `map<vector<int>,int>` or unordered hash of vectors; recursion / iterative postorder.

## Walkthrough (on a sample)

Test 1: tree A is a path `1-2-3` (root `1`); tree B is a star `1-2`, `1-3`. Different child multisets at the root → `NO`.

Test 2: both are the same path shape under root `1` (second tree’s edges `1-3`, `3-2` still a path) → `YES`.

## Pitfalls

- Forgetting to sort child ids (order of children must not matter).
- Treating the trees as unrooted (that is problem II).
- Stack depth on a line tree of size `1e5` — use careful recursion limits or iterative DP on a postorder.

## Complexity

- Time: `O(total_n log total_n)` from map / sorting children
- Space: `O(total_n)`

## Key takeaway

Rooted tree isomorphism = same AHU label at the given roots (sorted child-hash composition).
