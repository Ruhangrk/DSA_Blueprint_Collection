# [2127E — Ancient Tree](https://codeforces.com/problemset/problem/2127/E)

**Platform:** Codeforces
**Source:** Atto Round 1 (Codeforces Round 1041, Div. 1 + Div. 2)
**Tags:** constructive algorithms, data structures, dfs and similar, dsu, greedy, trees
**Difficulty / Rating:** 2100
**Time limit:** 4 seconds
**Memory limit:** 256 megabytes
**Editorial:** [Atto Round 1 (Codeforces Round 1041) Editorial (Problem E section)](https://codeforces.com/blog/entry/145357)

---

## Problem Statement

Bahamin came from the past to visit Ali. He brought an ancient tree as a gift. Some of its vertices have lost their color, and he needs the coloring with minimum cost.

You are given a rooted tree of `n` vertices, where vertex `1` is the root. Each vertex has an integer weight `w[i]` and a color `c[i]`, where colors are integers between `1` and `k`. Some vertices have lost their colors, represented by `c[i] = 0`.

Vertex `v` is called cutie if there exist two vertices `x` and `y` such that:

- `lca(x, y) = v`,
- `c[x] = c[y]`, and
- `c[x] != c[v]`.

The cost of the tree is the sum of weights of all cutie vertices.

Assign colors between `1` and `k` to all vertices that lost their colors, minimising the cost, and output a coloring achieving it.

### Input

Each test contains multiple test cases. The first line contains the number of test cases `t` (`1 <= t <= 1e4`).

The first line of each test case contains two integers `n` and `k` (`3 <= n <= 2e5`, `2 <= k <= n`).

The second line contains `n` integers `w[1..n]` (`1 <= w[i] <= 1e9`).

The third line contains `n` integers `c[1..n]` (`0 <= c[i] <= k`). `c[i] = 0` means vertex `i` has lost its color.

Then `n - 1` lines follow, each with two integers `u` and `v`, the endpoints of an edge. The edges form a tree.

The sum of `n` over all test cases does not exceed `2e5`.

### Output

For each test case, output on the first line the minimum possible cost. On the second line, output `n` integers `c'[1..n]`, a coloring with that cost, where `c'[i] = c[i]` whenever `c[i] != 0`, and `1 <= c'[i] <= k` whenever `c[i] = 0`.

If several colorings achieve the minimum, any of them is accepted.

### Constraints

- `1 <= t <= 1e4`
- `3 <= n <= 2e5`, `2 <= k <= n`
- `1 <= w[i] <= 1e9`
- Sum of `n` `<= 2e5`

### Examples

Input:

```
4
4 4
5 5 5 5
1 0 2 3
1 2
1 3
1 4
5 2
3 1 4 1 5
1 2 1 2 2
1 4
2 1
3 4
4 5
11 3
3 1 4 3 1 4 3 1 4 5 6
0 0 0 2 1 2 1 2 2 1 1
1 2
2 3
2 4
2 5
2 6
1 7
7 8
7 9
10 3
3 11
4 3
2 3 2 3
2 1 0 0
3 1
1 2
2 4
```

Output:

```
0
1 4 2 3
3
1 2 1 2 2
7
2 3 1 2 1 2 1 2 2 1 1
0
2 1 3 1
```

### Note

In the first test case there are four choices for the missing color of vertex 2. Colors `1` and `4` make no vertex cutie (cost `0`). Colors `2` and `3` make vertex 1 cutie (cost `5`). The minimum is `0`.

In the second test case every vertex is colored, so the cost is fixed. Vertex 1 is cutie because `c[5] = c[2] = 2`, `lca(2, 5) = 1`, and `c[1] != 2`. The cost is `w[1] = 3`.

---

## What this problem really is

A vertex `v` is "bad" when two same-colored vertices below it, in different child subtrees, have a color different from `v`'s own color. We want to choose colors for the uncolored vertices so that the total weight of bad vertices is as small as possible. The key question is: which vertices are bad no matter what we do?

## What to use

| Tool | Why |
|------|-----|
| Virtual (auxiliary) tree per color | For a single color, the only vertices that can become cutie are LCAs of consecutive same-colored vertices in DFS order. A virtual tree stores exactly these vertices plus the original ones. |
| Classification by how many virtual trees a vertex is in | A vertex in two or more color classes cannot match all of them, so it is forced to be cutie. This gives the lower bound. |
| DFS-based constructive coloring | Shows the lower bound is achievable: give each uncolored vertex a color that avoids creating new cutie vertices. |
| LCA via binary lifting | Needed to find LCAs of consecutive vertices quickly when building virtual trees. |

## Approach

**Root idea:** The minimum cost equals the total weight of vertices that belong to at least two color classes' virtual trees, where the color class of a colored vertex is fixed and uncolored vertices are assigned freely. Vertices in two or more virtual trees are always cutie. Everything else can be arranged to avoid being cutie.

**Mental picture:** For each color, draw the minimal "skeleton" connecting all vertices of that color: their pairwise LCAs plus the vertices themselves. That skeleton is the virtual tree. Wherever two skeletons of different colors overlap at a vertex, that vertex sits between two different colors below it, and whatever color it takes, one of those two colors differs from it. So it is cutie. Where skeletons do not overlap, you can pick colors to keep everything calm.

**Why the lower bound holds:** Take a vertex `u` that lies in the virtual trees of two colors `a` and `b` (`a != b`). Those trees each contain two vertices of their color whose LCA is `u` or below `u` in a way that makes `u` the branching point. Whatever `c[u]` is, at least one of `a`, `b` differs from it, so a pair with that color gives `u` as LCA and makes it cutie.

**Why the upper bound (construction) holds:** Run a DFS from the root. When entering vertex `u`:

1. If `u` is colored, its color is fixed. Use it and recurse.
2. If `u` is uncolored and belongs to exactly one virtual tree, give it that tree's color.
3. If `u` is in more than one virtual tree, it is already cutie. Give it any color appearing in its subtree, so no new cutie vertices appear.
4. If `u` is uncolored and no colored vertex lies in its subtree, copy its parent's color. Then `u` cannot be the LCA of two different-colored pairs in a way that creates a new cutie, because the only possible new cutie is the parent, and it has the same color.

The editorial argues this never introduces a new cutie vertex, and every vertex of the third kind is exactly the set of unavoidable cutie vertices.

**Data structures:**
- For each color, the sorted list of its vertices by DFS entry time.
- Consecutive pairs in that sorted list give LCAs, which are added to the virtual tree of that color.
- Per vertex: a counter of how many color classes' virtual trees contain it, and a flag for whether it was marked cutie directly.
- Binary lifting table `par[level][v]` to answer LCA queries.

**Short steps:**
1. Root the tree at `1`, compute depth and DFS entry times, and binary-lifting table.
2. For each color, sort its colored vertices by entry time and add the LCA of each consecutive pair to that color's virtual tree.
3. Vertices appearing in two or more color trees have their weight added to the answer.
4. Fill uncolored vertices using the DFS rules above.
5. Output the cost and the coloring.

Special case: if no vertex is colored at all, the answer is `0` and every vertex can be colored `1`.

## Walkthrough (on a sample)

Test 2: `n = 5`, `k = 2`, weights `[3, 1, 4, 1, 5]`, colors `[1, 2, 1, 2, 2]`. Tree edges: `1-4`, `2-1`, `3-4`, `4-5`. So vertex `1` has children `4` and `2`, and vertex `4` has children `3` and `5`. Every vertex is colored, so there is nothing to choose.

Color `1` vertices: `{1, 3}` (sorted by entry time: `1`, then `3`). Consecutive pair `(1, 3)`: `lca = 1`. Color-1 virtual tree: `{1, 3}`.

Color `2` vertices: `{2, 4, 5}`. Whichever child of `1` the DFS enters first, consecutive pairs in entry order give LCAs `1` (between `2` and `4`, or `5` and `2`) and `4` (between `4` and `5`). Color-2 virtual tree: `{1, 2, 4, 5}`.

Vertex `1` appears in both trees. It is type 3, so it is cutie. Its weight `3` is added. Vertex `4` is only in color 2's tree, so it is not forced. Answer `3`, matching the sample.

Test 1 is a case with cost `0`: the colored vertices are `1` (color 1), `3` (color 2), and `4` (color 3). Each color has one vertex, so no virtual tree has a pair and no vertex is in two trees. Cost `0`. Vertex `2` is uncolored with no colored descendants, so it copies its parent's color `1`, giving `1 1 2 3`, which is also valid.

## Pitfalls

- **Assuming a vertex in one virtual tree is safe:** A vertex in exactly one color tree can still be cutie if its own color differs from that tree's color. Only type 3 vertices are forced; type 1 and 2 can be arranged.
- **Forgetting the all-uncolored case:** If nothing is colored, any single color works and the cost is `0`.
- **Using LCA of non-consecutive vertices:** Only consecutive vertices in DFS order need to be used to build the virtual tree.
- **Counting a vertex twice for one color:** Each color's virtual tree should count a vertex at most once.
- **Ignoring the parent-copy rule:** An uncolored leaf-like vertex with no colored descendants must copy the parent's color, not a fresh color, to avoid creating new cutie vertices.
- **Overflow:** The sum of weights can reach `2e5 * 1e9`. Use 64-bit integers.

## Complexity

- Time: `O(n log n)`. Sorting per color and LCA queries via binary lifting dominate.
- Space: `O(n log n)` for the binary lifting table, `O(n)` for other arrays.

## Key takeaway

When a cost depends on "same-colored pairs whose LCA has a different color", build each color's virtual tree; vertices shared by two color skeletons are unavoidable, and a DFS that copies nearby colors achieves exactly that lower bound.
