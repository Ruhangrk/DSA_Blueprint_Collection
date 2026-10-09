# [1163 — Traffic Lights](https://cses.fi/problemset/task/1163)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** set, multiset, gaps, offline reverse  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There is a street of length `x` whose positions are numbered `0, 1, ..., x`. Initially there are no traffic lights, but `n` sets of traffic lights are added to the street one after another.

Your task is to calculate the length of the longest passage without traffic lights after each addition.

### Input

The first input line contains two integers `x` and `n`: the length of the street and the number of sets of traffic lights.

Then, the next line contains `n` integers `p1, p2, ..., pn`: the position of each set of traffic lights. Each position is distinct.

### Output

Print the length of the longest passage without traffic lights after each addition.

### Constraints

- `1 <= x <= 1e9`
- `1 <= n <= 2e5`
- `0 < pi < x`

### Examples

**Input**
```
8 3
3 6 2
```

**Output**
```
5 3 3
```

---

## What this problem really is

Start with one segment `[0,x]`. Each new light at `p` splits the segment containing `p` into two. After each insert, report the **maximum gap** between consecutive lights (including endpoints `0` and `x`).

## What to use

| Tool | Why |
|------|-----|
| `set` of light positions | Ordered neighbors of a new point |
| `multiset` of gap lengths | Track all passage lengths; query max |
| Endpoints `0` and `x` | Always present as barriers |

## Approach

**Root idea:** maintain the sorted set of positions `{0, lights..., x}` and a multiset of distances between consecutive positions. When inserting `p`, find neighbors `L < p < R`, remove gap `R-L`, add gaps `p-L` and `R-p`.

**Mental picture:** cutting a rope at point `p` — one piece becomes two; the longest remaining piece is what you report.

**Data structures**

- `set<int> pos` initially `{0, x}`.
- `multiset<int> gaps` initially `{x}` (one passage of length `x`).

**Per light `p`**

1. `auto it = pos.lower_bound(p);` → `R = *it`, `L = *prev(it)`.
2. Erase one occurrence of `R - L` from `gaps`.
3. Insert `p - L` and `R - p` into `gaps`.
4. Insert `p` into `pos`.
5. Print `*gaps.rbegin()` (current maximum gap).

## Walkthrough (on a sample)

`x=8`. Start gap `8`.

Add `3`: split `8` → `3` and `5`; max **5**.

Add `6`: neighbors `3,8`; replace gap `5` with `3` and `2`; gaps `{3,3,2}`; max **3**.

Add `2`: neighbors `0,3`; replace `3` with `2` and `1`; max still **3**.

## Pitfalls

- Forgetting endpoints `0` and `x`.
- Using `set` for gaps — duplicate gap lengths need `multiset`.
- Printing after all inserts only — need output after **each** addition.
- `x` up to `1e9` — cannot allocate an array of length `x`.

## Complexity

- Time: `O(n log n)`  
- Space: `O(n)`

## Key takeaway

**Longest gap after inserting points = set of positions + multiset of consecutive distances**; each insert splits one gap into two.
