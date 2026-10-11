# [3158 — Bus Companies](https://cses.fi/problemset/task/3158)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Graph Problems  
**Tags:** Dijkstra, virtual nodes, graphs  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` cities and `m` bus companies. Each bus company operates in specific cities and sells tickets for a specific price. Buying a ticket from a bus company allows you to travel between any two cities that the company operates in.

Determine the cost of the cheapest route from Syrjälä to every city.

### Input

The first line has two integers `n` and `m`: the number of cities and bus companies. The cities are numbered `1,2,...,n`, and city `1` is Syrjälä.

The next line has `m` integers `c1, c2, ..., cm`: the ticket costs for each bus company.

After that, there are `m` pairs of lines describing the cities for each bus company.

The first line of each pair has a single integer `k`: the number of cities the bus company operates in.

The second line of each pair has `k` distinct integers `a1, a2, ..., ak`: the cities the bus company operates in.

You can assume that it is possible to travel from Syrjälä to all other cities.

### Output

Print `n` integers: the cheapest route costs from Syrjälä to cities `1,2,...,n`.

### Constraints

- `1 <= n, m <= 1e5`
- `1 <= c <= 1e9`
- `2 <= k <= n`
- `1 <= a <= n`
- the sum of all `k` is at most `2e5`

### Examples

**Input**
```
5 3
4 3 2
3
1 4 3
2
5 1
4
2 3 4 5
```

**Output**
```
0 5 4 4 3
```

---

## What this problem really is

Each company is a clique: pay `c` once, then travel freely among its cities. Find multi-source style shortest costs from city `1` to all cities.

## What to use

| Tool | Why |
|------|-----|
| Virtual node per company | Avoid materializing `O(k^2)` clique edges |
| Dijkstra | Non-negative ticket costs |
| Edge pattern city ↔ company | Enter company paying `c`, exit free (or symmetric) |

## Approach

**Root idea:** paying for company `j` lets you jump between any of its cities. Model that as: from a city in the company, pay `c_j` to enter a gadget node `comp_j`, then go to any other city of that company at cost `0`.

**Mental picture:** each company is a hub airport. Landing at the hub costs the ticket; from the hub you can fly free to any city the company serves.

**How that becomes the method**

1. Nodes: cities `1..n` plus company nodes `n+1..n+m`.
2. For each company `j` with cost `c_j` and city list `A`:
   - for each city `a in A`: add edge `a → (n+j)` with weight `c_j` (buy ticket / enter hub);
   - for each city `a in A`: add edge `(n+j) → a` with weight `0` (ride to any served city).
3. Dijkstra from city `1`; answer is `dist[1..n]` (`dist[1]=0`).
4. Total edges `O(sum k)`, so Dijkstra is `O((n+m+sum k) log (n+m))`.

(Equivalent: 0-cost edges among cities via the hub; never build the dense clique.)

**Data structures:** adj list of pairs `(to, w)`; Dijkstra PQ; `long long dist[]`.

## Walkthrough (on a sample)

Companies: cost `4` on `{1,4,3}`, cost `3` on `{5,1}`, cost `2` on `{2,3,4,5}`.

- City `1`: `0`.
- Buy company 2 (cost `3`) at `1` → reach `5` at cost `3`.
- Buy company 1 (cost `4`) → reach `3`,`4` at `4`.
- From `5` or `4` buy company 3 (cost `2`) → reach `2` at `3+2=5` (via `5`) or `4+2=6` (worse). Best to `2` is `5`.

Matches `0 5 4 4 3`.

## Pitfalls

- Building explicit cliques → TLE/MLE (`sum k^2` can explode).
- Charging `c` on every hop inside a company (should pay once per ticket use — the hub model pays once per entry).
- Using `int` for distances.

## Complexity

- Time: `O((n + m + sum k) log (n + m))`
- Space: `O(n + m + sum k)`

## Key takeaway

Company ticket = clique travel → one virtual hub node with pay-to-enter / free-to-exit edges, then Dijkstra.
