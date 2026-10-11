# [2107D — Apple Tree Traversing](https://codeforces.com/problemset/problem/2107/D)

**Platform:** Codeforces
**Source:** Codeforces Round 1023 (Div. 2)
**Tags:** brute force, dfs and similar, greedy, implementation, trees
**Difficulty / Rating:** 2100
**Time limit:** 5 seconds
**Memory limit:** 512 megabytes
**Editorial:** [Codeforces Round 1023 (Div. 2) Editorial (Problem D section)](https://codeforces.com/blog/entry/142642)

---

## Problem Statement

There is an apple tree with `n` nodes, initially with one apple at each node. You have a paper with nothing written on it.

You traverse the apple tree by repeating the following action as long as at least one apple is left:

- Choose an apple path `(u, v)`. A path `(u, v)` is an apple path if and only if every node on the path has an apple.
- Let `d` be the number of apples on the path. Write down three numbers `(d, u, v)`, in this order.
- Remove all the apples on the path `(u, v)`.

Here the path `(u, v)` is the sequence of vertices on the unique shortest walk from `u` to `v`.

Let the number sequence on the paper be `a`. Find the lexicographically largest possible sequence `a`.

### Input

Each test contains multiple test cases. The first line contains the number of test cases `t` (`1 <= t <= 1e4`).

The first line of each test case contains `n` (`1 <= n <= 1.5e5`).

The next `n - 1` lines each contain two integers `u` and `v` (`1 <= u, v <= n`), the edges of the tree.

The sum of `n` over all test cases does not exceed `1.5e5`.

### Output

For each test case, output the lexicographically largest sequence `a[1], a[2], ..., a[|a|]`. It can be shown that `|a| <= 3 * n`.

### Constraints

- `1 <= t <= 1e4`
- `1 <= n <= 1.5e5`
- Sum of `n` `<= 1.5e5`

### Examples

Input:

```
6
4
1 2
1 3
1 4
4
2 1
2 4
2 3
5
1 2
2 3
3 4
4 5
1
8
6 3
3 5
5 4
4 2
5 1
1 8
3 7
6
3 2
2 6
2 5
5 4
4 1
```

Output:

```
3 4 3 1 2 2 
3 4 3 1 1 1 
5 5 1 
1 1 1 
5 8 7 2 4 2 1 6 6 
5 6 1 1 3 3 
```

### Note

In the first test case, choose the apple path `(4, 3)`. It consists of nodes `4, 1, 3`, all with apples, so `d = 3`. Append `3, 4, 3`, then remove those apples. Only node `2` remains, so choose `(2, 2)`, append `1, 2, 2`. The final sequence is `[3, 4, 3, 1, 2, 2]`.

---

## What this problem really is

We repeatedly remove a path made of still-present nodes, and each removal is recorded as `(length in nodes, endpoint, endpoint)`. The goal is to make the recorded sequence as large as possible in lexicographic order. Lexicographic order favours a long first path, so the first move should be as long as possible, then the endpoints as large as possible. The question becomes: which paths should we remove, in what order, so every step is as large as possible?

## What to use

| Tool | Why |
|------|-----|
| Diameter of each remaining component | The longest apple path in a component is a diameter of that component. Lexicographic order forces us to remove a longest path first. |
| Two-pass BFS/DFS for diameter | Finds a diameter of a tree in linear time, and the endpoints with tie-breaking. |
| Removing a diameter and recursing on pieces | Removing the path splits the component into smaller components, each handled the same way. |
| Sorting recorded triples at the end | Because pieces are independent, sorting all recorded triples in descending order gives the lexicographically largest output. |

## Approach

**Root idea:** Treat every remaining connected group of apple nodes as its own tree. In each group, the best next move is its diameter, with ties broken so that the endpoints are as large as possible. Remove that diameter, which splits the group into smaller groups. Repeat on every group. Finally, sort all recorded triples in descending order and print them.

**Why any two diameters share a node:** If two diameters were disjoint, the path between their closest points would combine with parts of both diameters into a path longer than the diameter. That contradicts maximality. So all diameters of a tree intersect. Consequence: after removing one diameter, every remaining piece has a strictly smaller diameter. Since each piece is strictly smaller in diameter than its parent, the depth of splitting is limited.

**Why the total work is about `n * sqrt(n)`:** A chain of nested pieces has strictly increasing diameters, and the nodes removed along the chain add up to at most `n`. Strictly increasing sizes `1, 2, ..., k` sum to about `k^2 / 2`, so the chain length is `O(sqrt(n))`. Each level touches `O(n)` nodes in total, so the whole process is `O(n * sqrt(n))`.

**Mental picture:** Picture a tree as a set of cities linked by roads. Each step, find the longest road that runs through the whole network and tear it up. This cuts the map into smaller towns. Every town has a shorter longest road than the one that was just torn up, so the towns get smaller and the process ends quickly.

**How that becomes the method:**
1. Initially the whole tree is one component.
2. For each component, find its diameter with two traversals: from any node find the farthest node `x` (tie-break by larger label). From `x`, find the farthest node `y` (tie-break similarly). The diameter is the path `x` to `y`, with `d` equal to the number of nodes on it, and endpoints written as `(d, max(x, y), min(x, y))`.
3. Record that triple. Remove all nodes on the path. The remaining nodes form new components.
4. Repeat until no node remains. Sort all recorded triples in descending order. Print them flattened.

**Data structures:**
- Adjacency lists for the tree.
- `used[v]`: whether `v` is removed.
- `parent[v]` from the traversal, used to walk the diameter path back from `y` to `x`.
- A list of recorded triples.

## Walkthrough (on a sample)

Test 1: star with centre `1` and leaves `2, 3, 4`.

- Component is all four nodes. Every diameter is a 3-node path through `1` between two leaves. From `1`, every leaf is equally far, and the largest label gives `x = 4`. From `4`, the farthest leaves are `2` and `3`; the larger label gives `y = 3`. Triple `(3, max(4,3), min(4,3)) = (3, 4, 3)`. Path is `4, 1, 3`. Remove these.
- Remaining node `2` is its own component with diameter 1, triple `(1, 2, 2)`.
- Sorted descending: `(3,4,3)`, `(1,2,2)`. Output `3 4 3 1 2 2`, matching the sample.

Test 3: path `1-2-3-4-5`. The only diameter is the whole path, with endpoints `5` and `1`. Triple `(5, 5, 1)`. Nothing remains. Output `5 5 1`.

## Pitfalls

- **Counting edges instead of nodes:** `d` is the number of apples (nodes) on the path. A single node has `d = 1`.
- **Wrong tie-breaking:** The triple uses the larger endpoint first. Ties on length are broken by the larger endpoint, then the smaller.
- **Forgetting to sort at the end:** Pieces are processed in an arbitrary order, so the final output must be sorted descending by triple.
- **Using recursion on deep trees:** A path of length `1.5e5` can overflow the stack. Use an iterative BFS or an explicit stack.
- **Treating the leftover as one component:** After removing a path, each remaining connected piece is a separate component and needs its own diameter.
- **Single node tree:** `n = 1` outputs `1 1 1`.

## Complexity

- Time: `O(n * sqrt(n))`. Each round of peeling diameters is `O(n)`, and there are `O(sqrt(n))` rounds because diameters strictly decrease.
- Space: `O(n)`.

## Key takeaway

For "repeatedly remove the best path" tasks where the best path is a diameter, all diameters intersect, so removing one strictly shrinks the rest; that bounds how many rounds happen and makes a simple peel-and-sort approach fast enough.
