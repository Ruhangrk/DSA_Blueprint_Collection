# [2214 — Inverse Inversions](https://cses.fi/problemset/task/2214)

**Platform:** CSES  
**Source:** CSES Problem Set — Construction Problems  
**Tags:** construction, permutations, inversions  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to create a permutation of numbers `1,2,...,n` that has exactly `k` inversions.

An inversion is a pair of positions `i < j` with `p[i] > p[j]`.

### Input

The only input line has two integers `n` and `k`.

### Output

Print a line that contains the permutation. You can print any valid solution.

### Constraints

- `1 <= n <= 1e6`
- `0 <= k <= n*(n-1)/2`

### Examples

**Input**
```
5 4
```

**Output**
```
1 5 2 4 3
```

---

## What this problem really is

Construct any perm of `1..n` with inversion count exactly `k` (guaranteed feasible).

## What to use

| Tool | Why |
|------|-----|
| Greedy place largest usable | Putting `x` at the front of a suffix creates `x-1`-style new inversions with a chosen offset |
| Decreasing runs | A block `m, m-1, ..., m-t+1` adds `t*(t-1)/2` plus cross inversions carefully |
| `O(n)` build | `n` up to `1e6` |

## Approach

**Root idea:** start from the identity (`0` inversions). To add inversions, peel the largest value you can still “afford” to move left across a suffix.

**Mental picture:** sorted line of people. Each time you take someone and walk them left past `t` people, you create `t` new inversions. Spend exactly `k` such steps.

**How that becomes the method**

1. Build `p = [1,2,...,n]` conceptually as a list / deque.  
2. While `k > 0`: let `t = min(k, remaining_max_move)` where the next unused largest element can jump left over at most `(how many still to its left)` positions. Classic pattern:  
   - For `i` from `n` downward, the value `i` can contribute at most `i-1` inversions by being placed to create a descending prefix.  
   - Concrete greedy: output a decreasing segment of length `t+1` when you spend `t` inversions, then continue on the rest.  
3. One standard construction: find largest `t` with `t*(t-1)/2 <= k`; write `t..1` first (adds `t*(t-1)/2`), set `k -= t*(t-1)/2`, then insert the remaining numbers with a small extra rotation/shift of one element by `k` positions to add the leftover `k < t` inversions.

**Simpler explicit greedy used often:**

- Keep next number to place from `1..n` in order for the “free” part.  
- While `k > 0`, take the largest remaining number that can add at most `k` inversions by appending it in a position creating exactly `min(k, #already_placed)` new inversions — place numbers from `n` down, each time appending to the front of the answer list (each new front max adds `#already` inversions) until adding the full `#already` would exceed `k`, then insert it `k` positions into the list.

**Data structures:** linked list / `vector` with careful `O(n)` construction (avoid `O(n^2)` inserts).

## Walkthrough (on a sample)

`n=5`, `k=4` → e.g. `1 5 2 4 3` has exactly four inversions.

## Pitfalls

- `O(n^2)` vector mid-inserts at `n=1e6`.  
- Overshooting `k`.  
- Not using all of `1..n`.

## Complexity

- Time: `O(n)`  
- Space: `O(n)`

## Key takeaway

**Exact-`k` inversions = greedy spend**: create big triangular descending chunks, then a small leftover shift.
