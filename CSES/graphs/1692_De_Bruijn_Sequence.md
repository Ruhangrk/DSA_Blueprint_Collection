# [1692 — De Bruijn Sequence](https://cses.fi/problemset/task/1692)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, Eulerian path, de Bruijn graph  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to construct a minimum-length bit string that contains all possible substrings of length `n`. For example, when `n=2`, the string `00110` is a valid solution, because its substrings of length `2` are `00`, `01`, `10` and `11`.

### Input

The only input line has an integer `n`.

### Output

Print a minimum-length bit string that contains all substrings of length `n`. You can print any valid solution.

### Constraints

- `1 <= n <= 15`

### Examples

**Input**
```
2
```

**Output**
```
00110
```

---

## What this problem really is

Build a shortest binary string whose length-`n` windows cover all `2^n` bitstrings. Min length is `2^n + n - 1` (de Bruijn sequence `B(2,n)`).

## What to use

| Tool | Why |
|------|-----|
| de Bruijn graph | Nodes = `(n-1)`-bit strings; edge for each `n`-bit string |
| Hierholzer Euler tour | Each edge = one substring; tour gives the sequence |
| Bitmask nodes `0 .. 2^(n-1)-1` | Compact representation |

## Approach

**Root idea:** an `n`-mer overlaps the next in `n-1` bits. So nodes are prefixes of length `n-1`; appending one bit is an edge labeled `0` or `1`. An Eulerian path in this digraph visits every `n`-mer exactly once.

**Mental picture:** each step adds one bit; the last `n` bits are a unique window you haven’t used yet until all are used.

**Data structures**

- For each node `u` in `[0, 2^(n-1))`, two outgoing edges (append `0` / `1`) to `((u<<1) & mask) | bit`.
- Hierholzer on that graph; record the bit of each edge used.
- Start from node `0`; output the starting `(n-1)` zeros (or any start node’s bits) plus the edge bits along the tour.

**Steps**

1. If `n==1`, print `01` or `10` (both length-1 windows).
2. Build implicit graph; run Hierholzer from `0`.
3. Emit initial `n-1` bits of start, then each edge’s bit → string of length `2^n + n - 1`.

## Walkthrough (on a sample)

`n=2`: nodes `0,1` (1-bit). Euler tour of four edges yields **`00110`**, whose windows are all of `00,01,11,10`.

## Pitfalls

- Building on `n`-bit nodes instead of `(n-1)` — wrong overlap model.
- Not using every edge → missing substrings.
- `n=15` → `2^14` nodes, `2^15` edges — fine in time if Hierholzer is linear.

## Complexity

- Time/Space: `O(2^n)`

## Key takeaway

**All length-`n` binary windows in min string = Euler tour in the de Bruijn graph** of `(n-1)`-bit nodes with bit-labeled edges.
