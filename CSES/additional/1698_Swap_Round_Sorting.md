# [1698 — Swap Round Sorting](https://cses.fi/problemset/task/1698)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems II  
**Tags:** Permutation cycles, involutions, rotations as products of reflections, constructive output  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a permutation of `1..n`. In one swap round, you choose any number of disjoint pairs of positions and swap each pair. Find the minimum number of rounds to sort the array, and print the pairs used in each round.

### Input

- The first line has `n`.
- The second line has the permutation.

### Output

First print `k`, the minimum number of rounds. Then for each round print the number of swaps, followed by the swaps (one pair of indices per line). Any valid solution is accepted.

### Constraints

- `1 <= n <= 2e5`

### Examples

**Input**
```
5
5 2 1 3 4
```

**Output**
```
2
2
1 3
4 5
1
3 5
```

---

## What this problem really is

One round is an involution: it swaps some disjoint pairs and leaves the rest fixed. Sorting means the composition of rounds must be the inverse of the permutation. So the question is how many involutions multiply to the permutation. The answer is always at most `2`, and the construction is explicit.

## What to use

| Tool | Why |
|------|-----|
| Cycle decomposition of `i -> a[i]` | Each cycle can be handled on its own |
| Reflections on a cycle | Two reflections of a cycle compose to a rotation of it |
| Two rounds | Each reflection is a round of disjoint swaps |

## Approach

**Answer 0:** the array is already sorted.

**Answer 1:** a single round only swaps disjoint pairs. That is possible exactly when every cycle has length at most `2`. Then each 2-cycle `(i, a[i])` is one swap.

**Answer 2:** otherwise. Every permutation is a product of two involutions. Construct them cycle by cycle.

**Root idea (reflections):** take one cycle, listed as `c[0], c[1], ..., c[L-1]`, where the value at `c[j]` should move to `c[j+1]` (indices mod `L`). Define two reflections on the cycle indices:
- Round 1: `c[j]` pairs with `c[(-j) mod L]`.
- Round 2: `c[k]` pairs with `c[(1 - k) mod L]`.

Each reflection pairs positions up and fixes the rest, so each round is a set of disjoint swaps. Composing them moves the element at `c[j]` to `c[(1 - (-j)) mod L] = c[j+1]`, which is exactly the rotation needed.

**Mental picture:** imagine the cycle as beads on a ring. Round 1 flips the ring like a mirror through one axis. Round 2 flips it through a second axis, shifted by one bead. Two mirror flips in a row amount to a rotation by one bead. Each mirror flip is a set of disjoint swaps, so a round can do all cycles at once.

**How that becomes the method**

1. If sorted, print `0`.
2. Find cycles of `i -> a[i]`. If all have length `<= 2`, print `1` and the swaps `(i, a[i])` for 2-cycles with `i < a[i]`.
3. Otherwise print `2`. For each cycle of length `L >= 2`:
   - Round 1: for `j` from `0` to `L-1`, if `j < (-j) mod L`, swap `c[j]` and `c[(-j) mod L]`.
   - Round 2: for `k` from `0` to `L-1`, if `k < (1-k) mod L`, swap `c[k]` and `c[(1-k) mod L]`.
4. Print the number of swaps in each round, then the pairs.

Note that a fixed point of a reflection (where `j = (-j) mod L`) is simply skipped.

**Data structures:** a visited array for cycles, and lists of swaps for the two rounds.

## Walkthrough (on a sample)

`a = 5 2 1 3 4`, `n = 5`. Not sorted, and there is a cycle of length `4`, so the answer is `2`.

Cycles of `i -> a[i]`:
- `1 -> 5 -> 4 -> 3 -> 1`, length `4`. Take `c = [1, 5, 4, 3]`. Check: `a[1] = 5 = c[1]`, `a[5] = 4 = c[2]`, `a[4] = 3 = c[3]`, `a[3] = 1 = c[0]`.
- `2 -> 2`, a fixed point, skipped.

Round 1 (`j`, `(-j) mod 4`):
- `j = 0`: `0` pairs with `0`, skip.
- `j = 1`: pairs with `3`. Swap `c[1] = 5` and `c[3] = 3`. So swap positions `(3, 5)`.
- `j = 2`: pairs with `2`, skip.
- `j = 3`: pairs with `1`, already done.

Round 1 has one swap: `(3, 5)`.

Round 2 (`k`, `(1-k) mod 4`):
- `k = 0`: pairs with `1`. Swap `c[0] = 1` and `c[1] = 5`. So swap `(1, 5)`.
- `k = 1`: pairs with `0`, already done.
- `k = 2`: pairs with `(1-2) mod 4 = 3`. Swap `c[2] = 4` and `c[3] = 3`. So swap `(3, 4)`.
- `k = 3`: pairs with `2`, already done.

Round 2 has two swaps: `(1, 5)` and `(3, 4)`.

Simulate: `[5, 2, 1, 3, 4]`
- Round 1 swap positions `3, 5`: `[5, 2, 4, 3, 1]`.
- Round 2 swap positions `1, 5`: `[1, 2, 4, 3, 5]`.
- Round 2 swap positions `3, 4`: `[1, 2, 3, 4, 5]`. Sorted. ✓

The output differs from the sample but is valid, and any valid solution is accepted.

## Pitfalls

- Printing 2 rounds when 1 suffices. A single round is enough exactly when all cycles have length at most 2.
- Repeating a pair within one round, or using an index in two swaps of the same round. Each round must use disjoint pairs.
- Mixing up the direction of the cycle. Build the cycle from `i -> a[i]` so that `c[j+1] = a[c[j]]`.
- Forgetting to apply round 1 before round 2. The order matters, since the composition is not commutative.
- Index off-by-one: the cycle arithmetic uses indices mod `L`, while the printed swaps use the actual positions.

## Complexity

- Time: `O(n)`.
- Space: `O(n)`.

## Key takeaway

Any cycle is a rotation, and a rotation is a product of two mirror flips. Each mirror flip is a round of disjoint swaps, so every permutation sorts in at most two rounds.
