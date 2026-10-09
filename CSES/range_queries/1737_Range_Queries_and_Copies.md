# [1737 — Range Queries and Copies](https://cses.fi/problemset/task/1737)

**Platform:** CSES  
**Source:** CSES Problem Set — Range Queries  
**Tags:** persistent segment tree, path copying  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to maintain a list of arrays which initially has a single array. You have to process the following types of queries:

1. Set the value at position `a` in array `k` to `x`.
2. Calculate the sum of values in range `[a,b]` in array `k`.
3. Create a copy of array `k` and add it to the end of the list.

### Input

The first input line has two integers `n` and `q`: the array size and the number of queries.

The next line has `n` integers `t1, t2, ..., tn`: the initial contents of the array.

Finally, there are `q` lines describing the queries. The format of each line is one of the following: `"1 k a x"`, `"2 k a b"` or `"3 k"`.

### Output

Print the answer to each sum query.

### Constraints

- `1 <= n, q <= 2e5`
- `1 <= ti, x <= 1e9`
- `1 <= a <= b <= n`

### Examples

**Input**
```
5 6
2 3 1 2 5
3 1
2 1 1 5
2 2 1 5
1 2 2 5
2 1 1 5
2 2 1 5
```

**Output**
```
13
13
13
15
```

---

## What this problem really is

Many **versions** of an array: point set and range sum on a chosen version, plus **clone** a version into a new array id. Copying must be cheap — not `O(n)` per clone.

## What to use

| Tool | Why |
|------|-----|
| Persistent (immutable) segment tree | Update creates `O(log n)` new nodes; rest shared |
| `root[k]` | Root pointer for each array version |
| Node `{left, right, sum}` | Standard sum segtree, never mutate old nodes |

## Approach

**Root idea:** each array `k` is the root of a segtree. A copy just duplicates the root pointer (full structural sharing). A point update clones only the path from root to the leaf, leaving sibling subtrees shared with the old version.

**Mental picture:** versions are different “entry doors” into the same forest of nodes. Changing one cell builds a new hallway of `O(log n)` doors; rooms you don’t touch stay in common.

**Data structures**

- Persistent segtree nodes allocated in a pool (`vector` of structs).
- `vector<int> root` — `root[k]` is the node index for array `k` (1-based array ids as in the problem).
- Build once from `t` → `root[1]`.

**Operations**

1. **Update** `k, a, x`: `root[k] = update(root[k], a, x)` — recursive path copy; new leaf sum = `x`; parents recompute `sum = left.sum + right.sum`.
2. **Query** `k, a, b`: standard range sum on tree `root[k]` (read-only).
3. **Copy** `k`: `root.push_back(root[k])` — new array id points at the same root until it diverges on a later update.

**Steps**

1. Build version `1` from the initial array.
2. Process queries; on type `3`, append a new root equal to `root[k]`.
3. Type `1` replaces only that version’s root with the updated persistent root.

## Walkthrough (on a sample)

Start array1 = `2 3 1 2 5`, sum `13`.

Type `3 1`: array2 shares the same tree as array1.

Queries on both still **13**.

Type `1 2 2 5`: update only array2 → `2 5 1 2 5`. New path for index `2`; array1 still sees old leaf `3`.

Sums: array1 still **13**, array2 **15**.

## Pitfalls

- Mutating a node in place — corrupts other versions that share it.
- On copy, deep-cloning the whole tree — TLE/MLE; only share the root.
- Updating array `k` but writing into the wrong `root[k]`.
- Under-allocating node pool: each update needs `~4 * log n` new nodes worst case; size for `q` updates at `2e5`.

## Complexity

- Time: `O((n + q) log n)`  
- Space: `O((n + q) log n)` nodes

## Key takeaway

**Arrays with cheap copies = persistent segtree** — copy shares the root; point updates path-copy `O(log n)` nodes and leave other versions untouched.
