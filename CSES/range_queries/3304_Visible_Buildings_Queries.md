# [3304 — Visible Buildings Queries](https://cses.fi/problemset/task/3304)

**Platform:** CSES  
**Source:** CSES Problem Set — Range Queries  
**Tags:** range queries, sparse table / cartesian tree, offline  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` buildings in a row numbered `1, 2, ..., n` from left to right. You are standing to the left of the first building. You can see a building if it is taller than all buildings to its left.

Your task is to process `q` queries: If only buildings in range `[a, b]` existed, how many buildings would you see?

### Input

The first line has two integers `n` and `q`: the number of buildings and queries.

The second line has `n` integers `h1, h2, ..., hn`: the heights of the buildings.

Finally, there are `q` lines describing the queries. Each line has two integers `a` and `b`.

### Output

For each query, print one integer: the number of visible buildings.

### Constraints

- `1 <= n <= 1e5`
- `1 <= q <= 2e5`
- `1 <= hi <= 1e9`
- `1 <= a <= b <= n`

### Examples

**Input**
```
5 3
4 1 2 2 3
1 5
2 5
3 4
```

**Output**
```
1
3
1
```

---

## What this problem really is

For subarray `h[a..b]`, count left-to-right **strict prefix maxima** (how many times the running max strictly increases), viewing from the left of that subarray only.

## What to use

| Tool | Why |
|------|-----|
| Next greater / cartesian structure | Visibility jumps only at new records |
| Sparse table for RMQ | Find position of maximum in a range |
| Divide-and-conquer / recursive count | Max in `[a,b]` is always visible; recurse on left part only |

## Approach

**Root idea:** in `[a,b]`, the **rightmost** occurrence of the global maximum is visible (it’s a new record when you reach it). Everything after it is shorter or equal so not a new strict max after that peak if we require strict — actually: walking left→right, a building is visible iff it’s `>` all to its left **inside the range**.

So the leftmost building is always visible. Then the next visible is the first strictly taller, and so on — the sequence of strict prefix maxima.

**Efficient structure:** the position `p` of the maximum in `[a,b]` (break ties by preferring the leftmost max for “first time max reaches that height”). That building is visible. All visible buildings are in `[a,p]` (nothing after `p` can be `>` max). Recurse: answer(`[a,b]`) = answer(`[a,p-1]`) + `1` (if we take leftmost max carefully).

More precisely with **leftmost** maximum `p` in `[a,b]`: it is the last new record of the whole range; visibles = visibles in `[a,p-1]` plus `1`.

Implement with sparse table that returns the index of the minimum index among maxima (RMQ on height with tie → smaller index).

**Steps**

1. Build RMQ that returns index of leftmost maximum on a range.
2. For each query, recurse/stack: `cnt=0`, while range nonempty: `p = leftmost_max(a,b)`; `cnt++`; set `b = p-1`… wait that's wrong direction.

Correct recursion for leftmost-max:

```
count(a,b):
  if a>b: return 0
  p = index of leftmost maximum in [a,b]
  return 1 + count(a, p-1)
```

Because after the first time we hit the overall max (at its leftmost), no later building is taller; all earlier visibles are exactly the visibles of `[a,p-1]`.

3. Memoization / sparse doubling can speed repeated subproblems; naive recursion per query is `O(visibles * log n)` which can TLE. Better: precompute for each position the next strict greater to the left, then each query is jumping with binary lifting while `pos` in `[a,b]` — count jumps.

**Binary lifting view (often cleaner):**

- `up[0][i]` = rightmost `j < i` with `h[j] > h[i]` (previous greater strict), or `0`.
- Actually for prefix maxima from `a`: start at `a` (always visible), then repeatedly jump to the next index `> cur` in range with height `> h[cur]`.

Precompute next greater to the **right**: `ng[i]` = smallest `j > i` with `h[j] > h[i]`. Then from left bound `a`, visibles are `a`, `ng[a]`, `ng[ng[a]]`, … while `<= b`. Count with binary lifting on `ng` for `O(log n)` per query.

**Steps (next-greater + lifting)**

1. Mono stack → `ng[i]` (next strict greater on the right).
2. Binary lift `ng`.
3. Query `[a,b]`: start `i=a`, `ans=1`; jump using lifting to count how many next-greater hops stay `<= b`.

## Walkthrough (on a sample)

`h = [4,1,2,2,3]`

- `[1,5]`: only `4` is a record → **`1`**
- `[2,5]`: `1`, then `2`, then `3` → **`3`**
- `[3,4]`: `2` then equal `2` not strict → **`1`**

## Pitfalls

- Non-strict `>=` vs strict `>` — problem says taller than all to the left → strict.
- `O(n)` scan per query — TLE.
- Tie-breaking when finding “maximum” for divide-and-conquer.

## Complexity

- Time: `O((n + q) log n)` with next-greater + binary lifting  
- Space: `O(n log n)`

## Key takeaway

**Visible buildings in a subarray = chain of strict prefix maxima.** Precompute next greater to the right and jump with binary lifting from `a` while inside `[a,b]`.
