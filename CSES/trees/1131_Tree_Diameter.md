# [1131 — Tree Diameter](https://cses.fi/problemset/task/1131)

**Platform:** CSES  
**Source:** CSES Problem Set — Tree Algorithms  
**Tags:** trees, BFS, diameter, two-pass  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a tree consisting of `n` nodes.

The diameter of a tree is the maximum distance between two nodes. Your task is to determine the diameter of the tree.

### Input

The first input line contains an integer `n`: the number of nodes. The nodes are numbered `1, 2, ..., n`.

Then there are `n-1` lines describing the edges. Each line contains two integers `a` and `b`: there is an edge between nodes `a` and `b`.

### Output

Print one integer: the diameter of the tree.

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
3
```

Explanation: The diameter corresponds to the path `2 -> 1 -> 3 -> 5`.

---

## What this problem really is

Longest path (by number of edges) between any two nodes in an unweighted tree.

## What to use

| Tool | Why |
|------|-----|
| Two BFS / DFS passes | Classical tree-diameter algorithm |
| Or tree DP | Longest + second-longest downward paths through each node |
| Undirected adjacency | Tree edges |

## Approach

**Root idea:** pick any node `u`, find the farthest node `A` from `u`. Then find the farthest node `B` from `A`. Distance `A–B` is the diameter. (Endpoints of a diameter are leaves in this sense; the farthest from an arbitrary start is always a diameter endpoint.)

**Mental picture:** stretch a rubber band from a random point to the rim, then stretch again from that rim point to the opposite rim — that second length is the width of the tree.

**Steps**

1. BFS/DFS from node `1` → node `A` = farthest.
2. BFS/DFS from `A` → record max distance (= diameter).
3. Print that distance.

**DP variant:** for each node, compute height of children; diameter candidate = sum of top-two child heights + (edges); take global max.

## Walkthrough (on a sample)

From `1`, farthest could be `4` or `5` or `2` (dist 2 from 1 to 4). From `2`, farthest is `4` or `5` at distance **3**.

## Pitfalls

- Using shortest-path algorithms for general graphs — overkill; tree method is enough.
- Counting nodes instead of edges on the path (diameter is edges).
- Starting the second BFS from the wrong node.

## Complexity

- Time: `O(n)`  
- Space: `O(n)`

## Key takeaway

**Tree diameter = two farthest-node searches** — farthest from arbitrary start, then farthest from that node.
