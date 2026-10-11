# [1701 — Tree Isomorphism II](https://cses.fi/problemset/task/1701)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Graph Problems  
**Tags:** trees, centers, hashing, unrooted isomorphism  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given two (not rooted) trees, your task is to find out if they are isomorphic, i.e., it is possible to draw them so that they look the same.

### Input

The first input line has an integer `t`: the number of tests. Then, there are `t` tests described as follows:

The first line has an integer `n`: the number of nodes in both trees. The nodes are numbered `1,2,...,n`.

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
YES
YES
```

---

## What this problem really is

Same as rooted isomorphism, but the root is *not* fixed — any node may map to any node. Compare unrooted tree shapes.

## What to use

| Tool | Why |
|------|-----|
| Tree centers | Every tree has 1 center or 2 adjacent centers; isomorphism must map centers to centers |
| Rooted AHU hash | After rooting at a center, reuse the rooted isomorphism test |
| Double BFS | Find a diameter; centers lie in the middle of a diameter |

## Approach

**Root idea:** peel the unrooted problem down to a few rooted ones. Centers are a complete invariant set of candidate roots.

**Mental picture:** the “middle” of the tree is unique (or a unique edge). Try painting each tree from its middle(s); if some middle-rooting of A matches some middle-rooting of B, the unrooted trees match.

**How that becomes the method**

1. Find centers of a tree:
   - BFS from any node to a farthest node `u`;
   - BFS from `u` to farthest `v` (diameter ends);
   - centers = middle node(s) on the `u–v` path (1 or 2).
2. Compute rooted AHU hashes (as in Tree Isomorphism I) for tree A rooted at each of its centers, and same for B.
3. `YES` if any hash from A’s center-rootings equals any hash from B’s.

(Equivalent: rerooting AHU to get a hash for every root, then compare multisets — centers prune this to ≤2 roots each.)

**Data structures:** adj; BFS queues / parent arrays for diameter; same `map<vector,int>` hasher as problem I.

## Walkthrough (on a sample)

Test 1: path of 3 vs star of 3. Unrooted, a path-of-3 **is** a star-of-3 (center degree 2 vs center degree 2… wait path `1-2-3` center is `2` with two leaves; star `1` with leaves `2,3` is the same shape) → `YES`.

Test 2: both paths → `YES`.

## Pitfalls

- Only trying root `1` (fails unrooted cases).
- Missing the two-center case (even diameter).
- Reusing AHU map state incorrectly across different rootings without consistent id assignment — assign ids in one shared forest of signatures, or hash strings/vectors directly.

## Complexity

- Time: `O(total_n log total_n)`
- Space: `O(total_n)`

## Key takeaway

Unrooted tree isomorphism → find centers, then rooted-hash from those ≤2 candidates.
