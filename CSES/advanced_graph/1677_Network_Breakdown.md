# [1677 — Network Breakdown](https://cses.fi/problemset/task/1677)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Graph Problems  
**Tags:** DSU, offline, reverse processing  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Syrjälä's network has `n` computers and `m` connections between them. The network consists of components of computers that can send messages to each other.

Nobody in Syrjälä understands how the network works. For this reason, if a connection breaks down, nobody will repair it. In this situation a component may be divided into two components.

Your task is to calculate the number of components after each connection breakdown.

### Input

The first input line has three integers `n`, `m` and `k`: the number of computers, connections and breakdowns. The computers are numbered `1,2,...,n`.

Then, there are `m` lines describing the connections. Each line has two integers `a` and `b`: there is a connection between computers `a` and `b`. Each connection is between two different computers, and there is at most one connection between two computers.

Finally, there are `k` lines describing the breakdowns. Each line has two integers `a` and `b`: the connection between computers `a` and `b` breaks down.

### Output

After each breakdown, print the number of components.

### Constraints

- `1 <= n <= 1e5`
- `1 <= m <= 2e5`
- `1 <= k <= m`
- `1 <= a,b <= n`

### Examples

**Input**
```
5 5 3
1 2
1 3
2 3
3 4
4 5
3 4
2 3
4 5
```

**Output**
```
2 2 3
```

---

## What this problem really is

Start from a graph, delete `k` given edges in order, and after each deletion report the number of connected components.

## What to use

| Tool | Why |
|------|-----|
| Reverse offline | Deletions backward = insertions forward (DSU-friendly) |
| DSU | Maintain components while adding edges |
| Edge set / hash | Know which edges survive all breakdowns as the starting point |

## Approach

**Root idea:** DSU cannot delete edges easily, but it can add them. Process breakdowns in reverse: begin from the graph with all `k` broken edges already removed, then re-add broken edges from last to first, recording component counts.

**Mental picture:** time runs backward; each “breakdown” is undone by plugging the cable back in. Component count only drops when the plug links two different islands.

**How that becomes the method**

1. Store all `m` edges; mark the `k` breakdown edges (as pairs — normalize `min,max`).
2. DSU on `n` nodes; `comps = n`.
3. Unite every edge that is **not** among the `k` breakdowns; decrease `comps` when a unite succeeds.
4. `ans[k] = comps` is the count after all `k` breakdowns (before re-adding any).
5. For `i = k-1 .. 0`: re-add breakdown edge `i`; if unite merges, `comps--`; set `ans[i] = comps` (state after first `i` breakdowns… careful with indexing).
6. Actually: after building the “all k removed” graph, walk breakdowns from last to first:
   - currently `ans_rev` corresponds to after `k` deletions;
   - store that as the answer for step `k`;
   - then add back the `k`-th deleted edge, store answer for step `k-1`, etc.
7. Print answers for deletions `1..k` in chronological order.

**Data structures:** vector of edges; `set`/`map` of broken pairs; DSU parent+size; answer array length `k`.

## Walkthrough (on a sample)

Edges: `1-2,1-3,2-3,3-4,4-5`. Break `3-4`, then `2-3`, then `4-5`.

- After removing all three: remaining `1-2,1-3` → components `{1,2,3}`, `{4}`, `{5}` → `3` (after 3rd breakdown).
- Add back `4-5`: `{1,2,3}`, `{4,5}` → `2` (after 2nd).
- Add back `2-3`: already same component → still `2` (after 1st).
- Chronological answers: `2, 2, 3`.

## Pitfalls

- Forward simulation with real deletions (hard without link-cut / offline reverse).
- Breakdown edges might need canonical `(min,max)` matching.
- Isolated vertices count as components from the start (`comps` starts at `n`).

## Complexity

- Time: `O((n + m) α(n))`
- Space: `O(n + m)`

## Key takeaway

Edge deletions offline → reverse time and DSU-insert; record component counts on the way back.
