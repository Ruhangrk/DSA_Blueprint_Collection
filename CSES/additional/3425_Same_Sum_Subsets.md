> **Warning: this solution might be incorrect.** The existence argument below is sound. The construction (meet-in-the-middle with NTT) is outlined from editorial sources and has not been implemented or verified.

# [3425 — Same Sum Subsets](https://cses.fi/problemset/task/3425)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems II  
**Tags:** Pigeonhole, meet in the middle, polynomial hashing, NTT  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given `n` positive integers `x[1..n]`, find two disjoint subsets with equal sums. Print each subset as its size followed by its elements. If none exists, print `IMPOSSIBLE`.

### Input

- The first line has `n`.
- The second line has `x[1..n]`.

### Output

Two subsets, each printed as its size on one line and its elements on the next.

### Constraints

- `3 <= n <= 40`
- `sum of x[i] <= 2^n - 2`

### Examples

**Input**
```
6
1 2 3 5 7 8
```

**Output**
```
2
2 3
1
5
```

---

## What this problem really is

Two subsets are "the same sum" but may overlap, so we need to make them disjoint. The key is that a solution always exists under the given constraint.

## What to use

| Tool | Why |
|------|-----|
| Pigeonhole on subset sums | Guarantees a collision |
| Removing the common part of two subsets | Turns "equal sum, overlapping" into "equal sum, disjoint" |
| Meet in the middle (two halves of 20) | Makes enumerating subset sums feasible |
| NTT / FFT on residue counts | Finds a residue class where a collision must occur |

## Approach

**Existence (sound):** there are `2^n - 1` non-empty subsets, but their sums lie in `[1, sum]` and `sum <= 2^n - 2`. So at least two different non-empty subsets `A` and `B` share a sum. Let `C = A ∩ B`, and set `A' = A \ C`, `B' = B \ C`. Then `A'` and `B'` are disjoint and still have equal sums, because the sum removed from each side is the same `C`. Neither is empty: if `A'` were empty then its sum is `0`, so `B'` has sum `0` and must also be empty, which would make `A = B`.

**Finding the collision (outline only):**

1. Split the elements into two halves of at most 20 each.
2. Build the sum polynomial of each half. Their product gives the count of subsets for each sum.
3. The full polynomial is too large, so work modulo `x^k - 1` for a chosen `k`. This keeps only counts per residue class of the sum modulo `k`.
4. Pigeonhole: some residue class must hold more subsets than average, so it has two subsets with the same exact sum modulo the compression.
5. Enumerate subsets in that residue class (meet in the middle) until two subsets have the same exact sum.
6. Remove their intersection, as in the existence argument.

Steps 3 to 5 are where the real work is. I have not implemented or checked this construction.

## Walkthrough (on the sample)

Input `1 2 3 5 7 8`. The expected answer is `{2, 3}` and `{5}`: both sum to `5`, and they are disjoint. I have not traced the meet-in-the-middle steps on this input.

## Pitfalls

- Assuming overlapping subsets can be printed. They must be disjoint, so the intersection has to be removed.
- Forgetting that the removed intersection can make a subset empty. The argument above shows this cannot happen when the sums are equal and positive.
- Printing `IMPOSSIBLE`. Under the constraint a solution always exists.

## Complexity

- Existence: no computation needed.
- Construction (outline): about `O(2^(n/2) * n)` enumeration with NTT for the residue counts. Not verified.

## Key takeaway

The pigeonhole argument guarantees two disjoint subsets with equal sums exist. Finding them is a meet-in-the-middle search, and I have not verified the construction.
