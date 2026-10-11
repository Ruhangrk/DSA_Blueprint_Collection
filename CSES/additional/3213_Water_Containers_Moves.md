# [3213 — Water Containers Moves](https://cses.fi/problemset/task/3213)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems I  
**Tags:** Shortest path, Dijkstra, state graph, implicit graph, reconstruction  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are two containers: container A has volume `a`, container B has volume `b`. Both start empty. You want container A to hold exactly `x` units.

On each move you can:
- `FILL A` or `FILL B`: fill a container to its capacity,
- `EMPTY A` or `EMPTY B`: empty a container,
- `MOVE A B` or `MOVE B A`: pour from one container into the other until the source is empty or the target is full.

Each move must transfer at least one unit, and must fill or empty at least one container. Find a sequence that leaves A with `x` units and minimizes the total amount of water moved, or print `-1` if impossible.

### Input

One line with three integers `a`, `b`, `x`.

### Output

First print two integers `n` (number of moves) and `m` (total amount of water moved). Then print the `n` moves, one per line.

If impossible, print `-1`.

### Constraints

- `1 <= a, b, x <= 1000`

### Examples

**Input**
```
5 3 4
```

**Output**
```
6 19
FILL A
MOVE A B
EMPTY B
MOVE A B
FILL A
MOVE A B
```

**Note on the total:** the total counts every fill and empty by the amount of water added or removed, plus every move by the amount transferred. In the sample: `5 + 3 + 3 + 2 + 5 + 1 = 19`.

---

## What this problem really is

The state is `(water in A, water in B)`, with `0 <= i <= a` and `0 <= j <= b`. Each move is an edge with a cost equal to the water it handles. Find the cheapest path from `(0,0)` to any state with `A = x`, and output the path.

## What to use

| Tool | Why |
|------|-----|
| State space `(i, j)` | At most `1001 * 1001 ~ 1e6` states; small enough to enumerate |
| Dijkstra with a min-heap | Edge costs are positive and vary (fill/empty/move amounts), so BFS alone is not enough |
| Parent pointers with move type | Reconstruct the actual sequence of moves |
| Implicit transitions (6 per state) | Avoid building the graph; compute each neighbor on the fly |

## Approach

**Root idea:** every state is a node, every legal move is an edge, and the cost of an edge is the amount of water that move handles. The answer is the shortest path from the empty state to any state where A holds `x`.

**Mental picture:** imagine two buckets on a table. Each state is a snapshot of how full both buckets are. From any snapshot you can make at most six moves: fill either bucket, empty either bucket, or pour one into the other. Pouring from A to B moves `min(i, b - j)` units; the amounts change the water cost. You walk through this map of snapshots looking for the cheapest route to a snapshot where A reads `x`. Dijkstra explores snapshots in order of total water handled so far.

**How that becomes the method**

1. Define `dist[i][j]` = minimum total cost to reach state `(i, j)`. Start `dist[0][0] = 0`.
2. From state `(i, j)`, generate:
   - `FILL A`: new state `(a, j)`, cost `a - i`.
   - `FILL B`: new state `(i, b)`, cost `b - j`.
   - `EMPTY A`: new state `(0, j)`, cost `i`.
   - `EMPTY B`: new state `(i, 0)`, cost `j`.
   - `MOVE A B`: `t = min(i, b - j)`, new state `(i - t, j + t)`, cost `t`.
   - `MOVE B A`: `t = min(j, a - i)`, new state `(i + t, j - t)`, cost `t`.
   Skip a move if it changes nothing (cost 0 or state unchanged).
3. Relax with a min-heap. Record `parent` and the move name.
4. Pick the reached state with `i = x` and minimum `dist`. Walk parents back to `(0,0)` and reverse.
5. The output count `n` is the number of moves, and `m` is `dist`.

**Data structures:**
- `dist[0..a][0..b]`, `long long` or `int` (max cost is small, about `1e6 * 1000`, so use `long long` to be safe).
- `par[0..a][0..b]`: previous state index.
- `how[0..a][0..b]`: the move name that produced the state.
- Min-heap of `(dist, state)`.

## Walkthrough (on a sample)

`a = 5`, `b = 3`, `x = 4`. Trace the sample path, computing costs:

| Step | Move | Before `(A,B)` | Cost | After `(A,B)` |
|---|---|---|---|---|
| 1 | `FILL A` | `(0,0)` | `5 - 0 = 5` | `(5,0)` |
| 2 | `MOVE A B` | `(5,0)` | `min(5, 3-0) = 3` | `(2,3)` |
| 3 | `EMPTY B` | `(2,3)` | `3` | `(2,0)` |
| 4 | `MOVE A B` | `(2,0)` | `min(2, 3) = 2` | `(0,2)` |
| 5 | `FILL A` | `(0,2)` | `5 - 0 = 5` | `(5,2)` |
| 6 | `MOVE A B` | `(5,2)` | `min(5, 3-2) = 1` | `(4,3)` |

Total cost `5 + 3 + 3 + 2 + 5 + 1 = 19`. Final state `(4,3)`, A holds `4 = x`. ✓

Dijkstra pops states in order of `dist`. The path above is the cheapest one the algorithm finds for `A = 4`, because any state with `A = 4` is first popped at the smallest total cost, and the parent chain recorded at that time gives exactly these six moves.

## Pitfalls

- Counting only moved water. The sample total `19` includes fills and empties. Count every operation's water amount.
- Using BFS. Costs differ per edge, so BFS by number of moves gives the wrong answer for the total.
- Forgetting the target can be any state with `A = x`, not only one specific `B`.
- Generating a no-op move (for example `FILL A` when A is already full) as an edge. Skip zero-cost self-loops, or they only waste time.
- Reconstructing the path with the wrong direction. Store parents when relaxing, then reverse when printing.
- Impossible case: no state with `A = x` is reachable. Print `-1`.

## Complexity

- Time: `O(a * b * log(a * b))` with a binary heap, about `1e6 * 20` operations with six edges per state.
- Space: `O(a * b)` for `dist`, `parent`, and move arrays.

## Key takeaway

Physical puzzles with containers are shortest-path problems on a small grid of states. The only subtle part is the cost model: charge each operation by the water it handles.
