# [2418 — Grid Path Construction](https://cses.fi/problemset/task/2418)

**Platform:** CSES  
**Source:** CSES Problem Set — Construction Problems  
**Tags:** construction, Hamiltonian path, grid  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an `n x m` grid and two squares `a=(y1,x1)` and `b=(y2,x2)`, create a path from `a` to `b` that visits each square exactly once.

### Input

The first input line has an integer `t`: the number of tests.

After this, there are `t` lines that describe the tests. Each line has six integers `n`, `m`, `y1`, `x1`, `y2` and `x2`.

In all tests `1 <= y1,y2 <= n` and `1 <= x1,x2 <= m`. In addition, `y1 != y2` or `x1 != x2`.

### Output

Print `YES` if it is possible to construct a path, and `NO` otherwise.

If there is a path, also print its description which consists of characters `U` (up), `D` (down), `L` (left) and `R` (right). If there are several paths, you can print any of them.

### Constraints

- `1 <= t <= 100`
- `1 <= n <= 50`
- `1 <= m <= 50`

### Examples

**Input**
```
5
1 3 1 1 1 3
1 3 1 2 1 3
2 2 1 1 2 2
2 2 1 1 2 1
4 7 1 3 3 6
```

**Output**
```
YES
RR
NO
NO
YES
RDL
YES
RRRRDDDLLLLLLUUURDDRURDRURD
```

---

## What this problem really is

Hamiltonian path on the grid graph from given start to given end (adjacent 4-way moves), output the move string — or impossible.

## What to use

| Tool | Why |
|------|-----|
| Color parity | Grid is bipartite; path of length `nm-1` needs correct start/end colors |
| Casework on dimensions | `1×m`, `2×m`, general `n×m` have known snake constructions |
| Explicit snakes | Row-by-row boustrophedon with local rewires to fix endpoints |

## Approach

**Root idea:** a rectangular grid has a Hamiltonian path between two cells iff color parity matches (`(y1+x1) % 2` vs length) and a few small forbidden configurations are avoided (e.g. both ends interior in awkward `2×odd` cases). When possible, build a snake and splice/reroute so it starts at `a` and ends at `b`.

**Mental picture:** draw a lawnmower path covering every cell; then rotate/flip the pattern or flip a cycle so the cut endpoints land on the requested squares.

**How that becomes the method**

1. If `n*m == 1`: impossible (start≠end required).  
2. Check bipartite parity: path has `nm-1` edges; start and end must have opposite colors iff `nm` even, same color iff `nm` odd.  
3. Handle strips: `n=1` or `m=1` — only possible if `a`,`b` are the two ends of the unique path covering the line in order (or reverse).  
4. For `n,m >= 2`, use standard constructions (serpentine + U-turns) parameterized so endpoints match; some pairs on `2×m` need special flips.  
5. Emit the string of moves along the built cell sequence.

**Data structures:** list of cells in path order; convert consecutive cells to `UDRL`.

## Walkthrough (on a sample)

`1×3` from end to end works (`RR`); from middle to end cannot cover without revisit → `NO`. `2×2` diagonal impossible by parity; adjacent edge works.

## Pitfalls

- Ignoring checkerboard parity.  
- Assuming any two distinct cells work on large grids.  
- Off-by-one in coordinates (`y` row, `x` col).

## Complexity

- Time: `O(n m)` per test  
- Space: `O(n m)`

## Key takeaway

**Grid Hamiltonian a→b = parity + strip/special-case checks, then a snake construction with endpoint wiring.**
