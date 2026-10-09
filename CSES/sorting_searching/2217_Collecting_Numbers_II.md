# [2217 — Collecting Numbers II](https://cses.fi/problemset/task/2217)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** permutation, positions, swaps, local updates  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array that contains each number between `1 ... n` exactly once. Your task is to collect the numbers from `1` to `n` in increasing order.

On each round, you go through the array from left to right and collect as many numbers as possible.

Given `m` operations that swap two numbers in the array, your task is to report the number of rounds after each operation.

### Input

The first line has two integers `n` and `m`: the array size and the number of operations.

The next line has `n` integers `x1, x2, ..., xn`: the numbers in the array.

Finally, there are `m` lines that describe the operations. Each line has two integers `a` and `b`: the numbers at positions `a` and `b` are swapped.

### Output

Print `m` integers: the number of rounds after each swap.

### Constraints

- `1 <= n, m <= 2e5`
- `1 <= a, b <= n`

### Examples

**Input**
```
5 3
4 2 1 5 3
2 3
1 5
2 3
```

**Output**
```
2
3
4
```

---

## What this problem really is

Same round-count as Collecting Numbers, but after each **swap of two positions** you must report the new answer — too many ops to rebuild from scratch every time.

## What to use

| Tool | Why |
|------|-----|
| `pos[v]` + current `rounds` | Same invariant: rounds = `1 +` count of `pos[k+1] < pos[k]` |
| Local edge updates | A swap only changes positions of two values; only pairs `(v-1,v)` and `(v,v+1)` for those values can flip |
| Set of affected values | Collect `{x-1,x,x+1}` for each swapped value `x` (in range) |

## Approach

**Root idea:** the answer is completely determined by the `n-1` boolean edges “does `k+1` sit left of `k`?”. Swapping two array positions changes `pos` for at most two values; only edges incident to those values can change. Undo old contributions, swap, apply new contributions.

**Mental picture:** a chain of checkpoints `1—2—3—...—n` where each link is “OK (rightward)” or “break (needs new round)”. Moving two labels on the line only touches links next to those labels.

**Data structures**

- `x[1..n]` current array, `pos[1..n]` inverse.
- Integer `rounds` (maintain explicitly), or recompute from affected edges only.

**Helper:** for a value `v` in `1..n-1`, edge `v` contributes `1` iff `pos[v+1] < pos[v]`.

**Per swap of positions `a,b`**

1. If `a == b`, just print current `rounds`.
2. Let values involved be `u = x[a]`, `v = x[b]` (and neighbors). Build a small set of edge indices that might change: for each value `t in {u,v}`, add edges `t-1` and `t` if valid (edges labeled by the left value of the pair).
3. Subtract the old contribution of those edges from `rounds`.
4. Swap `x[a], x[b]` and update `pos[u], pos[v]`.
5. Add the new contribution of those edges.
6. Print `rounds`.

(Initial `rounds` computed like the static Collecting Numbers problem.)

## Walkthrough (on a sample)

Start `4 2 1 5 3` → rounds **3** (same as static sample).

Swap positions `2,3`: array `4 1 2 5 3`. Now `1` before `2` before `3` with one break involving `4`… after update, rounds become **2**.

Further swaps move values so more descents appear → **3**, then **4**, matching the sample outputs.

Each swap only rechecks a handful of edges around the two moved values.

## Pitfalls

- Updating `pos` but forgetting to adjust `rounds` by **delta of edges** (both remove-old and add-new).
- Missing neighbor edges (`v-1` as well as `v`).
- Double-counting the same edge when both swapped values touch it — use a set of edge ids.
- Swapping equal positions / same value edge cases.

## Complexity

- Time: `O(n + m)` (each swap `O(1)` edges)  
- Space: `O(n)`

## Key takeaway

**Dynamic collecting-rounds = maintain descent edges on `pos[k]→pos[k+1]`** and, on each swap, refresh only the few edges touching the moved values.
