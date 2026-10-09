# [1143 — Hotel Queries](https://cses.fi/problemset/task/1143)

**Platform:** CSES  
**Source:** CSES Problem Set — Range Queries  
**Tags:** segment tree, binary search on tree  
**Difficulty / Rating:** Medium–Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` hotels on a street. For each hotel you know the number of free rooms. Your task is to assign hotel rooms for groups of tourists. All members of a group want to stay in the same hotel.

The groups will come to you one after another, and you know for each group the number of rooms it requires. You always assign a group to the first hotel having enough rooms. After this, the number of free rooms in the hotel decreases.

### Input

The first input line contains two integers `n` and `m`: the number of hotels and the number of groups. The hotels are numbered `1, 2, ..., n`.

The next line contains `n` integers `h1, h2, ..., hn`: the number of free rooms in each hotel.

The last line contains `m` integers `r1, r2, ..., rm`: the number of rooms each group requires.

### Output

Print the assigned hotel for each group. If a group cannot be assigned a hotel, print `0` instead.

### Constraints

- `1 <= n, m <= 2e5`
- `1 <= hi <= 1e9`
- `1 <= ri <= 1e9`

### Examples

**Input**
```
8 5
3 2 4 1 5 5 2 6
4 4 7 1 1
```

**Output**
```
3 5 0 1 1
```

---

## What this problem really is

Array of capacities. For each query `r`, find the **leftmost** index with `h[i] >= r`, then decrease that entry by `r` (or report `0`). Online, `n,m` large.

## What to use

| Tool | Why |
|------|-----|
| Segment tree of **max** | Root max tells if any hotel works; walk left/right for first sufficient |
| Point update | Subtract `r` from chosen hotel |
| Tree descent | `O(log n)` find-leftmost instead of scanning |

## Approach

**Root idea:** store range maxima. If the left child’s max `>= r`, the answer lies left; else go right (if right max `>= r`). That finds the first hotel with enough rooms.

**Mental picture:** a street of hotels; jump using “is there still a big enough hotel in this half?”

**Data structures**

- Segtree `t[node] = max` over segment.
- Build from `h[1..n]`.

**For each group `r`**

1. If `t[root] < r` → print `0`.
2. Else descend from root: prefer left child if `max(left) >= r`, else right.
3. At leaf `i`: print `i`, update `h[i]-=r` / set leaf to new value.

## Walkthrough (on a sample)

`r=4` → first hotel with `>=4` is index `3` (value `4`).  
Next `4` → hotel `5`.  
`7` → none → `0`.  
`1` twice → hotel `1` both times after updates.

## Pitfalls

- Picking any hotel with enough rooms — must be the **first**.
- Linear scan per group — TLE.
- Updating without refreshing segtree maxima.

## Complexity

- Time: `O((n + m) log n)`  
- Space: `O(n)`

## Key takeaway

**First index with `a[i] >= x` under updates = max-segtree + walk left when left max is enough.**
